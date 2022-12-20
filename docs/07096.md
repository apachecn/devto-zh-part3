# docker——Python 的首次开发

> 原文：<https://dev.to/jeremywmoore/docker-first-python-development-987>

我一直对避免测试代码和 Python 应用程序的依赖关系有多困难感到有点恼火。典型的构建过程可能类似于:

1.  创建虚拟环境
2.  安装服务依赖项
3.  安装测试依赖项
4.  运行测试
5.  将代码和依赖项打包到 RPM 中。

此时，我的服务依赖和测试依赖混合在虚拟环境中。为了解开它们，我现在必须做一些事情，比如销毁 venv 并创建一个新的，重新安装服务依赖项。无论采用何种打包方法，我都不想在部署服务时破坏依赖关系。

在 Twilio，我们正在采用基于容器的部署。Docker 容器非常适合 python 服务，因为您不再需要担心多个 Python 版本或虚拟环境。您只需使用一个带有您的服务所需要的 Python 版本的映像，并将您的依赖项直接安装到系统中。

我注意到的一件事是，虽然许多服务是作为 Docker 映像构建和打包的，但很少使用专门基于 Docker 的开发环境。虚拟环境和 pyenv `.python-version`文件比比皆是！

我最近开始编写一个新的 Python 服务，因为我知道这个服务只能通过容器来部署。这感觉像是一个正确的机会，可以在容器上全押，为 Docker-first localdev 建立一个策略。我带着以下目标出发:

1.  不要将测试和测试依赖关系与最终映像一起发布
2.  测试作为 Docker 构建的一部分运行
3.  失败的测试将导致构建失败
4.  IDE (PyCharm)集成

一些研究(又名谷歌搜索)表明，多阶段构建在这一努力中可能是有用的。最终，我得到了一个类似这样的 docker 文件:

```
FROM python3 as builder

COPY requirements.txt ./

RUN pip install -r requirements.txt

COPY src ./src

FROM builder as tests

COPY test_requirements.txt ./

RUN pip install -r test_requirements.txt

COPY tests ./tests

RUN pytest tests

FROM builder as service

COPY docker-entrypoint.sh ./

ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 3000 
```

当从这个 docker 文件构建映像时，Docker 将构建 3 个映像，每个映像对应 Docker 文件中的一个`FROM`语句。如果您以前使用过 Dockerfiles，您会知道语句排序对于有效利用层缓存至关重要，多阶段构建也不例外。Docker 按照定义的顺序构建每个图像。所有的中间阶段都是短暂的，构建过程只输出最后一个图像。

在这种情况下，第一阶段(`builder`)用所有的服务依赖项和代码构建一个映像。第二阶段(`tests`)安装测试需求和测试代码，并运行测试。如果测试通过，构建过程将继续到下一个阶段。如果测试失败，整个构建都会失败。这确保了只构建通过测试的映像！最后，最后一个阶段(`service`)构建在我们的`builder`映像之上，添加 entrypoint 脚本，定义 entrypoint 命令并公开端口 3000。

那么，我是如何实现最初的目标的呢？

1.  不要将测试和测试依赖关系与最终映像一起发布
2.  测试作为 Docker 构建的一部分运行
3.  失败的测试将导致构建失败
4.  IDE (PyCharm)集成❌

我已经达到了大部分目标，但是实际的开发体验如何呢？如果我打开 PyCharm 并导入我的源代码，它会抱怨我有未满足的依赖项:(幸运的是，PyCharm Professional 能够从 Docker 映像中选择 python 解释器！酷，但我必须先建立图像，然后才能使用它的解释器。但是由于目标 3，如果我的测试失败了，我就不能建立我的形象...

幸运的是，我们可以明确地告诉`docker build`构建一个中间阶段，在期望的阶段之后停止构建。现在，如果我运行`docker build --target builder -t builder .`，我可以从`builder`图像中选择解释器。

啊哦！构建器映像不包括我的测试依赖项！当然，这就是构建者图像的全部意义。让我们添加另一个阶段来运行和调试我们的测试。

```
FROM python3 as builder

COPY requirements.txt ./

RUN pip install -r requirements.txt

COPY src ./src

FROM builder as localdev

COPY test_requirements.txt ./

RUN pip install -r test_requirements.txt

COPY tests ./tests

ENTRYPOINT ["pytest"]

CMD ["tests"]

FROM localdev as tests

RUN pytest tests

FROM builder as service

COPY docker-entrypoint.sh ./

ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 3000 
```

在`localdev`阶段，我可以用我所有的服务和测试代码以及依赖项进行构建和映像。我甚至可以让 localdev 容器在运行时默认运行测试。通过使用这个映像中的解释器，我现在可以调试失败的测试了。

让我们再来看看最初的目标:

1.  不要将测试和测试依赖关系与最终映像一起发布
2.  测试作为 Docker 构建的一部分运行
3.  失败的测试将导致构建失败
4.  ide(pychar)的集成

万岁！

除了还有一件事困扰着我:对服务代码的更改会触发我们测试依赖项的重新安装。呸！让我们再来看看我们的 docker 文件:

```
FROM python3 as service_deps

COPY requirements.txt ./

RUN pip install -r requirements.txt

FROM deps as test_deps

COPY test_requirements.txt ./

RUN pip install -r test_requirements.txt

FROM deps as builder

COPY src ./src

FROM test_deps as tests_builder

COPY src ./src

COPY tests ./tests

FROM tests_builder as localdev

ENTRYPOINT ["pytest"]

CMD ["tests"]

FROM tests_builder as tests

RUN pytest tests

FROM builder as service

COPY docker-entrypoint.sh ./

ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 3000 
```

这看起来很复杂，这是我们的图像拓扑图:

```
 service_deps                    
    |   \                        
    |    -\                      
    |      \                     
    |       -test_deps           
    |            |               
    |            |               
 builder     tests_builder       
    |            |  -\           
    |            |    -\         
    |        localdev   -\       
    |                     --tests
 service 
```

我不喜欢`builder`和`tests_builder`阶段都复制源目录，但真正的问题是，这是否仍然满足我们的最初目标，同时避免过度重新安装测试依赖项？是的，看起来效果不错。多亏了 Docker 的层缓存，我们很少需要重新安装依赖项。

就这样。如果您有任何问题或建议，请告诉我！
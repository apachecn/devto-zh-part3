# Google Colab 笔记本如何利用免费的 12GB 内存 GPU 的 8 个技巧。

> 原文：<https://dev.to/kriyeng/8-tips-for-google-colab-notebooks-to-take-advantage-of-their-free-of-charge-12gb-ram-gpu-be4>

[![Title](img/7365724057825a3b7ed752bee0a1e978.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4bGTM_6f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/guw5bt6tjx438fkse6yf.jpg)

Colab 是基于 Jupyter 笔记本的免费云服务，用于机器学习教育和研究。它提供了一个完全配置用于深度学习的运行时，以及对强大 GPU 的免费访问。

这 8 个技巧是两个星期来使用 Colab 训练 YOLO 模型的结果。由于工具的一些不便，我正要放弃。但是，在第二次机会下，经过一番努力和调整，我发现了一个很棒的工具，免费提供给每个人。

使用 Colab 笔记本电脑，你首先会注意到的是处理每 12 小时就会爆炸一次的运行时的障碍！这就是为什么加快再次运行运行时所需的时间如此重要。这些提示让我可以在更少的时间和更少的人工干预下重新开始训练模型。在训练过程中，我将新训练的重量自动保存在我的本地电脑中

这些技巧是基于使用 Darknet 训练 YOLO 的，但是我将试着概括一下。

我们来看看吧！

> 我在**这里进入重点模式**，但是你可以找到[一步一步教程](http://blog.ibanyez.info/blogs/coding/20190410-run-a-google-colab-notebook-to-train-yolov3-using-darknet-in/)，可运行的 [Colab 笔记本](https://colab.research.google.com/drive/1lTGZsfMaGUpBG4inDIQwIJVW476ibXk_#scrollTo=Cqo1gtPX6BXO)或者 [github 回购](https://github.com/kriyeng/yolo-on-colab-notebook)

## 1。映射您的 Google Drive

在 Colab 笔记本上，您可以在 Colab VM 运行时将 Google Drive 作为网络映射驱动器进行访问。

```
# This cell imports the drive library and mounts your Google Drive as a VM local drive.  
# You can access to your Drive files using this path "/content/gdrive/My Drive/" 
from google.colab import drive
drive.mount('/content/gdrive') 
```

[![schema drive.jpg](img/8a6a91de09ecc61a7ddf81a06921be6e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--krVkVkQu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.ibanyez.info/download/B20190408T000000060.jpg)

## 2。在计算机中透明地处理文件

您可以同步电脑中的 Google Drive 文件夹。与前面的技巧一样，您的本地文件将在您的 Colab 笔记本中本地可用。

[![Sync Drive.jpg](img/30fbfdf02625926dad1e2b19b47c8dd4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1Cwymuxd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.ibanyez.info/download/B20190408T000000063.jpg)

我将使用名为`darknet`的 Google Drive 文件夹。这是我将在本地计算机中保持同步的文件夹。

## 3。减少每次运行时的手动交互

在我的情况下，我每次都需要从 Nvidia 下载 cuDNN。此库需要登录才能下载。我的方法是将 tar 文件一次性保存在我的计算机中。每次运行时，我都直接在需要 lib 文件的地方提取文件。无需手动上传。你可以把它应用到你需要安装的任何库上。

```
# Extracts the cuDNN files from Drive folder directly to the VM CUDA folders !tar -xzvf gdrive/My\ Drive/darknet/cuDNN/cudnn-10.0-linux-x64-v7.5.0.56.tgz -C /usr/local/
!chmod a+r /usr/local/cuda/include/cudnn.h

# Now we check the version we already installed. Can comment this line on future runs !cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2 
```

## 4。不要每次都编译库，只编译一次。

因为我使用的是 Darknet，所以我需要在每次运行时克隆存储库并编译框架。每次运行都需要一些时间。我的解决方案是仅在第一次运行笔记本时编译源代码，并在我的驱动器文件夹中保存一份可执行文件的副本。在以后的运行中，我只需要将 bin 文件复制到必要的文件夹中。

**第一次运行的代码**

```
# Leave this code uncommented on the very first run of your notebook
# or if you ever need to recompile darknet again.
# Comment this code on the future runs. !git clone https://github.com/kriyeng/darknet/
%cd darknet

# Check the folder !ls

# I have a branch where I have done the changes commented below !git checkout feature/google-colab

#Compile Darknet !make

#Copies the Darknet compiled version to Google drive !cp ./darknet /content/gdrive/My\ Drive/darknet/bin/darknet 
```

> **如果你对使用暗网感兴趣:**我用的是暗网的修改版。我需要对原始源代码进行一些修改。我这里的方法是直接从驱动器使用配置文件。原始暗网不允许在配置文件`obj.data`中的文件路径上有空格。我添加了接受转义空格的特性:**(...)/gr dive/My \ Drive/darknet**
> 我减少了屏幕上打印的日志行数。原始源在屏幕上打印更多行，Colab 笔记本可以与您的浏览器同步。

**第一次运行后要执行的代码**

```
# Uncomment after the first run, when you have a copy of compiled darkent in your Google Drive 
# Makes a dir for darknet and move there !mkdir darknet
%cd darknet

# Copy the Darkent compiled version to the VM local drive !cp /content/gdrive/My\ Drive/darknet/bin/darknet ./darknet

# Set execution permissions to Darknet !chmod +x ./darknet 
```

## 5。清理 Google Drive 上的根文件夹

Google 的一些资源解释了在你的根文件夹中有很多文件会影响映射单元的过程。如果您在驱动器上的根文件夹中有许多文件，请创建一个新文件夹并将它们全部移动到那里。我认为根文件夹中有很多文件夹也会有类似的影响。

## 6。将您的数据集复制到虚拟机本地文件系统，以提高训练速度。

Colab 笔记本有时在处理驱动器文件时会有一些延迟。我发现将数据集复制到 VM 文件系统可以提高速度。这导致每次运行时，您都需要花费一些时间在本地复制您的数据集文件，但是这会对训练时间产生相当大的影响。根据数据集大小和结果，您可以尝试两种方法。

**第一次接近。将您的数据从驱动器复制到虚拟机驱动器**

```
# Copy files from Google Drive to the VM local filesystem !cp -r "/content/gdrive/My Drive/darknet/img" ./img 
```

也许还有另一个选择，就是把你的数据集压缩到你的硬盘里。然后复制 tar 文件，解压到笔记本本地。我没有测试过这个。

**第二种方法。上传你的数据集到 git repo，然后从那里下载**

```
# Git clone directly to ./img folder !git clone https://[your-repository] ./img

# Check the result - Uncomment when you checked for speed up further runs !ls -la ./img 
```

## 7。直接从您的计算机使用配置文件。

把所有的配置文件保存在你的电脑上可以让事情变得更容易和方便。您可以使用您最喜欢的编辑器在本地修改任何配置，这些配置将会自动同步，并且可以从您的笔记本上访问。

这就是我如何执行暗网开始训练。

```
!./darknet detector train "/content/gdrive/My Drive/darknet/obj.data" "/content/gdrive/My Drive/darknet/yolov3.cfg" "/content/gdrive/My Drive/darknet/darknet53.conv.74" -dont_show 
```

这是我的`obj.data`文件:

```
classes=1
train = /content/gdrive/My\ Drive/darknet/train.txt
test = /content/gdrive/My\ Drive/darknet/test.txt
names = /content/gdrive/My\ Drive/darknet/obj.names
backup = /content/gdrive/My\ Drive/darknet/backup 
```

## 8。在训练过程中，将训练后的重量直接实时同步到电脑中

正如你在上面几行中看到的，我正在将备份文件夹设置到我的 Google Drive。因为我的硬盘与我的电脑同步，所以我可以在笔记本电脑训练时实时接收训练后的重量。这有一些优点:

*   在模型训练时，您可以使用测试数据集在您的计算机中检查它的本地运行情况。
*   当运行时间在 12 小时后关闭时，您会将最后一次训练的重量保存在电脑中
*   您可以再次运行笔记本，并使用以下代码从上次会议的位置开始培训:

```
# Start training at the point where the last runtime finished !./darknet detector train "/content/gdrive/My Drive/darknet/obj.data" "/content/gdrive/My Drive/darknet/yolov3.cfg" "/content/gdrive/My Drive/darknet/backup/yolov3_last.weights" -dont_show 
```

你可以在这里找到一个步骤教程或者可运行的 Colab 笔记本👇

*   更多深度讲解在[循序渐进教程](http://blog.ibanyez.info/blogs/coding/20190410-run-a-google-colab-notebook-to-train-yolov3-using-darknet-in/)
*   在 Colab 为自己运行: [Colab notebook 使用 Darknet 训练 YOLO 的技巧&把 Colab notebook 变成有用工具的技巧](https://colab.research.google.com/drive/1lTGZsfMaGUpBG4inDIQwIJVW476ibXk_)
*   你可以在 [Github](https://github.com/kriyeng/yolo-on-colab-notebook/) 找到笔记本

感谢阅读！我希望你喜欢！我鼓励您给我发送一些反馈和建议。在此开启讨论！

快乐的物体探测编程！
[![cyclist-3659933_1920-yolo.jpg](img/55e714aab12b881f3fe78596ec714cda.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--7ybVTOrI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.ibanyez.info/download/B20190410T000000073.jpg)

### 来源

*   YOLO 原创网站[约瑟夫·雷德蒙页面](https://pjreddie.com/darknet/yolo/)
*   AlexeyAB darknet repo[github](https://github.com/AlexeyAB/darknet/)
*   伊凡·冈查罗夫[笔记本](https://github.com/ivangrov/YOLOv3-GoogleColab/blob/master/YOLOv3_GoogleColab.ipynb)
*   [Google co laboratory 作为加速深度学习应用工具的性能分析](https://ieeexplore.ieee.org/document/8485684)
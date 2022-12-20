# 在亚马逊 SageMaker 上充分利用你的机器学习预算

> 原文：<https://dev.to/aws/making-the-most-of-your-machine-learning-budget-on-amazon-sagemaker-349d>

在过去 10 年左右的时间里，开发人员在 AWS 上运行和扩展各种工作负载方面取得了令人难以置信的成功。最重要的是，他们学会了最大限度地利用现收现付模式，确保他们不会多花一分钱。

<figure>[![](img/69ccc1214442e8fde1b4632f534ee0fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PRKol4_i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ABT7iMcf22tWoEyuX.jpg) 

<figcaption>“我们已经把你所有的预算都花在 GPU 上了”是什么意思？</figcaption>

</figure>

抱着机器学习(ML)与其他任何东西(起诉我)没有什么不同的坚定信念，这篇文章将介绍一些成本优化技术，其中大部分是针对[亚马逊 SageMaker](https://aws.amazon.com/sagemaker) ，我们受欢迎的完全管理的 ML 服务。这些技术应该帮助你**避免反模式，并保持你的账单绝对最小**。你是把钱花在发酵饮料上还是花在额外的概念验证预算上，完全取决于你自己；)

### 数据准备

每个 ML 项目都需要一个数据集，有时你必须从头开始构建它。这通常意味着清理和标记大量数据，这是一项耗时(且成本高昂)的任务。

您可以通过以下方式省钱:

1.  抵制在基于实例的服务(如 EC2 或 EMR)上构建特定 ETL 工具的冲动。 AWS 拥有精选的**全托管服务**，可在编码和基础设施管理方面为您节省时间和金钱。我建议看看[亚马逊雅典娜](http://aws.amazon.com/athena)(SQL 中的分析)[亚马逊胶水](https://aws.amazon.com/glue)(基于 Spark 的 ETL)以及[亚马逊湖形成](https://aws.amazon.com/lake-formation/)(数据湖)。后者仍处于预览阶段(请随意[加入](https://pages.awscloud.com/lake-formation-preview.html))，它具有非常有前途的 **ML 特性**(又名 *ML 转换*)，可以自动链接或删除数据:更多信息请参见本次 [re:Invent 2018 session](https://www.youtube.com/watch?v=nsiLMqg654s) 。
2.  **使用亚马逊 SageMaker Ground Truth 和主动学习来削减数据标注成本。** [地面真相](https://aws.amazon.com/sagemaker/groundtruth/)是[在 re:Invent 2018 推出的](https://aws.amazon.com/blogs/aws/amazon-sagemaker-ground-truth-build-highly-accurate-datasets-and-reduce-labeling-costs-by-up-to-70/)新服务。它不仅提供了**直观的工具**来标记文本、图像或自定义数据集，它还支持一种叫做[主动学习](https://docs.aws.amazon.com/sagemaker/latest/dg/sms-automated-labeling.html)的**自动标记技术**。简而言之，主动学习使用手动标记的数据来训练 ML 模型，进而能够标记数据。这可以减少多达 **70%** 的人工标记:不仅您的数据可以更快地被标记，您还可以节省大量人力资源的时间和金钱。

### 实验

一旦有了数据集，就该开始探索和试验了。Jupyter 笔记本是一种流行的方式，这就是为什么亚马逊 SageMaker 提供完全托管的[笔记本实例](https://docs.aws.amazon.com/sagemaker/latest/dg/nbi.html)，预装了您需要的大多数工具。

您可以通过以下方式省钱:

1.  **不使用笔记本实例！**你不必使用它们来与 Amazon SageMaker 一起工作:你完全可以在一台**本地机器**(或一台本地服务器)上开始试验，然后使用 [SageMaker SDK](https://github.com/aws/sagemaker-python-sdk) 来启动 AWS 上的培训和部署。
2.  **停止不需要的笔记本实例**。这听起来是显而易见的，但是你真的这么做了吗？实际上**没有理由让笔记本实例运行**:提交你的工作，[停止](https://docs.aws.amazon.com/sagemaker/latest/dg/API_StopNotebookInstance.html)它们，当你再次需要它们的时候[重启](https://docs.aws.amazon.com/sagemaker/latest/dg/API_StartNotebookInstance.html)它们。存储是持久的，您还可以使用[生命周期配置](https://docs.aws.amazon.com/sagemaker/latest/dg/notebook-lifecycle-config.html)来自动化包安装或存储库同步。
3.  **小规模试验和合适的尺寸**。你真的需要完整的数据集来开始可视化数据和评估算法吗？大概不会。通过处理数据集的一小部分**，你将能够使用**更小的** [**笔记本实例**](https://aws.amazon.com/sagemaker/pricing/) 。这里有一个例子:想象 5 个开发人员在他们自己的笔记本实例上每天工作 10 个小时。用 *ml.t3.xlarge* ，每天的花费就是 5 * 10 * 0.233 美元= 11.65 美元。用 *ml.c5.2xlarge* (更大的 oomph 和更多的 RAM 来支持大型数据集):5*10*$0.476=$23.68。两倍的成本。您每月可以节省 476 美元(这是一大笔啤酒/PoC 钱)。**
***   **使用本地模式**。当试验 SageMaker SDK 时，您可以使用[本地模式](https://github.com/aws/sagemaker-python-sdk)在笔记本实例本身上训练和部署您的模型**，而不是在托管实例上。这样做有双重好处，既可以在实验和生产中使用相同的代码，又可以通过不触发实例来节省时间和金钱。它只需要使用' *local* '实例类型:这在所有深度学习环境中都受支持，这里有一个 [TensorFlow 示例](https://github.com/awslabs/amazon-sagemaker-examples/blob/master/sagemaker-python-sdk/tensorflow_distributed_mnist/tensorflow_local_mode_mnist.ipynb)。****

 **### 训练

一旦你找到了一个在小规模数据集上运行良好的算法，你就会想在整个数据集上运行它。这可能会持续一段时间，所以请明智地消费！

您可以通过以下方式省钱:

1.  **不在笔记本实例上运行长时间的作业**。不幸的是，这种反模式似乎很常见。人们选择一个大的笔记本实例(比如说， *ml.p3.2xlarge* ，每小时 4.284 美元)，启动一个大的任务，让它运行，忘记它，最终在任务完成后花费几个小时什么都不做！相反，请**在托管实例**上运行您的培训工作:由于分布式培训，您将更快地获得结果，并且由于培训一完成实例就终止，**您将永远不会为培训支付过多的费用**！作为一个额外的好处，你不会在半夜被清除脚本(或过分热心的管理员)杀死所有笔记本实例(“因为它们什么也没做，对吗？”).
2.  **调整培训实例的规模**。通常情况下，**简单模型不会简单地在大型实例上训练得更快**，因为它们不会从增加的硬件并行性中受益。他们甚至可能对 GPU 通信开销训练得更慢！就像在 EC2 上一样，从小实例开始，先向外扩展，然后再向上扩展。是的，玩 *ml.p3.16xlarge* 很有趣，但它们每小时的价格是 34.272 美元，所以别傻了:)
3.  **使用 AWS 提供的 TensorFlow、Apache MXNet 等版本。**我们有整个团队致力于从 AWS 上的深度学习库中提取**最后一点性能**(阅读[这个](https://aws.amazon.com/blogs/machine-learning/faster-training-with-optimized-tensorflow-1-6-on-amazon-ec2-c5-and-p3-instances/) [、](https://aws.amazon.com/about-aws/whats-new/2018/10/chainer4-4_theano_1-0-2_launch_deep_learning_ami/) [这个](https://aws.amazon.com/about-aws/whats-new/2018/10/chainer4-4_theano_1-0-2_launch_deep_learning_ami/)，以及[这个](https://aws.amazon.com/about-aws/whats-new/2018/11/tensorflow-scalability-to-256-gpus/))。没有冒犯的意思，但是如果你认为你可以' *pip 安装*'并运行得更快，你的时间可能会被投资到其他地方:)
4.  **将数据集打包到 RecordIO / TFRecord 文件中**。由数千甚至数百万个文件组成的大型数据集会导致大量不必要的 IO，这会降低您的培训作业的速度。将这些文件打包成大型记录结构文件(比如每个 100MB)将使**更容易移动**你的数据集，**更容易将它分发给**训练实例。TensorFlow 请使用 [TFRecord](https://www.tensorflow.org/guide/datasets#consuming_tfrecord_data) ，Apache MXNet 和大多数内置算法请使用 [RecordIO](https://mxnet.incubator.apache.org/versions/master/faq/recordio.html) 。
5.  **使用管道模式**流式传输大型数据集。[管道模式](https://docs.aws.amazon.com/sagemaker/latest/dg/cdf-training.html)将你的数据集直接从亚马逊 S3 传输到你的训练实例。**不涉及复制**，这节省了启动时间，也让你可以处理**无限大的数据集**(因为它们不再完全加载到 RAM 中)。大多数内置算法和 TensorFlow 都支持管道模式。

### 优化

为 ML 模型计算出正确的超参数集可能相当昂贵，因为像网格搜索或随机搜索这样的技术通常涉及训练数百个不同的模型。

通过使用 [**自动模型调整**](https://docs.aws.amazon.com/sagemaker/latest/dg/automatic-model-tuning.html) ，可以节省(大量)资金，这是一种 ML 技术，它可以**快速**和**高效**用有限数量的训练工作(想想几十个，而不是几百个)计算出最优的参数集。它适用于所有算法:内置、深度学习和自定义。我怎么推荐都不为过。

### 预测

既然您对模型的性能感到满意，那么是时候将它部署到服务于 HTTPS 预测的 Amazon SageMaker [端点](https://docs.aws.amazon.com/sagemaker/latest/dg/how-it-works-hosting.html)了。ML 流程的这一部分实际上是你花费**最多**的地方，因为你的模型可能需要 24/7 可用**。事情可能会很快变糟，尤其是在大规模的情况下。**

您可以通过以下方式省钱:

1.  **删除不必要的端点**。这是一个显而易见的问题，但我们可能都因忽视它而感到内疚。用 SageMaker 部署模型非常容易(尤其是在实验的时候)，同样容易忘记清理。**请记住端点必须明确删除**。您可以使用 SageMaker SDK 或在控制台中轻松完成。
2.  **正确调整和自动扩展您的端点**。就像训练一样，您需要找出哪种实例类型最适合您的应用程序:延迟、吞吐量和成本。我再次建议**从小规模开始，先向外扩展，然后再向上扩展**。端点支持 [**自动伸缩**](https://docs.aws.amazon.com/sagemaker/latest/dg/endpoint-auto-scaling.html) ，因此当您可以运行一个动态的小型实例群时，没有理由运行一个未被充分利用的大型实例。
3.  **如果不需要在线预测，使用批量转换**。一些应用程序不需要一个活动的端点，定期运行[批量预测](https://docs.aws.amazon.com/sagemaker/latest/dg/how-it-works-batch.html)就可以了。这是由 Amazon SageMaker 支持的，额外的好处是，当批处理完成后，底层实例会自动终止，这意味着**您永远不会为预测**多付钱，因为您无缘无故地让那个端点运行了一周…
4.  **使用弹性推理代替成熟的 GPU 实例**。[弹性推理](https://aws.amazon.com/fr/machine-learning/elastic-inference/)是[在 re:Invent 2018 上推出的一项新服务](https://aws.amazon.com/blogs/aws/amazon-elastic-inference-gpu-powered-deep-learning-inference-acceleration/)，它可以让你将**分数 GPU 加速**附加到任何亚马逊实例(EC2、Sagemaker 笔记本实例和端点)。例如，一个配置了 *eia1.medium* 加速的 *c5.large* 实例每小时将花费你 0.22 美元。这种组合只比 p2.xlarge 实例慢 10-15%，p2 . xlarge 实例托管一个专用的 NVIDIA K80 GPU，每小时收费 0.90 美元。底线:在同等 GPU 性能的情况下，成本降低了 75%。在这种情况下，每个实例每月可以节省 490 美元……**在部署到 GPU 实例之前，请首先测试弹性推理**:这可能值得您花费更多时间。
5.  **针对底层硬件优化您的 ML 模型**。[亚马逊 SageMaker Neo](https://aws.amazon.com/sagemaker/neo/) 是[在 re:Invent 2018 上推出的另一项服务](https://aws.amazon.com/blogs/aws/amazon-sagemaker-neo-train-your-machine-learning-models-once-run-them-anywhere/)。简而言之，Neo 编译器将模型转换成一种**高效的通用格式**，这种格式由一个紧凑的运行时在设备上执行，它使用的资源不到一般框架传统消耗的百分之一。Neo 运行时针对底层硬件进行了**优化，使用特定的指令集来帮助加速 ML 推理。这怎么能帮你省钱呢？嗯，由于您的模型现在运行得相当快，因此有理由期望您可以在保持可接受的延迟和吞吐量水平的同时缩小预测实例的规模。最精彩的部分？编译一个模型只需要一个 API 调用，而且是免费的。**
6.  **使用推理管道代替多个端点**。一些模型要求对预测进行预处理和/或后处理。直到最近，这还意味着部署多个端点(每个步骤一个)，导致额外的成本和额外的延迟。有了[推理管道](https://docs.aws.amazon.com/sagemaker/latest/dg/inference-pipelines.html)，SageMaker 现在能够**将所有步骤部署到同一个端点**，节省了资金和延迟。

### 结论

如你所见，实施所有这些技巧和最佳实践可以很好地**削减你的 ML 预算一个数量级**。请给他们一个尝试，让我知道你节省了多少:)我也很想听听你想出的其他技术。请在这里随意分享。

一如既往的感谢您的阅读。很高兴在这里或在 [Twitter](https://twitter.com/julsimon) 上回答问题。**
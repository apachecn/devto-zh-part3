# 亚马逊 EC2 c5n 和 p3dn:用于深度学习的更多网络带宽

> 原文：<https://dev.to/juliensimon/amazon-ec2-c5n-and-p3dn-more-network-bandwidth-for-deep-learning-3kko>

### AWS re:Invent —亚马逊 EC2 c5n 和 p3dn:更多网络带宽用于深度学习

训练深度学习模型不仅仅是一项计算密集型任务:还需要大量的 I/O。我们来看看为什么。

[![](img/26e827cffbe42329d152979d17fe434a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oxe7gbV_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/852/1%2ANhigyAYDlXCVGXACdlTa7Q.jpeg)

#### 加载训练和推理数据

大型数据集通常存储在网络存储上，如亚马逊 S3。因此，在训练过程中，数据需要从网络存储加载到实例 RAM。这一数据加载过程需要尽可能快速稳定地进行，以保持 CPU 和 GPU 忙碌。由于它们的速度惊人，加载数据时的任何延迟或意外延迟都可能使它们停滞不前，浪费宝贵的培训时间。

I/O 速度和延迟对推理性能也至关重要。尽管许多应用程序一次预测一个样本，但是如果 I/O 不是一直很快，总吞吐量可能会受到影响。

#### 分布式训练时交换信息

训练深度学习模型的目的是逐渐发现该模型的最优权重集(又名参数)，即最小化特定度量(通常是验证误差)的权重集。

这包括运行一个[优化函数](https://medium.com/@julsimon/tumbling-down-the-sgd-rabbit-hole-part-1-740fa402f0d7) (SGD 或它的许多变体之一)来计算梯度，它反映了地面真实和预测之间的差异。当在分布式节点集群上训练时，每个节点接收一批数据，通过模型转发该批数据，并计算该批数据的梯度。然后，每个节点将梯度推送到主服务器，在那里来自所有节点的结果被合并。在处理新的批处理之前，节点首先获取最新的结果，这保证了所有节点共享相同的状态。

> 这是一个一般的描述，这种行为有细微差别。如果你对细节感兴趣，你可以在 [Apache MXNet](https://mxnet.incubator.apache.org/faq/distributed_training.html) 和 [TensorFlow](https://www.tensorflow.org/deploy/distributed) 中了解分布式培训是如何工作的。

大型模型的渐变可能非常大:Resnet-50 的渐变为 97MB。每个节点都必须一次又一次地推拉大量数据。这给网络带宽带来很大压力，并可能成为严重的性能瓶颈。已经设计了许多技术来压缩和量化梯度，它们有助于减少需要交换的数据量[1，2]。尽管如此，网络性能仍然是加速大型分布式培训工作的一个非常重要的因素。

#### c5n 和 p3dn:亚马逊 EC2 上的 100 Gbit 联网

新宣布的 [c5n](https://aws.amazon.com/blogs/aws/new-c5n-instances-with-100-gbps-networking/) 和 [p3dn](https://aws.amazon.com/ec2/instance-types/p3/) 实例支持 100 Gbit 网络，为 HPC 和深度学习等要求苛刻的应用带来低延迟和高吞吐量。给他们一个尝试！

乐意回答任何问题！请在 [Twitter](https://twitter.com/julsimon) 上关注我，了解类似的新闻和内容。

[1]“[深度梯度压缩:降低分布式训练的通信带宽](https://arxiv.org/abs/1712.01887)”，，宋瀚，，毛，，戴利，2017

[2] " [渐变压缩](https://mxnet.incubator.apache.org/faq/gradient_compression.html)，Apache MXNet。
## 计算机视觉
* [CNN Classification](#1-word2vec)

### 1. CNN Classification

* **算法说明**  
  Tesla基于Tensorflow实现支持用户自定义网络结构的CNN图片分类模型，用户将模型结构以Json格式的保存至配置文件中，即可迅速构建出自己的CNN、NIN（Network in Network）及FCN（Fully Connected Work）模型。但用户还是可以依此构建NIN（Network in Network）和全卷积网络FCN（Fully Connected Work）模型。

* **配置文件**
  在tesla中我们通过上传一份简单的json配置文件即可完成网络定义的工作。配置文件如下所示：

  ```json
        {
        "layer1":{"operation":"conv", "maps":64, "kernel_size":5, "stride":1, "padding":"SAME", "activation_func":"relu"},
        "layer2":{"operation":"max_pool", "kernel_size":3, "stride":2, "padding":"SAME"},
        "layer3":{"operation":"conv", "maps":64, "kernel_size":5, "stride":1, "padding":"SAME", "activation_func":"relu"},
        "layer4":{operation":"max_pool", "kernel_size":3, "stride":2, "padding":"SAME"},
        "layer5":{"operation":"fc","maps":384, "dropout_rate":1.0, "activation_func":"relu"},
        "layer6":{"operation":"fc","maps":192, "dropout_rate":1.0, "activation_func":"relu"},
        "layer7":{"operation":"fc","maps":10},
        "initial_image_size":32,
        "input_image_size":24,
        "input_channel":3
        }
  ```

  实例中所定义的网络，可适用于cifar10的分类任务。详解如下：  
    - __layer _x___：层标识，代表一个新层。  
      - __operation__：本层类型，包括卷积(conv)，池化（max_pool/ave_pool）,全连接（fc）三种。  
      - __maps、kernel、stride、padding等__：本层参数信息，根据层类型各有不同。
    - __initial_image_size__：存储在二进制文件中的图片数据的大小。
    - __input_image_size__：输入到网络中的图片数据的大小，在此之前图片可能经过了resize或crop等处理（目前默认的是resize），使得网络输入图片大小与存储在二进制文件中的图片大小有所不同。  
    - __input_channel__：指输入图片的通道大小。


* **训练节点**
  - 数据形式
    - 数据形式：[Images](../deeplearning/dl_dataformat.md)，以二进制形式存储。   
  - 算法IO参数
    - 训练数据：训练数据集过大被分成多份时，可选择先训练前几个文件中的数据做简单实验。
    - 配置文件：网络结构配置文件路径，必须为ceph文件系统上的路径名。
    - 模型输出：模型输出路径，也就是checkpoint路径，必须为ceph文件系统上的路径名。
    - 可视化输出：可视化信息输出路径，必须为ceph文件系统上的路径名。
  - 算法参数
    - 数据输入：训练和测试数据输入路径，必须为ceph文件系统上的路径名。
    - 批量训练大小：图片批量训练大小，也就是batch size
    - 训练步数：总共训练步数，即模型训练多少步即停止
    - 初始学习率：初始学习率，随着迭代的进行，会逐渐减小
    - 学习率衰减步数：每训练多少步衰减一次学习率
    - 学习率衰减因子：学习率衰减因子：0.1
    - 测试图片数目：测试图片数目，即测试集总共有多少张测试图片
    - 测试间隔（步）：每训练多少步做一次测试（方便用户根据测试结果提前终止实验）

# Singularity容器

[Singularity][1] 是一种针对高性能计算而设计的的 Linux 容器，它完美支持了并行计算、深度学习等科学计算技术，被广泛部署在全球各大高性能计算中心，包括斯坦福、哈佛、耶鲁、普林斯顿等大学的高性能计算中心均已采用这项技术。

和 Docker 极其相似，Singularity 是一种容器技术。容器将用户所需的操作系统和应用软件打包到一个“镜像”中。当用户需要执行计算作业时，可以启动这个打包好的镜像，镜像以类似[虚拟机][2]的方式运行在物理机器上。例如，深度学习框架 TensorFlow 的用户可以在一台CentOS上启动一台基于Ubuntu的容器，且容器中已经安装好指定版本的 TensorFlow。但不同于传统的虚拟机，容器启动速度极快。相比在物理机上运行程序而言，在容器上运行程序的性能损失几乎可忽略不计。

Singularity 允许用户间共享镜像，如果你的工作需要和其他研究人员合作，或者你的工作经常在不同的计算机环境上切换，那 Singularity 将大大提高你的工作效率。

## Singularity 教程

Singularity 的使用主要分为创建或下载镜像和使用镜像运行计算作业。我们安装的版本为2.6.1，用户可以参考官方提供的[在线文档][3]。

## 下载一个 pre-built 镜像

Singularity 兼容 docker 镜像，用户可以直接在 dockerhub 上别人已经安装好的镜像。这里直接使用一款已经安装好所有深度学习框架的 docker 镜像：

```bash
singularity build all-py36-jupyter-cpu docker://ufoym/deepo:all-py36-jupyter-cpu
```

!!! warning "镜像存放位置"
    由于镜像文件比较大，请将所需要的镜像放在 `/mnt/data` 下。通用的几个镜像已经放置在了 `/mnt/data/container_library` 下。用户自己创建的镜像也请放置在自己的相关目录下。

## 使用镜像运行作业

### shell 交互模式

```bash
/usr/local/bin/singularity shell /mnt/data/container_library/deep_learning/tensorflow-10.0-py3.simg
Singularity: Invoking an interactive shell within container...

python3 -c 'import tensorflow as tf; print(tf.__version__)'
1.10.0
```

singularity启动了一个镜像，用户进入这个镜像，以命令行的方式与系统做交互。

### exec 执行命令

在宿主机器上使用 `exec` 命令，启动镜像：

```bash
/usr/local/bin/singularity exec /mnt/data/container_library/deep_learning/tensorflow-10.0-py3.simg python3 -c 'import tensorflow as tf; print(tf.__version__)'
```

结果直接返回到宿主机器命令行上。

!!! warning "注意！"
    请不要在登录节点运行singularity镜像并在镜像中运行作业任务！登录节点连接了Internet，可以拉取dockerhub上的pre-built镜像。计算节点不可连接外网，无法直接拉取外网镜像。可以从登录节点ssh到某台计算节点上启动镜像并运行作业任务。

## 在调度系统中使用singularity启动作业

这里以 TensorFlow 提供的官方tutorials中[使用卷积网络对mnist数据进行分类的代码 convolutional.py][4]为例。将代码和数据下载到个人文件夹后，编辑pbs文件为：

```bash
#PBS -N mnist_tf_singularity
### use one node for this job
#PBS -l nodes=1
#PBS -q default
cd $PBS_O_WORKDIR

/usr/local/bin/singularity exec /mnt/data/container_library/deep_learning/tensorflow-10.0-py3.simg python convolutional.py
```

[1]: https://en.wikipedia.org/wiki/Singularity_(software)
[2]: https://baike.baidu.com/item/%E8%99%9A%E6%8B%9F%E6%9C%BA/104440
[3]: https://www.sylabs.io/guides/2.6/user-guide/index.html
[4]: https://github.com/tensorflow/models/blob/master/tutorials/image/mnist/convolutional.py

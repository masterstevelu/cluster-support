# TensorFlow

集群目前支持使用CPU在容器中进行深度学习。关于容器技术，请参考我们的 [Singularity](singularity.md) 的介绍。

## 在集群上使用singularity启动TensorFlow作业

这里以 TensorFlow 提供的官方tutorials中使用卷积网络对mnist数据进行分类的代码[convolutional.py][1]为例。将代码和数据下载到个人文件夹后，编辑pbs文件为：

```bash
#PBS -N mnist_tf_singularity
### use one node for this job
#PBS -l nodes=1
#PBS -q default
cd $PBS_O_WORKDIR

/usr/local/bin/singularity exec /mnt/data/container_library/deep_learning/tf-1.10.0-py35 python convolutional.py
```

[1]: https://github.com/tensorflow/models/blob/master/tutorials/image/mnist/convolutional.py

## 在Jupyter中使用TensorFlow

请参考我们的[Jupyter](jupyter.md)中的教程。

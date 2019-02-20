# R

虽然在公共目录上安装了R，但是使用该环境安装其他 package 的时候，会出现一些依赖错误。与 [Python使用方法](python.md) 类似，我们推荐使用 Anaconda 来使用和管理 R 语言环境，这个环境不会出现安装依赖错误。

Anaconda基础环境（base）包含了R 3.5所需的基础库，能够满足绝大多数计算需求。用户如需要安装其他版本的R，可以重新创建一个新的R环境。

安装自己的环境：

```bash
conda create -n test_r_env r-essentials r-base
```

其他所依赖的包可以进入交互模式后使用 `install.packages('your_packages')` 根据命令行提示来进行安装。

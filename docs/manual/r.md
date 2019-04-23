# R

R是用于统计分析、可视化的语言，被广泛应用于各大科研机构的经济、统计、社会科学等学科上。虽然在公共目录上安装了R，但是使用该环境安装其他 package 的时候，会出现一些依赖错误。我们推荐使用 Anaconda 来使用和管理 R 语言环境，这个环境不会出现安装依赖错误。

## R语言环境

Anaconda基础环境（base）包含了R 3.5所需的基础库，能够满足绝大多数计算需求，我们建议直接使用这个环境下的R。

加载 Anaconda：

```bash
module load anaconda/5.3.0
```

执行完此命令后，你已经可以使用我们提供的R 3.5基础环境，此环境也安装有Python 3.7和Julia 1.03。

此环境已经安装了data.table、dplyr、ggplot2、Rcpp等常用软件包。

进入R，使用下面的命令查看已安装包。

```r
print(as.data.frame(installed.packages()[,c(1,3)]))
```

同时我们也提供了一个 R 3.5 的环境，此环境安装了parallel、snow等并行编程包。

```bash
source activate r35
```

## 管理R包

其他所依赖的包可以进入交互模式后使用 `install.packages('your_package')` 根据命令行提示来进行安装，使用 `update.packages('your_package')` 更新包，使用 `remove.packages('your_package')` 来删除。

首次安装时，R会提示你包的安装位置，默认位置是用户HOME目录的R文件夹下。

一些包会依赖其他底层包，需要使用较新的C++编译器来编译代码，因此会出现安装不成功的现象，conda 使用较新的编译环境，因此我们建议使用 conda 来和管理安装包。

## 安装自己的环境

用户如需要使用Anaconda其他版本的R，可以重新创建一个新的R环境。不熟悉 Anaconda 的用户可以先阅读 [Anaconda使用方法](python.md) ，了解 `conda` 命令和相关知识。

安装一个R 3.3版本环境，命名环境为r33：

```bash
conda create -n r33 r-base=3.3
```

其中 `-n r33` 表示创建新的环境名，`r-base=3.3`指定了R的版本，记得这里要改成你自己的R环境名。这就创建了一个名为`r33`的基础环境，里面安装了3.3版本的R。

使用 `source` 来激活这个环境。

```bash
source activate r33
```

在刚刚创建的 r33 环境上安装 doSnow 包，注意 -n 表示将包安装到你所需的环境中：

```bash
conda install -n r33 -c conda-forge r-dosnow 
```

其他包的安装方法，可以在 https://anaconda.org/ 中搜索。

## 运行 R

### 交互模式

使用交互模式向集群申请一个节点，然后在该节点进入R：

```bash
$ qsub -I -l walltime=00:05:00 -l nodes=1:ppn=10
$ module load anaconda/5.3.0
$ R

R version 3.5.1 (2018-07-02) -- "Feather Spray"
Copyright (C) 2018 The R Foundation for Statistical Computing
Platform: x86_64-conda_cos6-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

>
```

`-I`表示使用交互模式，`-l walltime=00:05:00 -l nodes=1:ppn=10`表示向调度器申请1个节点的10个CPU核心，使用时间是5分钟，5分钟后，调度器将收回这些资源。也可以不设置`-l walltime=00:05:00`，不限制使用时间。

进入交互模式后，记得加载R语言环境 `module load anaconda/5.3.0` 并启动R。

### 批处理作业

对于运行时间较长的程序，调试好代码后，建议使用提交作业的方式来运行程序。这里给出了一个最小二乘的样例，名为ols.R，然后使用 `Rscript` 方式运行R程序：

```r
# Goal: Simulate a dataset from the OLS model and obtain
#       obtain OLS estimates for it.

x <- runif(100, 0, 10)                  # 100 draws from U(0,10)
y <- 2 + 3*x + rnorm(100)               # beta = [2, 3] and sigma = 1

# You want to just look at OLS results?
summary(lm(y ~ x))

# Suppose x and y were packed together in a data frame --
D <- data.frame(x,y)
summary(lm(y ~ x, D))

# Full and elaborate steps --
d <- lm(y ~ x)
# Learn about this object by saying ?lm and str(d)
# Compact model results --
print(d)
# Pretty graphics for regression diagnostics --
par(mfrow=c(2,2))
plot(d)

d <- summary(d)
# Detailed model results --
print(d)
# Learn about this object by saying ?summary.lm and by saying str(d)
cat("OLS gave slope of ", d$coefficients[2,1],
    "and a error sigma of ", d$sigma, "\n")


## I need to drop down to a smaller dataset now --
x <- runif(10)
y <- 2 + 3*x + rnorm(10)
m <- lm(y ~ x)

# Now R supplies a wide range of generic functions which extract
# useful things out of the result of estimation of many kinds of models.

residuals(m)
fitted(m)
AIC(m)
AIC(m, k=log(10))                        # SBC
vcov(m)
logLik(m)
```

编写提交作业脚本 submit_R.sh，<font color=red >将这个脚本跟你的代码放在同一个文件夹下</font>。

```bash
#!/bin/bash

#PBS -N R_job
### use one node for this job
#PBS -l nodes=1:ppn=1
#PBS -q default

cd $PBS_O_WORKDIR
module load anaconda/5.3.0

Rscript ols.R
```

上面这个脚本中`#PBS -l nodes=1:ppn=1`这行设置了申请多少CPU计算资源。申请过多的资源不但不会加速你的程序，而且还会导致自己和他人的作业排队；申请过少的资源，会导致你自己的作业运行较慢。**R语言默认只能使用多核处理器的单个核心**，如果没有其他优化的话，ppn应该设置为1。如果你不确定自己应该申请多少计算资源，可以参考我们提供的[资源申请指南](../resources.md)。

然后提交这个作业：

```bash
qsub submit_R.sh
```

程序的结果将写入同文件夹的 R_job.oXXXX 文件中，报错信息将写入 R_job.eXXXX 文件中，其中 XXXX 为作业的id。你可以将这两个文件同步到你的个人电脑上查看，也可以在集群上使用 `cat R_job.oXXXX` 命令查看输出结果。
PBS脚本中 `#PBS -N R_job` 来修改作业名，不同的作业的运行结果会写到对应作业名的输出文件中。

## 并行加速

在未做任何优化情况下，R只能使用服务器众多核心的单个核心，即只能利用服务器 1/20 的计算能力。即使在PBS脚本中申请多个核心，也并不能加速你的程序。如果想利用服务器的多核，需要做一些优化，例如调用 parallel、snow 等包，才能充分利用多个CPU核心。

## 使用 Jupyter

Jupyter的使用请参考 [Jupyter使用方法](jupyter.md)。

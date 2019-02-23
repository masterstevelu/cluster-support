# R

虽然在公共目录上安装了R，但是使用该环境安装其他 package 的时候，会出现一些依赖错误。与 [Python使用方法](python.md) 类似，我们推荐使用 Anaconda 来使用和管理 R 语言环境，这个环境不会出现安装依赖错误。

## R语言环境

Anaconda基础环境（base）包含了R 3.5所需的基础库，能够满足绝大多数计算需求，我们建议直接使用这个环境下的R。用户如需要安装其他版本的R，可以重新创建一个新的R环境。

安装自己的环境：

```bash
conda create -n test_r_env r-essentials r-base
```

## 管理R包

其他所依赖的包可以进入交互模式后使用 `install.packages('your_package')` 根据命令行提示来进行安装，使用 `update.packages('your_package')` 更新包，使用 `remove.packages('your_package')` 来删除。

首次安装时，R会提示你包的安装位置，默认位置是用户HOME目录的R文件夹下。

## 运行 R

### 交互模式

使用交互模式向集群申请一个节点，然后在该节点进入R：

```bash
qsub -I -l walltime=00:05:00 -l nodes=1:ppn=10

qsub: waiting for job 5906.rmdx-cluster.edu.com.cn to start
qsub: job 5906.rmdx-cluster.edu.com.cn ready

[luweizheng@node-3-2 ~]$ module load anaconda/5.3.0
[luweizheng@node-3-2 ~]$ R

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

编写提交作业脚本 submit_R.pbs ：

```bash
#!/bin/bash

#PBS -N R_job
### use one node for this job
#PBS -l nodes=1
#PBS -q default

cd $PBS_O_WORKDIR
module load anaconda/5.3.0

Rscript ols.R
```

程序将输出生成到了标准输出，并生成了相应的图片。

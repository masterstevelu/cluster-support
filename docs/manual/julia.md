# Julia

[Julia][1] 是MIT主导开发的一款新的编程语言，这个编程语言将 C 语言的速度、Matlab 的矩阵和数学计算能力、Python 的通用性前所未有地结合在一起，支持并行处理，易于学习和使用，尤其适合科学计算和大数据处理。因其开源免费，且性能优越，其用户量每九个月就翻一翻，得到数据科学界的广泛关注。一些研究机构甚至抛弃了matlab和R，转而投向Julia的怀抱。

## 首次安装Julia

我们推荐使用 Anaconda 来安装和管理 Julia 语言环境。不熟悉 Anaconda 的用户可以先阅读 [Anaconda 使用方法](python.md) ，了解 `conda` 命令和相关知识。

创建自己的Julia环境，环境名为julia：

```bash
module load anaconda/5.3.0
conda create -n julia -c conda-forge julia
```

其中 `-n julia` 表示创建环境名。

也可以将Julia安装到你已有的conda环境中：

```bash
conda install -n your_env_name -c conda-forge julia
```

激活Julia环境，进入的julia的交互模式，在命令行中输入：

```bash
source activate julia
julia
```

## Julia包

在Julia中安装包非常简单，只需要使用系统自带的`Pkg`模块来安装自己需要的包。

```julia
julia> import Pkg

julia> Pkg.status()
    Status `~/.julia/environments/v1.0/Project.toml`

julia> Pkg.add("Calculus")s
```

其他包名可以根据需要自己安装，包将安装在 `~/.julia` 目录，即个人家目录下的一个隐藏目录中。

## 在Jupyter中使用Julia

如果使用Jupyter的话，首先需要在Julia中安装支持Jupyter的包：

```julia
using Pkg
Pkg.add("IJulia")
```

然后按照我们提供的[Jupyter教程](jupyter.md)来启动Jupyter，记住要先使用`source activate`命令加载你的 julia 环境。Jupyter启动后，右侧的 “NEW” 按钮新建文件的时候，就可以选择 Julia 作为编程语言了。

## 提交作业

生成一个 Hello World 代码：

```julia
println("hello world")
```

生成一个提交 julia 作业的脚本 `submit_julia.pbs` ：

```bash
#!/bin/bash

#PBS -N julia_example
### use one node for this job
#PBS -l nodes=1
#PBS -q default

cd $PBS_O_WORKDIR
module load anaconda/5.3.0
source activate julia

julia hello.jl
```

使用 `qsub submit_julia.pbs` 来提交作业。

## 并行计算

比起 Python 和 R，Julia在设计之初就考虑了并行，而且不用使用 MPI 那样复杂的编程接口。

一个并行的例子 `julia_parallel_test.jl`：

```julia
@everywhere println("process: $(myid()) on host $(gethostname())")
```

PBS脚本为：

```bash
#!/bin/bash

#PBS -N julia_example
### use one node for this job
#PBS -l nodes=2:ppn=4
#PBS -q default

cd $PBS_O_WORKDIR
module load anaconda/5.3.0
source activate julia

julia --machine-file $PBS_NODEFILE ./julia_parallel_test.jl
```

注意这里使用了 `--machine-file` 这个参数。脚本向调度系统申请了2个节点，每个节点4个核心，共8个核心。

输出结果为：

```bash
process: 1 on host node-3-2.local
      From worker 3:	process: 3 on host node-2-11.local
      From worker 2:	process: 2 on host node-3-2.local
      From worker 5:	process: 5 on host node-2-11.local
      From worker 6:	process: 6 on host node-2-11.local
      From worker 4:	process: 4 on host node-2-11.local
      From worker 8:	process: 8 on host node-3-2.local
      From worker 7:	process: 7 on host node-3-2.local
      From worker 9:	process: 9 on host node-3-2.local
```




[1]: https://julialang.org/

[Julia][1] 是MIT主导开发的一款新的编程语言，这个编程语言将 C 语言的速度、Matlab 的矩阵和数学计算能力、Python 的通用性前所未有地结合在一起，支持并行处理，易于学习和使用，尤其适合科学和工程计算，因其开源和免费，最近得到数据科学界的广泛关注。

虽然在公共目录上安装了Julia，但与 [Python使用方法](manual/python.md) 类似，我们推荐使用 Anaconda 来安装和管理 Julia 语言环境。目前，anaconda上 `base` 和 `julia_base` 两个环境都已经安装了 Julia 1.0.3 环境

用户如需要安装其他依赖库，可以联系我们，由管理员在安装在基础环境上，也自己可以从 `julia_base` 环境上克隆出自己的环境，或者重新创建一个新的Julia环境。

克隆Julia基础环境

```bash
conda create --name julia_my_evn --clone julia_base
```

创建自己的Julia环境：

```bash
conda create -n julia_my_evn
conda install -c conda-forge julia -n julia_my_env
```

进入的julia的交互模式，在命令行中输入：

```bash
source activate julia_my_env
julia
```

安装包：

```julia
using Pkg
Pkg.add("Flux")
```

[1]: https://julialang.org/

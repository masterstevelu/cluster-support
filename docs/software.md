# 软件支持

我们在集群上安装了常用的编译器、MPI库、数学库、解释语言与科学计算库，涵盖了绝大多数科学计算应用场景。本文将介绍如何使用这些软件。

!!! note "什么是 Linux 环境变量"
    在Linux系统下使用软件需要了解“环境变量”这个概念。不同的软件会被安装在不同的目录下，环境变量决定了Linux系统将到哪个目录中寻找安装好的软件。用户在命令行或脚本中使用软件时，Linux 系统会从环境变量的所有路径下，从前到后寻找用户输入的软件，调用该目录的软件执行程序。一些常用的软件，如 `ssh` 已经被默认添加在系统的环境变量中。一些用户安装的软件一般要将该软件所在目录手动加载到系统的环境变量。

## 软件加载方法

### 使用source加载软件

我们在公共目录下准备好了添加环境变量的脚本，可以使用 `source <env_script>` 来添加环境变量。

比如，使用 openmpi 3.1.1：

```bash
source /public/software/gnu/etc/env.openmpi.3.1.1
```

使用 `which` 命令查看是否被加载

```bash
which mpicc
```

结果为：`/public/software/gnu/openmpi-3.1.1/bin/mpicc`，表示系统已经加载该软件，接下来执行 `mpicc` 命令都使用这个目录下的安装的软件。

!!! tip "提示"
    使用 gcc 编译器编译的软件环境变量脚本放置 `/public/software/gnu/etc/` 下，使用 intel 编译器编译的软件环境变量脚本放置在了 `/public/software/intel/etc` 下。

一个使用source加载软件的PBS脚本

```bash
#!/bin/bash

#PBS -N use_source_example
#PBS -o example.stdout
#PBS -e example.stderr
#PBS -q default
#PBS -l nodes=2:ppn=4
cd $PBS_O_WORKDIR

### set environment
source /public/software/gnu/etc/env.openmpi.3.1.1

### run an example mpi job
mpirun -np $NP -machinefile $PBS_NODEFILE ./mpi
```

### 使用module加载软件

`source` 命令可以满足绝大多数需求，不过为了更加方便易用，有研究人员开发了 [module][1] 这个软件来帮助用户加载软件。module 软件是一个用户界面友好的软件加载工具。

* 列举可被加载的软件

```bash
module avail
```

系统将展示所有可用的软件。

* 加载某个软件

```bash
module load mpi/openmpi-1.6.5
```

!!! tip "提示"
    软件命名规则采用了“软件名/软件版本号”的方式。用户可以使用 `tab` 键来帮助补全。

* 查看当前加载的软件
```sh
module list
```

* 清除某个加载过的软件

```sh
module unload mpi/openmpi-1.6.5
```

* 清除所有加载过的软件

```sh
module purge
```

一个使用module加载软件的PBS脚本

```bash
#!/bin/bash

#PBS -N use_module_example
#PBS -l nodes=1:ppn=10
#PBS -q default
#PBS -o example.stdout
#PBS -e example.stderr
cd $PBS_O_WORKDIR

### set environment
module load mpi/openmpi-1.6.5

### run an example mpi job
mpirun -np $NP -machinefile $PBS_NODEFILE ./mpi
```

## 已安装软件及其加载方法

### 编译器

| 软件类型 	| 简介                                                        	| 版本  	| 加载方法                                           	|
|----------	|-------------------------------------------------------------	|-------	|----------------------------------------------------	|
| gcc      	| GNU compile collection，包括C/C++和Fortran编译器            	| 4.4.7 	| 系统已默认加载                                     	|
| icc      	| Intel compiler suite，包括C/C++和Fortran编译器              	| 2013  	| `source /public/software/intel/etc/env.intel.2013` 	|
|          	| Intel compiler suite，包括C/C++和Fortran编译器以及Intel MKL 	| 2015  	| `source /public/software/intel/etc/env.intel.2015` 	|
| jdk      	| Java Development Kit                                        	| 1.8   	| 系统已默认加载                                     	|

### MPI库

| 软件类型  	| 简介      	| 版本             	| 加载方法                                                     	|
|-----------	|-----------	|------------------	|--------------------------------------------------------------	|
| openmpi   	| OpenMPI   	| 1.6.5            	| `source /public/software/intel/etc/env.openmpi.1.6.5`        	|
|           	|           	| 1.6.5-infiniband 	| `source /public/software/intel/etc/env.openmpi.1.6.5.openib` 	|
|           	|           	| 3.1.1            	| `source /public/software/intel/etc/env.openmpi.3.1.1`        	|
| Intel MPI 	| Intel MPI 	| 2015             	| `source /public/software/intel/etc/env.intel.2015`           	|

### 分子动力学软件

| 软件类型 	| 简介                                           	| 加载方法                                              	|
|----------	|------------------------------------------------	|-------------------------------------------------------	|
| amber    	| 串行版amber18                                  	| `source /public/software/gnu/etc/env.amber18.serial`  	|
|          	| 使用openmpi编译的并行版                        	| `source /public/software/gnu/etc/env.amber18.openmpi` 	|
| gromacs  	| gromacs 4.5.7 使用openmpi 1.6.5和 fftw double精度编译 	| `source /public/software/gnu/etc/env.gromacs.4.5.7`   	|
| namd     	| namd 2.12                                      	| `source /public/software/gnu/etc/env.namd.2.12`       	|

### Python

参见 [Python使用方法](manual/python.md)。

### R

参见 [R使用方法](manual/r.md)。

### Julia

参见 [Julia使用方法](manual/julia.md)。

### Jupyter

参见 [Jupyter使用方法](manual/jupyter.md)。

[1]: http://modules.sourceforge.net/

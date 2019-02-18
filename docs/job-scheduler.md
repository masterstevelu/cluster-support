# 作业调度

高性能计算集群配有较高的计算资源，同时拥有较多用户，是一个多用户共享的服务。为了有效利用众多处理器核心所提供的计算能力，需要一个作业管理调度系统，统一用户作业提交，合理分配计算资源，将用户的作业指派到最合适的资源上执行。

!!! note "调度器与队列"

    如果把向集群提交作业比作前往银行办理业务，那么作业调度器就是银行门口的取号机，用户需要排队，根据叫号来办理业务。用户一般需要告知取号机自己办理哪种服务，比如存取款的普通柜台、对公柜台、VIP柜台、理财顾问等等。取号机在相应的柜台服务上生成排队号码。调度器中的队列与之相似，用户在提交作业时，一般要告知调度器作业所属的队列，调度器会把用户作业放在该队列里排队。

## PBS简介

集群基于 PBS（Portable Batch System）作业管理系统来管理和调度计算任务。PBS是由NASA开发的作业调度管理系统。它被用于集群系统、超级计算机和大规模并行系统。PBS后来又发展出openPBS、PBS Pro和Torque三大分支。其中Torque是目前使用比较广泛的PBS开源实现。本集群使用Torque作为作业调度器。集群用户需要使用 PBS 脚本和命令提交计算任务。

PBS系统的命令包括作业控制、作业检测和节点状态等几大部分。下面将介绍如何使用PBS系统来提交作业，其他命令的具体使用可参考[PBS使用手册](manual/torque.md)相关内容。

## 作业提交流程

一个标准的作业提交步骤将包含以下流程：

1. 准备：用户编写代码，编译生成可执行文件，准备输入文件和提交作业作业的脚本（包括作业名，需要的计算资源等）。
2. 提交：用户使用作业提交命令 `qsub` 将该作业提交给作业调度器。
3. 排队：调度器将该任务排入队列。
4. 调度：调度器检查队列内各计算节点的状态是否符合该作业的要求，并进行调度。
5. 执行：当条件满足时，作业被发给相应的执行计算节点执行。程序运行时执行计算节点会收集程序的标准输出和标准错误流，等程序结束时，将这些信息返回给用户。
6. 查询和调整：当作业在运行时，用户可以使用监控命令 `qstat` 进行状态查询。用户发现作业提交错误时，可以使用作业删除命令 `qdel` 删除正在运行的作业。
7. 查看结果：使用 `qpeek` 查看作业运行时的标准输出。

## 使用qsub提交作业

在PBS调度系统中提交任务需要使用 `qsub` 命令，`qsub` 命令是整个调度系统中使用最多的命令。

```shell
qsub [options] <script>
```

其中options表示参数列表，参数列表包括作业名称、作业需要申请的节点数等等参数，script为要执行的作业本身。

例如，我们提交一个名为 hello.sh 的作业，其功能为要求每个节点睡眠10秒钟，并打印节点的主机名。

hello.sh的内容为

```bash
#!/bin/bash

sleep 10
hostname
```

在集群上提交这个作业的方式为

```bash
qsub hello.sh
```

## PBS作业参数

前一节介绍了如何使用 `qsub` 来向集群提交作业。在提交作业过程中需要指定参数，以向集群申请资源。下表列出常用的 PBS 作业参数。

| 属性 	| 取值                                                                   	| 说明                                 	|
|------	|------------------------------------------------------------------------	|--------------------------------------	|
| -l   	| 资源申请参数，通常为多个参数，参数间以冒号隔开                         	| 设定作业所需资源                     	|
| -N   	| 作业名称                                                               	| 设定作业名称                         	|
| -o   	| 程序运行后标准输出的文件路径，可以设置为用户的家目录下某个文件夹下     	| 设定作业的标准输出文件路径           	|
| -e   	| 程序运行后错误日志输出的文件路径，可以设置为用户家目录下的某个文件夹下 	| 设定作业的标准错误文件路径           	|
| -p   	| -1024到1023之间的整数                                                  	| 设定作业的优先级，数值越大优先级越高 	|
| -q   	| 队列名                                                                 	| 设定作业队列名称                     	|

*表 PBS作业常用参数*

PBS系统允许两种方式设置上述作业参数。一种是在执行 `qsub` 命令时附上参数；另一种是将这些参数写在 shell 脚本里，在脚本中指定这些参数。脚本中的这些参数以“#PBS”开头，参数形式跟qsub命令参数一样。

依然以前面所举的 `hello.sh` 为例，分别演示两种提交作业的方式。

**在命令中附上参数**

```bash
qsub -N hello -o hello_out -e hello_err -q default hello.sh
```

作业被提交到了 default 队列中，作业名叫做 hello , 作业成功运行的标准输出为 hello_out 文件，出现错误的话，输出的错误文件为 hello_err。

**在脚本中设定参数**

编辑一个脚本名为 `submit_hello.sh`

```bash
#!/bin/bash

### set job name
#PBS -N hello
### set job queue
#PBS -q daopian

### print job id and job queue
echo "This jobs is "$PBS_JOBID@$PBS_QUEUE
echo begin time is `date`
sh hello.sh
echo end time is `date`
```

执行这个脚本

```bash
qsub submit_hello.sh
```

!!! note "注释与井号"

    在 Linux shell 脚本中，行首单个井号“#”表示注释。PBS系统必须使用"#PBS"前缀来设定参数。为区别PBS参数和注释内容，实际注释内容就用三个井号来表示。

第二种方法起到的效果与第一种相同，第二种方法还可以在脚本中添加一些我们自己想要添加的命令或程序，比如这个例子中的打印开始时间和结束时间的计时功能。

我们建议使用第二种在在脚本中设定参数的方法。

## 申请计算资源

作业提交脚本中最为重要的参数为计算资源申请部分。

如请求4个节点，每个节点上使用2个CPU，每个核心的最大内存为1024mb，那么-l参数应该写为:

```bash
#PBS -l nodes=4:ppn=2,pmem=1024mb
```

也可以将这些参数拆开，分行写成

```bash
#PBS -l nodes=4
#PBS -l ppn=2
#PBS -l pmem=1024mb
```

| 资源     	| 取值                            	| 说明                                                                          	|
|----------	|---------------------------------	|-------------------------------------------------------------------------------	|
| nodes    	| 节点                            	| 设定作业所需节点资源                                                          	|
| ppn      	| 数值，应小于每个节点上的CPU核数 	| 设定每个节点所需要的CPU核数                                                   	|
| walltime 	| hh:mm:ss                        	| 设定作业所需的最大墙上时间，从进程从开始运行到结束，时钟走过的时间            	|
| pmem     	| 正整数，后面可跟b、kb、mb、gb   	| 设定作业在每个核心上所需最大内存                                              	|
| cput     	| hh:mm:ss                        	| 设定作业所需的最大CPU时间，用户的作业获得了CPU资源以后，所有CPU执行时间的总和 	|

*表 常用资源设置参数*


## PBS脚本变量

PBS脚本中有一些与集群环境相关的环境变量。这些环境变量是PBS系统传递给脚本的变量内容，在脚本中需要使用 `${PBS_*}`这样的形式获取变量值。

例如，`${PBS_O_WORKDIR}`是执行`qsub`命令所在的绝对路径，`${PBS_QUEUE}`是作业所执行的队列名。

| 环境变量              	| 说明                                                  	|
|-----------------------	|-------------------------------------------------------	|
| 登陆SHELL继承来的变量 	| $HOME，$LANG，$PATH，$MAIL，$SHELL等shell自带环境变量 	|
| PBS_ENVIRONMENT       	| 批处理作业为 PBS_BATCH，交互式作业为 PBS_INTERACTIVE  	|
| PBS_JOBID             	| PBS 系统给作业分配的标识号                            	|
| PBS_JOBNAME           	| 用户指定的作业名称                                    	|
| PBS_NODEFILE          	| 执行作业的节点列表，列表的形式为：node1 node2 node3 … 	|
| PBS_QUEUE             	| 作业所执行的队列名称                                  	|
| PBS_O_HOME            	| 执行 qsub 命令的 HOME 环境变量值                      	|
| PBS_O_PATH            	| 执行 qsub 命令的 PATH 环境变量值                      	|
| PBS_O_SHELL           	| 执行 qsub 命令的 SHELL 环境变量值                     	|
| PBS_O_HOST            	| 执行 qsub 命令节点名称                                	|
| PBS_O_QUEUE           	| 提交的作业的最初队列名称                              	|
| PBS_O_WORKDIR         	| 执行 qsub 命令所在的绝对路径                          	|

*表 PBS脚本环境变量*

## 通用脚本样例

一般地，一个提交作业的脚本应如下所示。

```sh
#!/bin/bash

### file: example.sh
### set job name
#PBS -N example-job
### set output files
#PBS -o example.stdout
#PBS -e example.stderr
### set queue name
#PBS -q default
### set computing resources
#PBS -l nodes=2:ppn=4
# enter job's working directory
cd $PBS_O_WORKDIR
# get the number of processors
NP=`cat $PBS_NODEFILE | wc -l`

### add environment variables
module load mpi/intelmpi-2015
# run an example mpi4py job
mpirun -np $NP -machinefile $PBS_NODEFILE python example_mpi4py.py
```

这个样例脚本基本上能覆盖了绝大多数的功能，用户可以根据这个样例脚本做一些修改，生成适合自己的提交作业脚本。

## 使用qstat查看作业

直接执行 `qstat` 可以看到当前用户所有提交作业，使用 `qstat -f <job_id>` 可以查看某个job的信息。
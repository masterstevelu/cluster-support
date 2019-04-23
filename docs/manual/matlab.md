# matlab

MATLAB（矩阵实验室）是MATrix LABoratory的缩写，是一款由美国 MathWorks 公司出品的商业数学软件。MATLAB是一种用于算法开发、数据可视化、数据分析以及数值计算的高级技术计算语言和交互式环境。MATLAB主要用于数值运算，但利用为数众多的附加工具箱（Toolbox）它也适合不同领域的应用，例如控制系统设计与分析、图像处理、信号处理与通讯、金融建模和分析等。

在集群上，一般不推荐使用Matlab的界面，而要用命令行提交批处理任务。

!!! warning "不要直接在登录节点运行matlab！"
        请不要直接在登录节点（IP为183.174.229.251，名为rmdx-cluster的节点）上运行 matlab 任务，而应该使用调度系统来提交任务，否则会影响其他用户使用集群。有关调度系统的介绍和使用，请参考我们提供的[文档](../job-scheduler.md)。

## 加载软件

```bash
module load matlab/2016b
```

## 提交matlab作业

准备好你的matlab代码，例如 `matlab_simple.m` 文件：

```matlab
% Creates a 10x10 Magic square
M = magic(10);
M
exit
```

准备一个作业提交脚本 `submit_matlab.pbs`，<font color=red >将脚本跟你的代码放在同一个文件夹下</font>。其中 `-nodisplay` 表示不使用图形化界面， `-nosplash` 表示启动matlab时不显示闪屏版权信息：

```bash
#!/bin/bash

#PBS -N matlab_example
### use one node for this job
#PBS -l nodes=1:ppn=1
#PBS -q default

cd $PBS_O_WORKDIR
module load matlab/2016b
matlab -nodisplay -nosplash < matlab_simple.m
```

上面这个脚本中`#PBS -l nodes=1:ppn=1`这行设置了申请多少CPU计算资源。不同的程序所需的资源不同，申请过多的资源不但不会加速你的程序，而且还会导致自己和他人的作业排队；申请过少的资源，会导致你自己的作业运行较慢。具体如何申请资源，可以参考我们提供的[资源申请指南](../resources.md)。

提交这个任务：

```bash
qsub submit_matlab.pbs
```

程序的结果将写入同文件夹的 matlab_example.oXXXX 文件中，报错信息将写入 matlab_example.eXXXX 文件中，其中 XXXX 为作业的id。你可以将这两个文件同步到你的个人电脑上查看，也可以在集群上使用 `cat matlab_example.oXXXX` 命令查看输出结果。
PBS脚本中 `#PBS -N matlab_example` 来修改作业名，不同的作业的运行结果会写到对应作业名的输出文件中。

## 并行加速

为了利用集群机器的多个CPU核心，对于计算密集型的任务，可以考虑使用matlab提供的并行工具箱，一些使用方法请阅读官方文档：[Parallel Computing Toolbox][1]。

一般地，我们可以使用 `parfor` 来替换原来的 `for` 来做循环。大致步骤为：

1. 创建一个 matlab pool
2. 初始 pool 参数，包括并行处理器数，临时文件地址等
3. 将代码中所有 `for` 替换为 `parfor` 

注意代码中的 `parpool()` 函数第二个参数要输入并行所使用的CPU核心数。

一个使用 `parfor` 的简单例子：

```matlab
% start the matlabpool with 24 workers
pc = parcluster('local')

parpool(pc, 24)

% run a parfor loop, distributing the iterations to 24 workers
parfor i = 1:100
        ones(10,10)
end
```

一个使用例子 `matlab_parfor.m` ：

```matlab
%{
   This script samples a parameter of a stiff ODE and solves it both in
   serial and parallel (via parfor), comparing both the run times and the
   max absolute values of the computed solutions. The code -- especially the
   serial part -- will take several minutes to run.
%}

% open the local cluster profile
p = parcluster('local');

% open the parallel pool, recording the time it takes
time_pool = tic;
% set the number of parallel CPU cores 
parpool(p, 24);
time_pool = toc(time_pool);
fprintf('Opening the parallel pool took %g seconds.\n', time_pool)

% create vector of random coefficients on the interval [975,1050]
nsamples = 100; % number of samples
coef = 975 + 50*rand(nsamples,1); % randomly generated coefficients

% compute solutions within serial loop
time_ser = tic;
y_ser = cell(nsamples,1); % cell to save the serial solutions
for i = 1:nsamples
  if mod(i,10)==0
    fprintf('Serial for loop, i = %d\n', i);
  end
  [~,y_ser{i}] = ode15s(@(t,y) stiffODEfun(t,y,coef(i)) ,[0 10000],[2 0]);
end
time_ser = toc(time_ser);

% compute solutions within parfor
time_parfor = tic;
y_par = cell(nsamples,1); % cell to save the parallel solutions
err = zeros(nsamples,1); % vector of errors between serial and parallel solutions
parfor i = 1:nsamples
  if mod(i,10)==0
    fprintf('Parfor loop, i = %d\n', i);
  end
  [~,y_par{i}] = ode15s(@(t,y) stiffODEfun(t,y,coef(i)) ,[0 10000],[2 0]);
  err(i) = norm(y_par{i}-y_ser{i}); % error between serial and parallel solutions
end
time_parfor = toc(time_parfor);
time_par = time_parfor + time_pool;

% print results
fprintf('RESULTS\n\n')
fprintf('Serial time : %g\n', time_ser)
fprintf('Parfor time : %g\n', time_par)
fprintf('Speedup : %g\n\n', time_ser/time_par)
fprintf('Max error between serial and parallel solutions = %e\n', max(abs(err)))

% close the parallel pool
delete(gcp)
exit
```

其中使用了函数 `stiffODEfun`：

```matlab
function dy = stiffODEfun(t,y,c)
  % This is a modified example from MATLAB's documentation at:
  % http://www.mathworks.com/help/matlab/ref/ode15s.html
  % The difference here is that the coefficient c is passed as an argument.
    dy = zeros(2,1);
    dy(1) = y(2);
    dy(2) = c*(1 - y(1)^2)*y(2) - y(1);
end
```

提交作业的脚本 `submit_parfor.pbs` ，其中使用了一个节点的24个核心，ppn即为所使用的核心数，这与matlab代码中的 `parpool()` 第二个参数要保持一致：

```bash
#!/bin/bash

#PBS -N matlab_parfor
### use one node with 24 cores for this job
#PBS -l nodes=1:ppn=24
#PBS -q default

BASE_MFILE_NAME=matlab_parfor
MATLAB_OUTPUT=${BASE_MFILE_NAME}.out

cd $PBS_O_WORKDIR
module load matlab/2016b
matlab -nodisplay -nosplash -r $BASE_MFILE_NAME > $MATLAB_OUTPUT
```

## 运行多个并行任务

matlab 使用了一个磁盘上的临时文件来存储一些作业信息，每份作业的信息是不相同的。当同时提交多个并行任务时，多个任务同时去同一个临时文件上读取这份信息，有可能导致程序错误。为避免错误，我们可以手动设定这个临时文件夹：在提交作业的脚本中创建一个临时文件夹，并告知matlab使用这个地址，临时文件夹名使用了作业ID：

```bash
#!/bin/bash

#PBS -N matlab_parfor
### use one node with 24 cores for this job
#PBS -l nodes=1:ppn=24
#PBS -q default

BASE_MFILE_NAME=matlab_parfor
MATLAB_OUTPUT=${BASE_MFILE_NAME}.out

cd $PBS_O_WORKDIR
# create a temporary dir for your parallel job
mkdir -p $PBS_JOBID
module load matlab/2016b
matlab -nodisplay -nosplash -r $BASE_MFILE_NAME > $MATLAB_OUTPUT

# clean up the temporary dir you just created before
rm -rf $PBS_JOBID
```

注意 matlab 中要 `pc.JobStorageLocation = getenv('PBS_JOBID')` 对临时文件地址做修改。

```matlab
% start the matlabpool with maximum available workers
% control how many parallel workers you want to set
pc = parcluster('local', 24)

% explicitly set the JobStorageLocation to the temp directory that was created in your submit job script
pc.JobStorageLocation = getenv('PBS_JOBID')

parpool(pc, 24)

% run a parfor loop, distributing the iterations to 24 workers
parfor i = 1:100
        ones(10,10)
end
```

[1]: https://ww2.mathworks.cn/products/parallel-computing.html
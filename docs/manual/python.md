我们在公共目录上安装了Python解释器，也安装了 [Anaconda][1] 包管理器。建议用户使用Anaconda。

# 使用 conda 管理 Python 环境
加载 Anaconda
```bash
module load anaconda/5.3.0
```

接下来我们用 `conda` 来安装和管理一个针对自己的Python环境。

```
conda create -n test_env python=3.6
```

conda 会在用户个人目录 `/home/~your-cluster-username~/.conda/envs/test_env` 里创建 Python 3.6 的环境，安装了Python 3.6解释器、pip等软件。

使用 conda 继续安装所需软件包 `numpy`，-n 表示安装到所创建的test_env的Python环境。

```bash
conda install -n test_env numpy
```

激活这个环境，激活后行首会出现`(your_env_name)`，表示激活成功：

```bash
[linpack@rmdx-cluster ~]$ source activate test_env
(test_env) [linpack@rmdx-cluster ~]$
```

在这个环境中也可以使用 `pip` 来安装自己想要安装的软件：

```bash
pip3 install jieba -i https://pypi.douban.com/simple
```

!!! tip "国内源"
    pip默认使用的官方源服务器在国外，相对比较慢。`-i https://pypi.douban.com/simple` 使用了豆瓣提供的国内Python安装源，可以大大加快安装速度。

退出该环境：

```bash
source deactivate
```

!!! note "为什么要创建虚拟环境"
    conda 的虚拟环境提供了环境隔离。不同用户可以使用不同的包，同一用户也可以使用不同版本的包。这样可以保证用户之间、环境和环境之间互相不冲突。

用户可以根据我们本教程来创建自己的环境，也可以使用我们已经安装好的基础环境（base）。基础环境包含了常见的数据科学库，包括mpi4py、numpy 、 pandas 、 scikit-learn 、 xgboost等。

使用 `conda list -n base` 查看基础环境已经安装的包。

普通用户无root权限，无法在基础环境（base）上安装或修改包。用户将常用包告知我们，我们统一安装，也可以根据需要创建自己的环境，或从基础环境上克隆出自己的环境再进行修改：

```bash
conda create --name test_env_2 --clone base
```

删除环境：

```bash
conda remove --name test_env_2 --all
```

!!! warning "注意！"
    如不激活任何环境，系统使用默认的基础环境（base）。有时会因为忘记执行激活用户环境的命令，导致程序报错，这时应该检查自己是否激活正确的环境。

# 在调度系统中提交 Python 作业

准备好一个 Python 程序，程序使用 xgboost 算法对 iris 数据集进行分类预测，输出准确度：

```python
from sklearn.datasets import load_iris
import xgboost as xgb
from xgboost import plot_importance
from matplotlib import pyplot as plt
from sklearn.model_selection import train_test_split

# read in the iris data
iris = load_iris()

X = iris.data
y = iris.target

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=1234565)

params = {
    'booster': 'gbtree',
    'objective': 'multi:softmax',
    'num_class': 3,
    'gamma': 0.1,
    'max_depth': 6,
    'lambda': 2,
    'subsample': 0.7,
    'colsample_bytree': 0.7,
    'min_child_weight': 3,
    'silent': 1,
    'eta': 0.1,
    'seed': 1000,
    'nthread': 4,
}

plst = params.items()
dtrain = xgb.DMatrix(X_train, y_train)
num_rounds = 500
model = xgb.train(plst, dtrain, num_rounds)

# 对测试集进行预测
dtest = xgb.DMatrix(X_test)
ans = model.predict(dtest)

# 计算准确率
cnt1 = 0
cnt2 = 0
for i in range(len(y_test)):
    if ans[i] == y_test[i]:
        cnt1 += 1
    else:
        cnt2 += 1

print("Accuracy: %.2f %% " % (100 * cnt1 / (cnt1 + cnt2)))

# 显示重要特征
#plot_importance(model)
#plt.show()
```

编写 PBS 脚本如下：

```bash
#PBS -N iris_classification_example
### use one node for this job
#PBS -l nodes=1
#PBS -q default
#PBS -o iris.stdout
#PBS -e iris.stderr

cd $PBS_O_WORKDIR
### set python in anaconda and 'test_evn_2' environment
module load anaconda/5.3.0
source activate test_env_2

python3 iris_with_xgb.py
```

[1]: https://www.anaconda.com/

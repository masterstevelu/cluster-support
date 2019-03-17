# 机器学习

机器学习(Machine Learning)是一门多领域交叉学科，涉及概率论、统计学、凸优化分析等多门学科，是人工智能的核心。本文将介绍集群所支持的机器学习工具。

## scikit-learn

scikit-learn是一款非常经典的基于 Python 的机器学习工具，它提供了常用机器学习算法、数据预处理和特征提取、模型评估等工具，现在已经提供了[中文文档][1]。用户可以使用它来进行监督学习如 Logitstic Regression 、 Support Vector Machine 、决策树等；进行无监督学习如聚类等。同时，它提供的数据预处理和特征提取模块和模型效果评估模块非常实用。

目前我们在 Anaconda 的 `base` ，`python36` 和 `python27` 环境均已安装了 scikit-learn 。

```bash
module load anaconda/5.3.0
```

在你的 Python 代码中使用：

```python
import sklearn
```

## XGBoost

XGBoost 一种是梯度提升决策树的实现，在算法上它做了正则化和剪枝处理。它能充分利用多核并行，速度极快，相比深度学习，它不需要大量的GPU计算资源。因其以上的有点，xgboost现在被大量应用于数据挖掘和机器学习领域。用户可以在其提供的[文档][2]中学习[决策树相关知识][4]以及xgboost的具体使用方法。

### Python
在你的 Python 代码中使用：

```python
import xgboost
```

### R
* [XGBoost R tutorial][5]

## LightGBM

与 XGBoost 类似，LightGBM 也是一种梯度提升树模型的实现。其使用方法可参考[官方文档][3]。

在你的 Python 代码中使用：

```python
import lightgbm
```

## TensorFlow

TensorFlow 是 Google 开源的深度学习框架，被广泛应用于计算机视觉、自然语言处理、推荐系统等场景。

TensorFlow 所依赖操作系统版本要求较高，目前可以使用 Singularity 容器，在容器中启动你的深度学习作业，具体做法请参看我们提供的[文档](./singularity.md)。

## PyTorch

PyTorch 是 Facebook 开源的深度学习框架，其设计理念更加先进，深受各大科研机构所欢迎。

同样，PyTorch 所依赖操作系统版本要求较高，目前可以使用 Singularity 容器，在容器中启动你的深度学习作业，具体做法请参看我们提供的[文档](./singularity.md)。

## Prophet

[Prophet][6] 是一个由 Facebook 开源的专门针对时间序列预测框架。

### Python
目前 Prophet 只支持 Python 3.6，我们安装在了 Anaconda 的 `python36` 环境里。

```bash
module load anaconda/5.3.0
source activate python36
```

在你的 Python 代码中使用：

```python
from fbprophet import Prophet
```

### R
R语言用户要加载 Anaconda 环境：

```bash
module load anaconda/5.3.0
```

在你的 R 代码中使用：

```r
library(prophet)
```

[1]: http://sklearn.apachecn.org/
[2]: https://xgboost.readthedocs.io/en/latest/
[3]: https://lightgbm.readthedocs.io/en/latest/
[4]: https://xgboost.readthedocs.io/en/latest/tutorials/model.html
[5]: https://xgboost.readthedocs.io/en/latest/R-package/xgboostPresentation.html
[6]: https://facebook.github.io/prophet/

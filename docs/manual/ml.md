# 机器学习

机器学习(Machine Learning)使用计算机对数据进行统计建模，是一门涉及统计学、凸优化分析、高性能计算跨学科技术，是人工智能技术的核心。机器学习的应用领域非常广泛，几乎所有包含数据的场景都可以使用机器学习技术来进行优化和研究。本文将介绍集群所支持的机器学习工具。以下软件均需加载 Anaconda。

```bash
module load anaconda/5.3.0
```

部分包安装在了不同的环境上，使用前请先激活相应的环境。如 `source activate python36`激活 python36 环境。

## pandas

pandas 可以轻松地从SQL数据库、CSV、Excel、JSON文件等不同来源获取数据，并对数据进行操作。

已安装在：base、python36、python27

## scikit-learn

scikit-learn是一款非常经典的基于 Python 的机器学习工具，它提供了常用机器学习算法、数据预处理和特征提取、模型评估等工具，现在已经提供了[中文文档][1]。用户可以使用它来进行监督学习如 Logitstic Regression 、 Support Vector Machine 、决策树等；进行无监督学习如聚类等。同时，它提供的数据预处理和特征提取模块和模型效果评估模块非常实用。

已安装在：base、python36、python27

## 概率编程

概率编程可以灵活创建自定义概率模型，主要关注从数据中洞悉和学习。这种方法本质上是贝叶斯方法，我们可以指定先验来告知和约束我们的模型，并得到后验分布形式的不确定性估计。使用MCMC采样算法，我们可以从后验中抽样灵活地估计这些模型。PyMC3和Stan是目前用来构建并估计这些模型最先进的工具。


### PyMC3

[pymc3][9]

已安装在：base、python36

### PyStan

[PyStan: The Python Interface to Stan][8]

已安装在：python36

## 决策树

### XGBoost

XGBoost 一种是梯度提升决策树的实现，在算法上它做了正则化和剪枝处理。它能充分利用多核并行，速度极快。相比深度学习，它不需要大量的GPU计算资源。因其以上的特点，xgboost现在被大量应用于数据挖掘和机器学习领域。用户可以在其提供的[文档][2]中学习[决策树相关知识][4]以及XGBoost的具体使用方法。

#### Python

已安装在：base、python36

#### R

* [XGBoost R tutorial][5]

已安装在：base

### LightGBM

与 XGBoost 类似，LightGBM 也是一种梯度提升树模型的实现，由 Microsoft 开发并开源。其使用方法可参考[官方文档][3]。

已安装在：base、python36

## 深度神经网络

### TensorFlow

TensorFlow 是 Google 开源的深度学习框架，被广泛应用于计算机视觉、自然语言处理、推荐系统等各类人工智能场景。

TensorFlow 所依赖操作系统版本要求较高，目前可以使用 Singularity 容器，在容器中启动你的深度学习作业，具体做法请参看我们提供的[文档](./singularity.md)。

### PyTorch

PyTorch 是 Facebook 开源的深度学习框架，其设计理念更加先进，深受各大科研机构所欢迎。

同样，PyTorch 所依赖操作系统版本要求较高，目前可以使用 Singularity 容器，在容器中启动你的深度学习作业，具体做法请参看我们提供的[文档](./singularity.md)。

### Theano

经典深度学习框架，由于各种原因，已不再更新。一些其他包可能还依赖 theano 。

已安装在：python36

## 统计建模

### Statsmodels

[Statsmodels][10] 是一款统计建模和计量经济学工具 Python 包。

已安装环境：python36

### Prophet

[Prophet][6] 是一个由 Facebook 开源的时间序列预测框架。

#### Python

已安装在：python36

#### R

已安装在：base

## 自然语言处理

### Gensim

[Gensim][7]是一款 Python 自然语言处理工具，用于从原始的非结构化的文本中，无监督地学习到文本隐层的主题向量表达。它支持包括TF-IDF，LSA，LDA，和word2vec在内的多种主题模型算法，支持流式训练，并提供了诸如相似度计算，信息检索等一些常用任务的API接口。

已安装在：base、python36

### BERT

自然语言处理最前沿的技术当属 Google 开源的 [BERT][11] 以及各类预训练模型。

已安装在：base、python36

[1]: http://sklearn.apachecn.org/
[2]: https://xgboost.readthedocs.io/en/latest/
[3]: https://lightgbm.readthedocs.io/en/latest/
[4]: https://xgboost.readthedocs.io/en/latest/tutorials/model.html
[5]: https://xgboost.readthedocs.io/en/latest/R-package/xgboostPresentation.html
[6]: https://facebook.github.io/prophet/
[7]: https://radimrehurek.com/gensim/
[8]: https://pystan.readthedocs.io/en/latest/
[9]: https://docs.pymc.io/
[10]: https://www.statsmodels.org/stable/
[11]: https://github.com/google-research/bert

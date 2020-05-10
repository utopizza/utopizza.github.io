# 特征工程


特征工程（Feature Engineering）：利用领域知识和现有数据，构造出需要的特征，用于机器学习算法【参考维基百科：[Feature engineering][1]】。

俗话说，数据与特征工程决定了模型的上限，改进算法只是逼近这个上限。可见特征工程在机器学习领域里的地位之重要。针对数据挖掘以及传统的机器学习，通过人们的总结和归纳，特征工程主要包括以下方面：

![](/2018-03-17-机器学习-特征工程/1.jpg)

目前比较流行的方案是借助一个强大的 python 类库—— sklearn 来实现特征工程。它的[官方文档][2]是教材级别的文档，值得深入学习。言归正传，下面开始总结特征工程的方法。

一、数据预处理（参考sklearn官方文档 [Preprocessing data][3]）

1、无量纲化：使不同规格的数据转换到同一规格。常用的方法有标准化、缩放化、正则化三种，它们的具体区别参见：[归一化/标准化/正则化][4]

(1)、标准化：减去均值并除以标准差。公式表达为：

$$x_{new}=\frac{x-\bar{x}}{S}$$

使用 sklearn 的 StandardScaler 实现：

```
from sklearn.preprocessing import StandardScaler

data_new = StandardScaler().fit_transform(data)
```

(2)、缩放化：常用最大最小值进行缩放，把特征缩放到区间 [0，1] 内，公式表达为：

$$x_{new}=\frac{x-x_{min}}{x_{max}-x_{min}}$$

使用 skearn 的 MinMaxScaler 实现：

```
from sklearn.preprocessing import MinMaxScaler

data_new = MinMaxScaler().fit_transform(data)
```

(3)、正则化：对每个样本计算其 p-范数，然后对该样本中每个元素除以该范数，这样处理的结果是使得每个处理后样本的 p-范数（L1-norm，L2-norm）等于1。其目的在于样本向量在点乘运算或其他核函数计算相似性时，拥有统一的标准，也就是说都转化为“单位向量”。在 L2 范数下正则化公式表达为：

$$x_{new}=\frac{x}{||x||_{2}}$$

使用 sklearn 的 Normalizer 实现：

```
from sklearn.preprocessing import Normalizer

data_new = Normalizer().fit_transform(data)
```

2、二值化：设定一个阈值，大于阈值的赋值为1，小于等于阈值的赋值为0，公式表达如下：

$$x_{new} = 
\begin{cases} 
1, & \text {if $x > threshold$} \\ 
0, & \text{if $x \leq threshold$} 
\end{cases}$$

使用 sklearn 的 Binarizer 实现：

```
from sklearn.preprocessing import Binarizer

data_new = Binarizer(threshold=3).fit_transform(data)
```

3、独热编码：常用于类别特征（category feature）的处理，把如“男/女”这样的字符型特征转化为数值类型特征。思想是使用 N 位状态寄存器来对 N 个状态进行编码，每个状态都由他独立的寄存器位，并且在任意时候，其中只有一位有效。详情参见：[数据预处理之独热编码][5]。使用 sklearn 的 OneHotEncoder 实现：

```
from sklearn.preprocessing import OneHotEncoder

data_new = OneHotEncoder().fit_transform(data)
```

4、缺失值处理：缺失值的填补方法有多种，中位数填补、众数填补、均值填补、临近数填补。采用怎么样的填补方法，或者直接放弃该特征，需视具体情况而定。可参考：[浅谈数据挖掘中的数据处理（缺失值处理以及异常值检测）][6]。使用 sklearn 的 Imputer 实现：

```
from sklearn.preprocessing import Imputer

data_new = Imputer(strategy='mean').fit_transform(data)
```

5、构造多项式特征：假设有数据集有两个特征 $[X_1, X_2]$，则可以利用 sklearn 的 PolynomialFeatures 构造二次多项式特征如 $[1,X_1,X_2,X_1^2,X_1*X_2,X_2^2]$。也可以构造更高次数的多项式特征，只需要调整参数即可。

```
from sklearn.preprocessing import PolynomialFeatures

data_new = PolynomialFeatures(2).fit_transform(data) 
```

6、函数转换：利用 Python 的函数对特征进行转换。可以用 sklearn 的 FunctionTransformer 实现：

```
import numpy as np
from sklearn.preprocessing import FunctionTransformer

data_new = FunctionTransformer(np.log1p).transform(X)
```

二、特征选择（参考sklearn官方文档 [Feature selection][7]）

1、Filter：过滤法或称选择法，按照发散性或者相关性对各个特征进行评分，设定阈值或者待选择阈值的个数，选择特征。

(1)、方差选择法：先计算各个特征的方差，然后根据阈值，选择方差大于阈值的特征。这样可以去掉一些方差特别小的特征如常量特征，这种特征对于模型的训练和预测不会有太多贡献。方差的数学公式表达：

$$\delta^{2}=\frac{(X-\mu)^2}{N}$$

使用 sklearn 的 VarianceThreshold 实现：

```
from sklearn.feature_selection import VarianceThreshold

data_new = VarianceThreshold(threshold=3).fit_transform(data)
```

(2)、Pearson 相关系数选择法：计算各个特征对目标值的相关系数以及相关系数的P值，然后选择相关性最高的 k 个。Pearson 相关系数是用两个变量的协方差除以两个变量的标准差得到的，详情参考 [知乎：如何理解皮尔逊相关系数][8] 以及 [维基百科：皮尔逊积矩相关系数][9]。数学公式表达：

$$P(X,Y)
=\frac{Cov(X,Y)}{\sigma_X \sigma_Y}
=\frac{E[(X-\mu_{X})(Y-\mu_{Y})]}{\sigma_X \sigma_Y}
$$

使用 sklearn 的 SelectKBest 与 scipy 的 pearsonr 结合实现：

```
from sklearn.feature_selection import SelectKBest
from scipy.stats import pearsonr

data_new = SelectKBest(lambda X, Y: array(map(lambda x:pearsonr(x, Y), X.T)).T, k=2).fit_transform(data, target)
```

(3)、卡方检验法：经典的卡方检验（Chi-Square）是检验定性自变量对定性因变量的相关性，详情参考：[卡方检验][10]。使用 sklearn 的 SelectKBest 与 chi2 结合实现：

```
from sklearn.feature_selection import SelectKBest
from sklearn.feature_selection import chi2

X_new = SelectKBest(chi2, k=2).fit_transform(X, y)
```

(4)、互信息法：皮尔逊系数只能衡量线性相关性，而互信息系数能够很好地度量各种相关性，但是计算相对复杂一些。互信息是用来评价一个事件的出现对于另一个事件的出现所贡献的信息量，可用来评价定性自变量对定性因变量的相关性，详情参考[特征选择方法之互信息][11]。数学描述为：

$$I(X,Y)=\sum_{x \in X}\sum_{y \in Y} p(x,y) \log \frac{p(x,y)}{p(x)p(y)}$$

使用 sklearn 的 SelectKBest 与 minepy 的 MINE 结合实现：

```
from sklearn.feature_selection import SelectKBest
from minepy import MINE

def mic(x, y):
    m = MINE()
    m.compute_score(x, y)
    return (m.mic(), 0.5)

X_new = SelectKBest(lambda X, Y: array(map(lambda x:mic(x, Y), X.T)).T, k=2).fit_transform(X, Y)
```

2、Wrapper：包装法，根据目标函数（通常是预测效果评分），每次选择若干特征，或者排除若干特征。

(1)、递归特征消除：使用一个基模型来进行多轮训练，每轮训练后，模型选择 importance 排在前列的若干特征构建新的特征集，再基于新的特征集进行下一轮训练，如此重复直至特征个数达到指定的数量。使用 sklearn 的 RFE 类实现如下，这里使用逻辑斯蒂回归作为基模型，也可以使用 sklearn 中其他的模型如 svm 等等：

```
from sklearn.feature_selection import RFE
from sklearn.linear_model import LogisticRegression

X_new = RFE(estimator=LogisticRegression(), n_features_to_select=2).fit_transform(X, Y)
```

3、Embedded：嵌入法，先使用某些机器学习的算法和模型进行训练，得到各个特征的权值系数，根据系数从大到小选择特征。类似于Filter方法，但是是通过训练来确定特征的优劣。

(1)、基于正则项的特征选择法：使用带正则项的基模型，除了筛选出特征外同时也进行了降维，例如使用具有特征稀疏能力的 L1 范数作为正则项（[知乎：l1 相比于 l2 为什么容易获得稀疏解？][12]）。使用 sklearn 的 SelectFromModel 结合带 L1 正则项的逻辑回归模型实现：

```
from sklearn.feature_selection import SelectFromModel
from sklearn.linear_model import LogisticRegression

X_new = SelectFromModel(LogisticRegression(penalty="l1", C=0.1)).fit_transform(X, Y)
```

(2)、基于树模型的特征选择法：树模型中GBDT也可用来作为基模型进行特征选择。使用 sklearn 的 SelectFromModel 结合 GradientBoostingClassifier 实现：

```
from sklearn.feature_selection import SelectFromModel
from sklearn.ensemble import GradientBoostingClassifier

X_new = SelectFromModel(GradientBoostingClassifier()).fit_transform(X, Y)
```

三、降维

当特征选择完成后，可以直接训练模型了，但是可能由于特征矩阵过大，导致计算量大，训练时间长的问题，因此降低特征矩阵维度也是必不可少的。常见的降维方法除了以上提到的基于 L1 正则项的模型以外，另外还有主成分分析法（PCA）和线性判别分析（LDA），线性判别分析本身也是一个分类模型。PCA 和 LDA 有很多的相似点，其本质是要将原始的样本映射到维度更低的样本空间中，但是 PCA 和 LDA 的映射目标不一样：[PCA 是为了让映射后的样本具有最大的发散性；而 LDA 是为了让映射后的样本有最好的分类性能][13]。所以说 PCA 是一种无监督的降维方法，而 LDA 是一种有监督的降维方法。

1、主成分分析法（PCA）：使用 sklearn 的 PCA 实现

```
from sklearn.decomposition import PCA

X_new = PCA(n_components=2).fit_transform(X)
```

2、线性判别分析法（LDA）：使用 sklearn 的 LDA 实现

```
from sklearn.lda import LDA

X_new = LDA(n_components=2).fit_transform(X, Y)
```

四、特征交互（Feature interaction）

通过结合领域知识或者数据特点等等，利用多个特征组合成新的特征，例如

 1. 利用某个特征，构造多项式特征
 2. 把某个特征进行某些函数变换，例如取绝对值、平移、取对数等等
 2. 通过某几个特征的加减乘除运算、与或非等运算，或者各种 join 等操作得到的新特征
 3. 按某个特征进行 groupby 分组，把每一组作为新的特征，样本属于哪一组就在对应的特征用 1 表示，其余用 0 表示
 4. 用基因编程创造新特征
 5. 用决策树创造新特征

五、参考资料：

 1. [使用sklearn做单机特征工程][14]
 2. [sklearn学习——特征工程(特征选择)][15]
 3. [结合Scikit-learn介绍几种常用的特征选择方法][16]
 4. [知乎：机器学习中，有哪些特征选择的工程方法？][17]
 5. [机器学习特征工程实用技巧大全][18]
 6. [警惕「特征工程」中的陷阱][19]
 7. [特征选择， 经典三刀][20]


  [1]: https://en.wikipedia.org/wiki/Feature_engineering
  [2]: http://scikit-learn.org/stable/
  [3]: http://scikit-learn.org/stable/modules/preprocessing.html
  [4]: http://www.cnblogs.com/chaosimple/p/4153167.html
  [5]: http://blog.csdn.net/dulingtingzi/article/details/51374487
  [6]: http://www.cnblogs.com/xiaohuahua108/p/6237906.html
  [7]: http://scikit-learn.org/stable/modules/feature_selection.html
  [8]: https://www.zhihu.com/question/19734616
  [9]: https://zh.wikipedia.org/wiki/%E7%9A%AE%E5%B0%94%E9%80%8A%E7%A7%AF%E7%9F%A9%E7%9B%B8%E5%85%B3%E7%B3%BB%E6%95%B0
  [10]: https://zh.wikipedia.org/wiki/%E5%8D%A1%E6%96%B9%E6%A3%80%E9%AA%8C
  [11]: http://blog.csdn.net/liu_zhlai/article/details/53512939
  [12]: https://www.zhihu.com/question/37096933
  [13]: http://www.cnblogs.com/LeftNotEasy/archive/2011/01/08/lda-and-pca-machine-learning.html
  [14]: http://www.cnblogs.com/jasonfreak/p/5448385.html
  [15]: http://blog.csdn.net/sqiu_11/article/details/59487177
  [16]: http://www.17bigdata.com/%E7%BB%93%E5%90%88scikit-learn%E4%BB%8B%E7%BB%8D%E5%87%A0%E7%A7%8D%E5%B8%B8%E7%94%A8%E7%9A%84%E7%89%B9%E5%BE%81%E9%80%89%E6%8B%A9%E6%96%B9%E6%B3%95.html
  [17]: https://www.zhihu.com/question/28641663/answer/41653367
  [18]: https://zhuanlan.zhihu.com/p/26444240
  [19]: https://zhuanlan.zhihu.com/p/33651227
  [20]: https://zhuanlan.zhihu.com/p/24635014


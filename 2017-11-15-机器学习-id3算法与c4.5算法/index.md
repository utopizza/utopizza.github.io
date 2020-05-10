# ID3算法与C4.5算法



一、ID3算法

ID3算法的核心是在决策树各个结点上应用“信息增益”准则选择特征，递归地构建决策树。具体方法：从根结点开始，对结点计算所有可能的特征的信息增益，选择信息增益最大的特征作为结点的特征，由该特征的不同取值建立子结点；再对子结点递归地调用以上方法，构建决策树，直到所有特征的信息增益均很小或没有特征可以选择为止。


输入：训练数据集 $D$，特征集 $A$，阈值 $\varepsilon$
输出：决策树 $T$

 1. 若 $D$ 中所有实例属于同一类 $C_k$，则 $T$ 为单结点树，并将 $C_k$ 作为该结点的类标记，返回 $T$；
 2. 若 $A=\emptyset$ ，则 $T$ 为单结点树，将 $D$ 中实例数最大的类 $C_k$ 作为该结点的类标记，返回 $T$；
 3. 遍历 $A$ 中每一个特征，[计算信息增益][1] $g(D,A)=H(D)-\sum_{i=1}^{n}\frac{|D_i|}{|D|}H(D_i)$，选择信息增益最大特征 $A_g$。如果 $A_g$ 的信息增益小于阈值 $\varepsilon$，则置 $T$ 为单结点树，并将 $D$ 中实例数最大的类 $C_k$ 作为该结点的类标记，返回 $T$；否则，对 $A_g$ 的每一个可能值 $a_i$，按照 $A_g=a_i$ 将 $D$ 分割为若干非空子集 $D_i$，对每个子集 $D_i$，将其实例数中最大的类作为标记，构建子结点，由结点及其子结点构成树 $T$，返回 $T$；
 4. 对第 $i$ 个子结点，以 $D_i$ 为训练集，以 $A- \{ A_g \}$ 为特征集，递归地调用前3步，得到子树 $T_i$，返回 $T_i$。

以下是部分关键代码，参考《机器学习实战》第三章。这里忽略了上述算法的“信息增益阈值 $\varepsilon$ 这一条件。

```
def createTree(dataSet, labels):
    
    # 1、若所有实例属于同一类，返回该类
    classList = [example[-1] for example in dataSet]
    if classList.count(classList[0]) == len(classList):
        return classList[0]
    
    # 2、若特征值集合为空集，则返回样本类别中最多的那个类别
    if len(dataSet[0]) == 1:
        return majorityCnt(classList)
        
    # 3、遍历特征值集合中的每一个特征，找出信息增益最大的特征
    bestFeat = chooseBestFeatureToSplit(dataSet)
    bestFeatLabel = labels[bestFeat]
    
    # 4、以该特征的label建立结点，并从特征集合中删去该特征
    myTree = {bestFeatLabel: {}}
    del (labels[bestFeat])
    
    # 5、收集该特征的所有值，去掉重复值
    featValues = [example[bestFeat] for example in dataSet]
    uniqueVals = set(featValues)
    
    # 6、对每一种取值，递归调用本函数进行决策树的构建
    for value in uniqueVals:
        subLabels = labels[:]
        myTree[bestFeatLabel][value] = createTree(splitDataSet(dataSet, bestFeat, value), subLabels)
    
    return myTree
```

二、C4.5算法

C4.5算法与ID3算法基本相同，唯一不同的地方只是在生成决策树的过程中，使用“信息增益比”而不是直接使用“信息增益”来选择特征。

  [1]: https://yushengwxxx.github.io/2017/11/11/2017-11-11-%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0-%E4%BF%A1%E6%81%AF%E5%A2%9E%E7%9B%8A/


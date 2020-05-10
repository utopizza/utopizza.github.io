# CART算法


 
分类与回归树（classification and regression tree, CART）模型是应用广泛的决策树学习方法，**既可以用于分类，也可以用于回归**。

回顾之前学习的 ID3 算法，它的做法是每次选取当前最佳的特征来分割数据，分割依据是该特征的所有取值。也就是说，如果这个特征有 $n$ 个取值，那么数据将立即被分割成 $n$ 分。一旦被切分之后，该特征在之后将不会再被考虑。这样有两个问题：一是切分过于迅速，如果 $n$ 很大，那么数据将被马上切分成大量的小份，影响后续切分的效果。二是，这样的决策树只能处理离散型的数值，如果特征的取值是连续型数值，则需要将其预处理转化成离散型，但这样就会人为地破坏了连续型变量的内在性质。

CART 算法解决了上述问题。CART 设定决策树是**二叉树**，每次切分数据只做二元切分，即把数据集切分成两份。对某个连续型的特征，设定一个切分值，大于此值的数据进入左子树，反之则进入右子树。然后递归地构建子树。

一、CART 生成

CART 决策树的生成就是递归地构建二叉决策树的过程。对回归树用**平方误差最小化**准则，对分类树用**基尼指数最小化**准则，进行特征选择。

1、回归树的生成

输入：训练数据集 $D$，特征集 $A$
输出：回归树 $T$

(1)、寻找最佳的切分特征 $A_g$ 和对应的最佳切分值 $v$：遍历特征集中每一个特征，尝试以该特征的每一个取值作为切分值，对数据集执行二元切分，然后计算切分误差，如果当前误差小于记录的最小误差，那么更新最优切分特征、最优切分值、最小误差。遍历完成后，返回最优切分特征、最优切分值。

其中误差一般使用目标变量的方差来计算，设数据集为 $D$，数据集中的目标变量为 $D.Y$，数据集在执行一次二元切分后被切分成 $D_{left}$ 和 $D_{right}$，则此次切分减小的混乱程度为

$$g(D,A_g,v)=var(D.Y)- \left[ var(D_{left}.Y)+var(D_{right}.Y) \right]$$

其中 $D_{left}=\{D \mid D.A_g>v\}$，$D_{right}=\{D \mid D.A_g \leq v\}$。

(2)、如果该结点不能再分（左右两个子集方差之和比原数据集方差还大，或者大于给定的某个阈值等等），将该结点存为叶结点，把该结点上的数据集**目标变量的平均值**作为该结点的输出值返回；否则执行二元切分，把数据集以 $v$ 为切分值，按特征 $A_g$ 切分成 $D_{left}$ 和 $D_{right}$ 两个子集，并对应地生成左右两个子树

(3)、递归地对左右子树重复执行前面的步骤，直到满足停止条件

构造决策树：

```
def createTree(dataSet,tolN,tolS):
    # 寻找最优切分特征、最优切分值
    feat, value = chooseBestSplit(dataSet,tolN,tolS)
    
    # 切分特征为空，说明不能再分，直接返回value作为叶结点的输出值
    if feat is None:
        return value
        
    # 把最优切分特征、最优切分值记录到树中
    retTree = {}
    retTree['spInd'] = feat
    retTree['spVal'] = value

    # 执行二元切分，得到左右子树对应的两个数据集子集
    leftSet, rightSet = binSplitDataSet(dataSet, feat, value)
    
    # 在左右子树继续递归地调用自己，构造决策树
    retTree['left'] = createTree(leftSet, leafType, errType, ops)
    retTree['right'] = createTree(rightSet, leafType, errType, ops)
    
    return retTree
```

选择最优切分特征、最优切分值：

```
def chooseBestSplit(dataSet,tolN,tolS):
    # 如果数据集的目标变量全部属于同一个值，则不用再切分
    # 此时返回空特征，并把数据集的目标变量（最后一列）的平均值作为返回值
    if len(set(dataSet[:, -1].T.tolist()[0])) == 1:
        return None, mean(dataSet[:, -1])
        
    # 求数据集的目标变量的总方差
    m, n = shape(dataSet)
    S = var(dataSet[:, -1]) * m

    # 初始化最小方差、最优特征、最优切分值
    bestS = inf
    bestIndex = 0
    bestValue = 0
    
    # 遍历每个特征的每个取值
    for featIndex in range(n - 1):
        for splitVal in set((dataSet[:, featIndex].T.A.tolist())[0]):
            
            # 执行一次二元切分尝试
            mat0, mat1 = binSplitDataSet(dataSet, featIndex, splitVal)

            # 计算两个子集的方差之和
            newS = var(mat0[:, -1]) * shape(mat0)[0] + var(mat1[:, -1]) * shape(mat1)[0]
            
            # 如果误差小于当前记录的最小误差，则记录这次切分
            if newS < bestS:
                bestIndex = featIndex
                bestValue = splitVal
                bestS = newS

    # 如果误差减小不大，小于给定的阈值 tolS，则不再切分
    if S - bestS < tolS:
        return None, mean(dataSet[:, -1])

    # 如果切分的两个子集的大小小于给定的阈值 tolN，则不再切分
    mat0, mat1 = binSplitDataSet(dataSet, bestIndex, bestValue)
    if shape(mat0)[0] < tolN or shape(mat1)[1] < tolN:
        return None, mean(dataSet[:, -1])

    # 返回最优切分特征和最优切分值
    return bestIndex, bestValue
```

执行一次二元切分：

```
def binSplitDataSet(dataSet, feature, value):
    mat0 = dataSet[nonzero(dataSet[:, feature] > value)[0], :]  # [0]
    mat1 = dataSet[nonzero(dataSet[:, feature] <= value)[0], :]  # [0]
    return mat0, mat1
```

2、分类树的生成

分类树用基尼指数选择最优特征，同时决定该特征的最优切分值。

基尼指数：分类问题中，假设有 $K$ 个类，样本点属于第 $k$ 类的概率为 $p_k$ ，则概率分布的基尼指数为：

$$Gini(p)=\sum_{k=1}^{K}p_k(1-p_k)=1-\sum_{k=1}^{K}p_{k}^{2}$$

对于给定的样本集合 $D$，其基尼指数为：

$$Gini(D)=1-\sum_{k=1}^{K} \left( \frac{|C_k|}{|D|} \right)^2$$

这里，$C_k$ 是 $D$ 中属于第 $k$ 类的样本子集，$K$ 是类的个数。

设集合 $D$ 被特征 $A_g$ 的某个取值 $v$ 二元切分，得到两个子集 $D_{left}$ 和 $D_{right}$，则在特征 $A_g$ 和切分值 $v$ 的条件下，集合 $D$ 的基尼指数定义为

$$
Gini(D,A_g,v)=
\frac{|D_{left}|}{|D|}Gini(D_{left})+
\frac{|D_{right}|}{|D|}Gini(D_{right})
$$

其中 $D_{left}=\{D \mid D.A_g>v\}$，$D_{right}=\{D \mid D.A_g \leq v\}$。

基尼指数 $Gini(D)$ 表示集合 $D$ 的不确定性，基尼指数 $Gini(D,A_g,v)$ 表示按特征 $A_g$ 的值 $v$ 二元切分后的不确定性。基尼指数越大，样本集合的不确定性越大，这一点和熵类似。

输入：训练数据集 $D$，停止计算的条件
输出：CART 决策树

根据训练数据集，从根节点开始，递归地对每个结点进行一下操作：

(1)、设结点的数据集为 $D$，计算 $Gini(D)$。然后遍历每一个特征的每一个取值，尝试二元切分，并计算 $Gini(D,A_g,v)$，找出基尼指数最小的特征及其对应的切分值，作为最优切分特征和最优切分值。

(2)、把数据集 $D$ 按照最优切分特征和最优切分值进行二元切分，得到两个子集，并对应地从本结点生成两个子结点，将两个子集分配到对应的结点中去。

(3)、对左右子树递归地调用前面步骤，直至满足停止条件。算法的停止条件是结点中的样本个数小于预定阈值，或样本集的基尼指数小于预定阈值，或者没有更多特征。这里需要注意，分类树的叶子结点不是像回归树那样取叶子结点所对应的数据集的目标变量的均值，而是像 ID3 算法那个，选择叶子结点对应的数据集中实例数最大的那个类别作为叶子结点的类别返回。

二、CART 剪枝

如果一棵决策树的结点过多，表明该模型可能对数据进行了“过拟合”。要分析是否确实发生了过拟合，可以使用交叉验证来判断。另一方面，可以在构造决策树的时候采取一些措施来防止发生过拟合，降低决策树模型的复杂度，此称为决策树的剪枝（pruning）。决策树的剪枝有两种方式，一种是预剪枝（prepruning），另一种是后剪枝（postpruning）。

1、预剪枝

预剪枝实际上就是在决策树构建过程中加入算法的停止条件。具体来说，就是前面所述的例子中，在选择最优特征最优切分点的函数 $chooseBestSplit()$ 中加入的停止条件： $tolS$ 和 $tolN$ 两个参数。这两个参数所表达的意义是，对某个结点的数据集，如果二元切分之后的两个子集总误差比原数据集误差下降很小，或者子集的大小太小，那么不对这个结点进行切分，直接把它当作一个叶子结点返回，这样就阻止了决策树在这个结点上的过分生长，简化了决策树。实验证明，决策树生成算法对这两个参数非常敏感。但是这样也会有一个缺点，需要不断的人为地调整这两个参数来达到好的结果，这并不是一个好的办法。

2、后剪枝

后剪枝方法需要将数据分成测试集和训练集。首先用训练集生成长一棵足够大足够复杂的决策树，然后从上而下找到叶子结点，用测试集来判断将这些叶子结点合并是否能降低测试误差。如果是就合并。剪枝算法相关代码如下：

```
# 从叶子结点到根结点坍塌树，把每棵子树根结点替换成左右子树的均值
def getMean(tree):
    if isTree(tree['right']):
        tree['right'] = getMean(tree['right'])
    if isTree(tree['left']):
        tree['left'] = getMean(tree['left'])
    return (tree['left'] + tree['right']) / 2

# 剪枝算法
def prune(tree, testData):
    # 若测试集大小为0，对本子树进行坍塌，收缩到一个值返回
    if shape(testData)[0] == 0:
        return getMean(tree)

    # 若本结点有左右子树，则把测试集按本结点进行二元划分得到左右子集
    if isTree(tree['right']) or isTree(tree['left']):
        leftSet, rightSet = binSplitDataSet(testData, tree['spInd'], tree['spVal'])

    # 如果本结点有左子树，用测试集的左子集对该子树剪枝
    if isTree(tree['left']):
        tree['left'] = prune(tree['left'], leftSet)
    
    # 如果本结点有右子树，用测试集的右子集对该子树剪枝
    if isTree(tree['right']):
        tree['right'] = prune(tree['right'], rightSet)

    # 若本结点既无左子树，也无右子树
    # 仍然按本结点把测试集进行二元划分
    if not isTree(tree['left']) and not isTree(tree['right']):
        leftSet, rightSet = binSplitDataSet(testData, tree['spInd'], tree['spVal'])
        
        # 分别计算不合并和合并的误差
        errorNoMerge = sum(power(leftSet[:, -1] - tree['left'], 2)) + sum(power(rightSet[:, -1] - tree['right'], 2))
        treeMean = (tree['left'] + tree['right']) / 2.0
        errorMerge = sum(power(testData[:, -1] - treeMean, 2))
        
        # 确定是否合并
        if errorMerge < errorNoMerge:
            print "merging"
            return treeMean
        else:
            return tree
    else:
        return tree
```


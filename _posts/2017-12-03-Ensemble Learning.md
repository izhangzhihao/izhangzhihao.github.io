---
layout: post
title: 组合学习
categories: 机器学习
description: 组合学习中的 Bagging & Boosting
keywords: 组合学习, Bagging, Boosting, 决策树, 随机森林
---

# 模型组合

如果我们已经训练好了多个模型，并且希望通过已有的这些模型组合成一个更强的模型，那么我们可能会有以下选择：

* 将模型应用在验证集上，选择表现最好的
* 多个模型投票或者取均值
* 多个模型做加权平均

可以看到这些都是我们做模型组合的一些思路，所谓模型组合即组合多个模型以获得更好的效果，使组合的模型具有更强的泛化能力。这里所说更强的泛化能力在机器学习无非两种即**降低Bias或降低Variance**。Bias度量了某种学习算法的平均估计结果所能逼近学习目标(目标输出)的程度，即Bias越低，模型的误差越低；Variance则度量了模型对于不同的数据集模型估计结果发生变动的程度，可以理解为模型稳定性的一个度量方式，Variance越低说明模型越健壮，越稳定，反之说明模型不稳定，对于不同的数据集表现差别很大，也就是很有可能发生过拟合了，所以说一个好的模型不仅要有很低的误差(Bias),还需要对不同的数据集表现稳定（低的Variance）。下面我们去看看两种降低Bias和Variance的方法。

## Bagging

Bagging全名Boostrap Aggregration，在这里并不是书包的意思，其中Bootstrap是一种**有放回的抽样方法**，其抽样策略就是简单随机抽样。Bagging的思路就是**让学习算法训练多轮，每轮的训练集由从初始的训练集中随机取出的n个训练样本组成，初始训练例在某轮训练集中可以出现多次或根本不出现训练之后可得到一个预测函数序列h1,h2 ⋯ ⋯ hn 最终的预测函数H对分类问题采用投票方式，对回归问题采用简单平均方法对新示例进行判别。这种方法可以说是再简单不过了，但是在应用中却往往特别高效，基于Bagging思想的随机森林模型一直都是公认泛化能力很强的分类器之一。

## Boosting

Boosting顾名思义，提升方法，将多个弱学习模型提升为强学习模型。初始化时对每一个训练例赋相等的权重1／n，然后用该学算法对训练集训练t轮，每次训练后，对训练失败的训练例赋以较大的权重，也就是让学习算法在后续的学习中集中对比较难的训练铡进行学习，从而得到一个预测函数序列h1,h2 ⋯ ⋯ hn其中每一个hi都有一定的权重，预测效果好的预测函数权重较大，反之较小。最终的预测函数H对分类问题采用有权重的投票方式，对回归问题采用加权平均的方法对新示例进行判别。基于Boosting的方法主要有Adaboost和梯度提升决策树(Gradient Boosted Decision Tree, GBDT)。

## Bagging和Boosting的对比

从Bagging和Boosting的定义中很容易看到，Bagging的特点在于其抽样的随机化，每一轮的训练是独立的。而Boosting每一轮训练取决于其上一轮的结果，每一轮的训练是相互关联的，因此Bagging很容易实现**并行化**而Boosting却无法做到并行化。另外Bagging采用的是投票机制，Boosting采用的是加权方式。Boosting通过反复迭代将弱学习模型提升为强学习模型，因此通过Boosting方法可以降低模型的Bias；而Bagging通过反复的抽样训练新的模型，实际上是对已有强学习模型取平均，并不能起到降低Bias的效果，而是使得模型稳定性加强，降低了Variance。因此基于Bagging和Boosting的方法都可以起到增加模型泛化能力的作用。

## 相对熵

相对熵（relative entropy）又称为KL散度（Kullback–Leibler divergence，简称KLD），信息散度（information divergence），信息增益（information gain）。
KL散度是两个概率分布P和Q差别的非对称性的度量。 KL散度是用来度量使用基于Q的编码来编码来自P的样本平均所需的额外的位元数。 典型情况下，P表示数据的真实分布，Q表示数据的理论分布，模型分布，或P的近似分布。期望信息越小，信息增益越大，从而纯度越高。

## 决策树

决策树学习采用的是**自顶向下**的递归方法, 其基本思想是以**信息熵**为度量构造一棵**熵值下降最快**的树,到叶子节点处的熵值为零, 此时每个叶节点中的实例都属于同一类。

最大优点: 可以自学习。在学习的过程中,不需要使用者了解过多背景知识,只需要对训练实例进行较好的*标注*,就能够进行学习，属于*监督学习*。

### 三种生成算法

* ID3 --- **信息增益**最大的准则
* C4.5 --- **信息增益比**最大的准则
* CART
    * 回归树: **平方误差**最小的准则
    * 分类树: **基尼系数**最小的准则

### 决策树的优缺点

* 优点: 决策树在训练集上容易获得较好的分类能力
* 缺点: 但对未知的测试数据未必有好的分类能力,*泛化*能力弱,即可能发生*过拟合*现象。
    * 剪枝
    * 随机森林

## 随机森林

随机森林能够解决决策树的过拟合问题。随机森林用训练集生成多个(**非常深的**)决策树。在预测时, 每个树的都会预测一个结果, 每个结果*加权表决*, 来避免过拟合。 例如, 如果你训练了3个树, 其中有2个树的结果是A, 1个数的结果是B, 那么最终结果会是A。

Random Forest classifiers work around that limitation by creating a whole bunch of decision trees (hence "forest") — each trained on **random subsets of training samples** (drawn with replacement) and features (drawn without replacement) — and have the decision trees work together to make a more accurate classification.


## 使用 scikit-learn

scikit-learn 决策树算法类库内部实现是使用了调优过的 CART 树算法，既可以做分类，又可以做回归。分类决策树的类对应的是 `DecisionTreeClassifier`，而回归决策树的类对应的是 `DecisionTreeRegressor`。两者的参数定义几乎完全相同，但是意义不全相同。下面就对 `DecisionTreeClassifier` 和 `DecisionTreeRegressor` 的重要参数做一个总结，重点比较两者参数使用的不同点和调参的注意点。

<table>
    <tbody>
    <tr>
        <td>参数</td>
        <td><span class="n">DecisionTreeClassifier</span></td>
        <td><span class="n"><span class="n">DecisionTreeRegressor</span></span></td>
    </tr>
    <tr>
        <td>
            <p>特征选择标准criterion</p>
        </td>
        <td>
            <p>可以使用"gini"或者"entropy"，前者代表基尼系数，后者代表信息增益。一般说使用默认的基尼系数"gini"就可以了，即CART算法。除非你更喜欢类似ID3,
                C4.5的最优特征选择方法。&nbsp;</p>
        </td>
        <td>&nbsp;可以使用"mse"或者"mae"，前者是均方差，后者是和均值之差的绝对值之和。推荐使用默认的"mse"。一般来说"mse"比"mae"更加精确。除非你想比较二个参数的效果的不同之处。</td>
    </tr>
    <tr>
        <td>
            <p>特征划分点选择标准splitter</p>
        </td>
        <td colspan="2">
            <p>可以使用"best"或者"random"。前者在特征的所有划分点中找出最优的划分点。后者是随机的在部分划分点中找局部最优的划分点。</p>
            <p>默认的"best"适合样本量不大的时候，而如果样本数据量非常大，此时决策树构建推荐"random"&nbsp;</p>
        </td>
    </tr>
    <tr>
        <td>
            <p>划分时考虑的最大特征数max_features</p>
        </td>
        <td colspan="2">
            <p>可以使用很多种类型的值，默认是"None",意味着划分时考虑所有的特征数；如果是"log2"意味着划分时最多考虑<span class="MathJax_Preview"
                                                                             style="color: inherit;"></span><span
                    class="MathJax" id="MathJax-Element-1-Frame" tabindex="0"
                    data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mi>l</mi><mi>o</mi><msub><mi>g</mi><mn>2</mn></msub><mi>N</mi></math>"
                    role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math"
                                                                                                   id="MathJax-Span-1"
                                                                                                   role="math"
                                                                                                   style="display: inline-block;"><span
                    style="display: inline-block; position: relative; height: 0px;"><span
                    style="position: absolute;"><span class="mrow" id="MathJax-Span-2"
                                                      style="display: inline-block;"><span class="mi"
                                                                                           id="MathJax-Span-3"
                                                                                           style="display: inline-block;"><img
                    src="http://mathjax.cnblogs.com/2_6_1/fonts/HTML-CSS/TeX/png/Math/Italic/476/006C.png?rev=2.6.1"
                    style="width: 6px; height: 16px; vertical-align: 0px;"></span><span class="mi" id="MathJax-Span-4"
                                                                                        style="display: inline-block;"><img
                    src="http://mathjax.cnblogs.com/2_6_1/fonts/HTML-CSS/TeX/png/Math/Italic/476/006F.png?rev=2.6.1"
                    style="width: 11px; height: 10px; vertical-align: 0px;"></span><span class="msubsup"
                                                                                         id="MathJax-Span-5"
                                                                                         style="display: inline-block;"><span
                    style="display: inline-block; position: relative; height: 0px;"><span
                    style="position: absolute;"><span class="mi" id="MathJax-Span-6" style="display: inline-block;"><img
                    src="http://mathjax.cnblogs.com/2_6_1/fonts/HTML-CSS/TeX/png/Math/Italic/476/0067.png?rev=2.6.1"
                    style="width: 11px; height: 14px; vertical-align: -4px;"></span><span
                    style="display: inline-block; width: 0px;"></span></span><span style="position: absolute;"><span
                    class="mn" id="MathJax-Span-7" style="display: inline-block; font-size: 70.7%;"><img
                    src="http://mathjax.cnblogs.com/2_6_1/fonts/HTML-CSS/TeX/png/Main/Regular/476/0032.png?rev=2.6.1"
                    style="width: 10px; height: 15px;"></span><span
                    style="display: inline-block; width: 0px;"></span></span></span></span><span class="mi"
                                                                                                 id="MathJax-Span-8"
                                                                                                 style="display: inline-block;"><img
                    src="http://mathjax.cnblogs.com/2_6_1/fonts/HTML-CSS/TeX/png/Math/Italic/476/004E.png?rev=2.6.1"
                    style="width: 20px; height: 15px;"></span></span><span
                    style="display: inline-block; width: 0px;"></span></span></span><span
                    style="display: inline-block; overflow: hidden; width: 0px;"></span></span></nobr><span
                    class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><mi>l</mi><mi>o</mi><msub><mi>g</mi><mn>2</mn></msub><mi>N</mi></math></span></span>
                <script type="math/tex" id="MathJax-Element-1">log_2N</script>
                个特征；如果是"sqrt"或者"auto"意味着划分时最多考虑<span class="MathJax_Preview" style="color: inherit;"></span><span
                        class="MathJax" id="MathJax-Element-2-Frame" tabindex="0"
                        data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><msqrt><mi>N</mi></msqrt></math>"
                        role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math"
                                                                                                       id="MathJax-Span-9"
                                                                                                       role="math"
                                                                                                       style="display: inline-block;"><span
                        style="display: inline-block; position: relative; height: 0px;"><span
                        style="position: absolute;"><span class="mrow" id="MathJax-Span-10"
                                                          style="display: inline-block;"><span class="msqrt"
                                                                                               id="MathJax-Span-11"
                                                                                               style="display: inline-block;"><span
                        style="display: inline-block; position: relative; height: 0px;"><span
                        style="position: absolute;"><span class="mrow" id="MathJax-Span-12"
                                                          style="display: inline-block;"><span class="mi"
                                                                                               id="MathJax-Span-13"
                                                                                               style="display: inline-block;"><img
                        src="http://mathjax.cnblogs.com/2_6_1/fonts/HTML-CSS/TeX/png/Math/Italic/476/004E.png?rev=2.6.1"
                        style="width: 20px; height: 15px;"></span></span><span
                        style="display: inline-block; width: 0px;"></span></span><span style="position: absolute;"><span
                        style="display: inline-block; position: relative; height: 0px;"><span
                        style="position: absolute;"><img
                        src="http://mathjax.cnblogs.com/2_6_1/fonts/HTML-CSS/TeX/png/Main/Regular/476/2212.png?rev=2.6.1"
                        style="width: 15px; height: 1px; vertical-align: 5px;"><span
                        style="display: inline-block; width: 0px;"></span></span><span style="position: absolute;"><img
                        src="http://mathjax.cnblogs.com/2_6_1/fonts/HTML-CSS/TeX/png/Main/Regular/476/2212.png?rev=2.6.1"
                        style="width: 15px; height: 1px; vertical-align: 5px;"><span
                        style="display: inline-block; width: 0px;"></span></span></span><span
                        style="display: inline-block; width: 0px;"></span></span><span style="position: absolute;"><span
                        style="display: inline-block; position: relative; height: 0px;"><span
                        style="position: absolute;"><img
                        src="http://mathjax.cnblogs.com/2_6_1/fonts/HTML-CSS/TeX/png/Size4/Regular/476/E001.png?rev=2.6.1"
                        style="width: 24px; height: 14px; vertical-align: 0px;"><span
                        style="display: inline-block; width: 0px;"></span></span><span style="position: absolute;"><img
                        src="http://mathjax.cnblogs.com/2_6_1/fonts/HTML-CSS/TeX/png/Size4/Regular/476/23B7.png?rev=2.6.1"
                        style="width: 16px; height: 40px; vertical-align: -20px;"><span
                        style="display: inline-block; width: 0px;"></span></span></span><span
                        style="display: inline-block; width: 0px;"></span></span></span></span></span><span
                        style="display: inline-block; width: 0px;"></span></span></span><span
                        style="display: inline-block; overflow: hidden; width: 0px;"></span></span></nobr><span
                        class="MJX_Assistive_MathML" role="presentation"><math
                        xmlns="http://www.w3.org/1998/Math/MathML"><msqrt><mi>N</mi></msqrt></math></span></span>
                <script type="math/tex" id="MathJax-Element-2">\sqrt{N}</script>
                个特征。如果是整数，代表考虑的特征绝对数。如果是浮点数，代表考虑特征百分比，即考虑（百分比xN）取整后的特征数。其中N为样本总特征数。
            </p>
            <p>一般来说，如果样本特征数不多，比如小于50，我们用默认的"None"就可以了，如果特征数非常多，我们可以灵活使用刚才描述的其他取值来控制划分时考虑的最大特征数，以控制决策树的生成时间。</p>
        </td>
    </tr>
    <tr>
        <td>
            <p>决策树最大深max_depth</p>
        </td>
        <td colspan="2">&nbsp;决策树的最大深度，默认可以不输入，如果不输入的话，决策树在建立子树的时候不会限制子树的深度。一般来说，数据少或者特征少的时候可以不管这个值。如果模型样本量多，特征也多的情况下，推荐限制这个最大深度，具体的取值取决于数据的分布。常用的可以取值10-100之间。</td>
    </tr>
    <tr>
        <td>
            <p>内部节点再划分所需最小样本数min_samples_split</p>
        </td>
        <td colspan="2">这个值限制了子树继续划分的条件，如果某节点的样本数少于min_samples_split，则不会继续再尝试选择最优特征来进行划分。&nbsp;默认是2.如果样本量不大，不需要管这个值。如果样本量数量级非常大，则推荐增大这个值。我之前的一个项目例子，有大概10万样本，建立决策树时，我选择了min_samples_split=10。可以作为参考。</td>
    </tr>
    <tr>
        <td>
            <p>叶子节点最少样本数min_samples_leaf</p>
        </td>
        <td colspan="2">&nbsp;这个值限制了叶子节点最少的样本数，如果某叶子节点数目小于样本数，则会和兄弟节点一起被剪枝。&nbsp;默认是1,可以输入最少的样本数的整数，或者最少样本数占样本总数的百分比。如果样本量不大，不需要管这个值。如果样本量数量级非常大，则推荐增大这个值。之前的10万样本项目使用min_samples_leaf的值为5，仅供参考。</td>
    </tr>
    <tr>
        <td>
            <p>叶子节点最小的样本权重和min_weight_fraction_leaf</p>
        </td>
        <td colspan="2">这个值限制了叶子节点所有样本权重和的最小值，如果小于这个值，则会和兄弟节点一起被剪枝。&nbsp;默认是0，就是不考虑权重问题。一般来说，如果我们有较多样本有缺失值，或者分类树样本的分布类别偏差很大，就会引入样本权重，这时我们就要注意这个值了。</td>
    </tr>
    <tr>
        <td>
            <p>最大叶子节点数max_leaf_nodes</p>
        </td>
        <td colspan="2">&nbsp;通过限制最大叶子节点数，可以防止过拟合，默认是"None”，即不限制最大的叶子节点数。如果加了限制，算法会建立在最大叶子节点数内最优的决策树。如果特征不多，可以不考虑这个值，但是如果特征分成多的话，可以加以限制，具体的值可以通过交叉验证得到。</td>
    </tr>
    <tr>
        <td>
            <p>类别权重class_weight</p>
        </td>
        <td>
            指定样本各类别的的权重，主要是为了防止训练集某些类别的样本过多，导致训练的决策树过于偏向这些类别。这里可以自己指定各个样本的权重，或者用“balanced”，如果使用“balanced”，则算法会自己计算权重，样本量少的类别所对应的样本权重会高。当然，如果你的样本类别分布没有明显的偏倚，则可以不管这个参数，选择默认的"None"
        </td>
        <td>&nbsp;不适用于回归树</td>
    </tr>
    <tr>
        <td>
            <p>节点划分最小不纯度min_impurity_split</p>
        </td>
        <td colspan="2">&nbsp;这个值限制了决策树的增长，如果某节点的不纯度(基尼系数，信息增益，均方差，绝对差)小于这个阈值，则该节点不再生成子节点。即为叶子节点&nbsp;。</td>
    </tr>
    <tr>
        <td>
            <p>数据是否预排序presort</p>
        </td>
        <td colspan="2">
            这个值是布尔值，默认是False不排序。一般来说，如果样本量少或者限制了一个深度很小的决策树，设置为true可以让划分点选择更加快，决策树建立的更加快。如果样本量太大的话，反而没有什么好处。问题是样本量少的时候，我速度本来就不慢。所以这个值一般懒得理它就可以了。
        </td>
    </tr>
    </tbody>
</table>

除了这些参数要注意以外，其他在调参时的注意点有：

1. 当样本少数量但是样本特征非常多的时候，决策树很容易过拟合，一般来说，样本数比特征数多一些会比较容易建立健壮的模型

2. 如果样本数量少但是样本特征非常多，在拟合决策树模型前，推荐先做*维度规约*，比如主成分分析（PCA），特征选择（Losso）或者独立成分分析（ICA）。这样特征的维度会大大减小。再来拟合决策树模型效果会好。

3. 推荐多用决策树的**可视化**，同时先限制决策树的深度（比如最多 3 层），这样可以先观察下生成的决策树里数据的初步拟合情况，然后再决定是否要增加深度。

4. 在训练模型时，注意观察样本的类别情况（主要指分类树），如果类别分布非常不均匀，就要考虑用 class_weight 来限制模型过于偏向样本多的类别。

5. 决策树的数组使用的是 numpy 的 `float32` 类型，如果训练数据不是这样的格式，算法会先做 copy 再运行。

6. 如果输入的样本矩阵是稀疏的，推荐在拟合前调用`csc_matrix`稀疏化，在预测前调用`csr_matrix`稀疏化。

### 部分代码：

[decision_trees](https://github.com/izhangzhihao/MachineLearningInAction/blob/master/Module%206:%20Decision%20Trees/decision_trees.ipynb)

[random_forest](https://github.com/izhangzhihao/MachineLearningInAction/blob/master/Module%207:%20Ensemble%20Learning/random_forest.python)

参考
* [模型组合基本方法简介](http://baogege.info/2015/04/27/model-aggregation/)
* [信息增益](http://en.wikipedia.org/wiki/Information_gain)
* [随机森林与决策树](https://clyyuanzi.gitbooks.io/julymlnotes/content/rf.html)
* [scikit-learn 决策树算法类库使用小结](http://www.cnblogs.com/pinard/p/6056319.html)
* [Decision Trees](http://scikit-learn.org/stable/modules/tree.html)
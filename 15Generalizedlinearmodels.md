# 广义线性模型（GLM）

付逸阳 2019302130036

## 简介

广义线性模型[generalize linear model]是线性模型的扩展，通过连接函数建立响应变量的数学期望值与线性组合的预测变量之间的关系。其特点是不强行改变数据的自然度量，数据可以具有非线性和非恒定方差结构。是线性模型在研究响应值的非正态分布以及非线性模型简洁直接的线性转化时的一种发展。

广义线性模型的目的是将线性建模的思想扩展到$X$和$E(y|X)$之间的线性关系或正态分布不合适的情况。

广义线性模型分为三个部分：

1. 线性预测器$\eta=X\beta$
2. 将线性预测值与结果变量平均值关联的连接函数：$\mu=g^{-1}(\eta)=g^{-1}(X\beta)$
3.  用平均值$E(y|X)=\mu$指定结果变量$y$分布的随机分量。该分布还取决于离散参数$\phi$。

因此，给定$X$，$y$分布的均值由$X\beta$确定：$E(y|X)=g^{-1}(X\beta)$。尽可能使用与线性回归相同的符号，因此$X$是解释变量的$n×p$矩阵，$\eta=X\beta$是$n$个线性预测值的向量。如果我们用$X_i\beta$和离散参数$\phi$（如果存在）来表示第$i$个情形的线性预测器，那么分布就以以下形式出现：
$$
p(y|X,\beta,\phi)=\prod_{i=1}^{n}p(y_i|X^i\beta,\phi)\tag{1}
$$
对于泊松分布和二项分布，最常用的广义线性模型不需要离散参数，即，$\phi$固定在$1$。但实际上，在大多数应用中，过度分散是常态，而不是例外。

## 标准广义线性模型似然估计

***连续数据***

线性回归是广义线性模型的一种**特例**，单位连接函数为$g(\mu)=\mu$。对于所有正态的连续数据，我们可以使用对数尺度上的正态模型。当该分布族不符合数据时，伽马分布和威布尔分布有时可以作为替代。

***泊松分布***

**计数数据**（比如，在一定时间内某随机事件发生的次数）通常使用泊松模型，观测数据服从泊松分布。泊松广义线性模型，通常称为泊松回归模型，假设$y$为均值$\mu$（方差$\mu$）的泊松分布。连接函数通常为$log\mu=X\beta$。因此，$y=(y_1,...,y_n)$的分布为
$$
p(y|\beta)=\prod_{i=1}^{n}\frac{1}{y_i!}e^{-exp(\eta_i)}(exp(\eta_i))^{y_i}\tag{2}
$$
其中，$\eta^i=X_i\beta$是第$i$个情形的线性预测器。在考虑贝叶斯后验分布时，我们以$y$为条件，因子$\frac{1}{y_i!}$可以为任意常数。

***二项式***

最广泛使用的广义线性模型是用于**二进制或二项式数据**的模型。假设在$n_i$已知下，$y_i\sim Bin(n_i,\mu_i)$。通常根据比例$\frac{y_i}{n_i}$的均值而不是$y_i$的均值来指定模型。选择成功概率的logit变换$g(\mu_i)=log(\mu_i/(1-\mu_i))$作为连接函数导入logistic回归模型。数据$y$的分布为：
$$
p(y|\beta)=\prod_{i=1}^{n}(^{n_i}_{y_i})(\frac{e^{\eta_i}}{1+e^{\eta^i}})^{y_i}(\frac{1}{1+e^{\eta_i}})^{n_i-y_i}
$$
其他常用的连接函数，如，**probit连接函数**$g(\mu)=\Phi^{-1}(\mu)$，经常用于计量经济学中。probit模型的数据分布如下：
$$
p(y|\beta)=\prod_{i=1}^{n}(^{n_i}_{y_i})(\Phi(\eta_i))^{y_i}(1-\Phi(\eta_i))^{n_i-y_i}
$$
probit连接函数是通过在正态线性模型中保留正态变化过程，同时假设所有结果都是二分的来获得的。实际上，probit和logit模型是相似的，主要区别在于尾部的极端。在这两种情况下，后验密度的因子仅取决于观测量，并可归入后验密度的常数因子。logit和probit模型可以推广到多变量结果的建模。同时，$t$分布可用于logit和probit模型的稳健替代方案。

另一个标准连接函数为$g(\mu)=log(-log(\mu))$,互补对数，在$\mu$中不对称（$g(\mu)\neq -g(1-\mu)$）。

***过度分散模型***

广义线性模型的经典分析允许超出假设抽样分布的变化，通常称为过度分散。例如，考虑一个logistic回归，其中抽样单位是一窝小白鼠，出生的窝的比例被认为是一些解释变量（如母亲的年龄，药物剂量等）的二项式。数据表明，由于母亲之间的系统性差异，二项分布下的差异可能比预期的要大。这种变化可以使用每个母亲的指标纳入分层模型，这些指标本身遵循正态分布（通常易于解释）或$\beta$分布（与二项式先验分布共轭）。

## 使用广义线性模型的要点

以下是一些概念的介绍。

***标准连接函数***

上面小节中针对各个模型使用了每个族的规范连接函数。标准连接函数是概率密度指数族形式指数中出现的平均参数的函数。我们经常使用规范连接函数，但在我们的讨论中没有任何东西是基于规范连接函数的，例如，二项式和累积多项式模型的probit连接函数不是规范的。

***偏移量***

有时表示一个广义线性模型很方便，因为其中一个预测器有一个已知的系数。这种类型的预测器称为偏移量，通常出现在泊松模型中，其中发生率为每单位时间$\mu$，因此已知$T$时，预期事件数为$\mu T$。我们可能希望采用$log \mu=X\beta$，像通常的泊松广义线性模型（公式（2））一样；然而，广义线性模型通过$y$的均值（即$\mu T$）进行参数化，其中$T$表示回归中各单位的暴露时间向量。我们可以应用泊松广义线性模型，通过增加一列包含$log T$值的解释变量矩阵；增广矩阵的这列对应于一个已知值（等于$1$）的系数。

***解释模型参数***

解释变量$x$的选择和参数化涉及与线性模型相同的考虑因素。但有一个重要的补充。线性预测器用于预测连接函数$g(\mu)$，而不是$\mu=E(y)$，因此将第$j$个解释变量$x_j$改变固定量的效果取决于$x$的当前值。将效应转化为$y$量表的一种方法是，使用预测因子$x_0$和标准结果$y_0=g^{-1}(x_0\beta)$的向量，测量与标准情况相比的变化。然后，加上或减去向量$\Delta x$导致标准结果从$y_0$变为$g^{-1}(g(y_0)\pm(\Delta x)\beta)$。这个表达式也可以用微分形式写成，但是考虑$\Delta x$的变化通常更有用，$\Delta x$不一定接近于$0$。

***从潜在的连续数据理解离散数据模型***

在理解和计算离散数据回归时，一个重要的思想是对未观测（潜在）连续数据的再表达。二进制数据的probit模型$Pr(y_i=1)=\Phi(X_i\beta)$与以下潜在数据$\mu _i$的模型等效：
$$
u_i \sim N(X_i\beta,1)\\
y_i = \left\{\begin{array}{lr}1&\textsf{if}&\mu_i>0\\0&\textsf{if}&\mu_i<0\end{array}\right.\tag{3}
$$
有时，可以对潜在数据进行有用的解释。例如，在一项政治调查中，如果$y=0$或$1$代表对民主党或共和党的支持，那么$\mu$可以被视为党派偏好的持续衡量指标。

probit模型潜在参数化的另一个优点是，它允许方便的Gibbs采样器计算。以潜在参数$\mu_i$为条件，模型为简单线性回归。然后，以模型参数和数据为条件，$\mu_i$具有截断正态分布，$\mu_i\sim N(X_i\beta,1)$，截断为负或正，取决于$y_i=0$还是$1$。

潜在数据思想也可以应用于**logistic回归**，在这种情况下，$\mu_i\sim N(X_i\beta,1)$，其中logistic分布密度为$\frac{1}{e^{x/2}+e^{-x/2}}$,即逆logit函数的导数。该模型比probit模型更不便于计算（因为带有logistic误差的回归并不特别容易计算），但有助于模型理解。

对于**有序多项式回归**，也可以给出类似的解释。例如，如果数据$y$的值为$0$、$1$、$2$或$3$，则可以根据切点$c_0,c_1,c_2$定义有序多项式模型，这样，如果$u_i<c_0$，$y_i$等于$0$;如果$u_i\in(c_0,c_1)$，$y_i$等于$1$;如果$u_i\in(c_1,c_2)$,$y_i$等于$2$；如果$u_i>c_2$，$y_i$等于$3$。这些切点模型的参数化有多种自然方式，当模型置于层次结构中时，参数化的选择会产生影响。

例如，如果将常数添加到所有三个切点$c_0,c_1,c_2$，则此参数化下的广义线性模型的似然性不变，但该模型可能无法识别。处理此问题的典型方法是将其中一个切点设置为固定值，例如设置$c_0=0$，以便只需将$c_1$和$c_2$根据数据估算。（这概括了上述二进制数据的潜在数据解释，其中$0$为预设切点。）

***贝叶斯非层次和层次广义线性模型***

我们考虑$\beta$上的先验信息分布的广义线性模型，$\beta$上的先验信息分布，以及$\beta$上先验分布依赖于未知超参数的层次模型，我们尝试用相同的广义笔刷处理所有的广义线性模型，这会产生一些困难。广义线性模型除了用回归系数（正常情况下为$\sigma$）表示外，还用离散参数表示；这里，我们使用离散参数的通用符号$\phi$。可以在离散参数上放置先验分布，并且可以在离散参数的条件下描述关于$\phi$的任何先验信息,即$p(\beta,\phi)=p(\phi)p(\beta|\phi)$。

***$\beta$上的非信息先验分布***

广义线性模型的经典分析是在假定$\beta$的先验分布为非信息分布或平坦分布的情况下得到的。与非信息均匀先验密度相对应的后验模式是参数$\beta$的最大似然估计，可通过**迭代加权线性回归**得到（例如，在计算机软件R包或Glim中实现）。近似后验推断可从似然的正常近似值中获得。

***共轭先验分布***

一个有用的方法去指定$\beta$的先验信息是根据在同一模型下获得的假设数据，即$n_0$假设数据点的向量$y_0$和解释变量$X_0$的相应$n_0\times k$矩阵。其得到的后验分布与增广数据向量$(^y_{y_0})$的后验分布相同，也就是说，$y$和$y_0$串在一起作为一个向量，而不是解释变量矩阵$(^X_{X_0})$和$y$上的非信息一致先验密度的组合系数。对于共轭先验分布的计算，可以使用与非信息先验分布相同的迭代方法。

***非共轭先验分布***

直接用参数$\beta$表示先验信息通常更为自然。例如，我们可以使用正态模型，具有特定$\beta_0$和$\sum _\beta$下，$\beta \sim N(\beta_0,\sum _\beta)$。

***层次模型***

与正态线性模型一样，广义线性模型的分层先验分布是拟合复杂数据结构的自然方式，需要我们在不遇到“过度拟合”问题的情况下包含更多解释变量。通常使用$\beta$的正态分布，以便使用广义线性模型似然的正态近似，模拟已经为分层正态线性模型开发的建模实践和计算方法。

***似然函数的正态逼近***

广义线性模型中的后验推断通常需要第三部分中的近似和采样工具。

* **伪数据和伪方差**

对于简单的计算，可以方便地通过$\beta$中的正态分布来近似似然，条件是离散参数$\phi$（如有必要）和任何分层参数。基本方法是通过线性模型来近似广义线性模型；对于每个数据点$y_i$，我们构造一个“伪数据”$z_i$和一个“伪数据”。因此，广义线性模型似然$p(y_i|X_i\beta,\phi)$由正态似然$N(z_i|X_i\beta,\sigma^2_i)$近似。然后，我们可以组合$N$个伪数据点，并通过向量$z=(z_1,...,z_n)$的线性回归模型近似整个似然在解释变量$X$的矩阵上，使用已知的方差矩阵$diag(\sigma^2_1,...,\sigma^2_n)$。这种有点复杂的方法的优点是产生一个近似的可能性，我们可以像分析正常线性回归数据一样进行分析，从而允许使用可用的线性回归算法。

* **法线近似的中心**

一般来说，对于近似值居中的点，法向近似值将取决于其中心的$\beta$值（如果模型有离散参数，则为$\phi$）。我们使用符号$(\widehat{\beta},\widehat{\phi})$表示近似值的中心点，并使用$\widehat{\eta}=X\widehat{\beta}$表示线性预测值的对应向量。在计算的模式查找阶段，我们迭代地改变法向近似值的中心。一旦我们近似达到模式，我们在固定值$(\widehat{\beta},\widehat{\phi})$处使用法向近似值。

* **确定正态近似的参数**

我们可以将对数似然写成：
$$
p(y_1,...,y_n|\eta,\phi)=\prod_{i=1}^{n}p(y_i|\eta_i,\phi)\\
=\prod_{i=1}^{n}exp(L(y_i|\eta_i,\phi))
$$
其中$L$是单个观测值的对数似然函数。我们通过$\eta_i$中的正态密度来近似上述乘积中的每个因子，从而通过$\eta_i$中的二次函数来近似每个$L(y_i|\eta_i,\phi)$：
$$
L(y_i|\eta_i,\phi) \approx -\frac{1}{2\sigma^2_i}(z_i-\eta_i)^2+constant
$$
式中，一般情况下，$z_i$、$\sigma^2_i$和常数取决于$y$、$\widehat{\eta}=(X\widehat{\beta})_i$和$\widehat {\phi}$。也就是说，第$i$个数据点近似等于观测值$z_i$，其正态分布的均值为$\eta_i$和方差$\sigma^2_i$。

确定近似值$z_i$和$\sigma^2_i$的标准方法是匹配以$\widehat{\eta} -X\widehat{\beta}$为中心的$L(y_i|\eta_i,\phi)$泰勒级数的一阶和二阶项。写入和分别得出以下结果：
$$
z_i=\widehat{\eta_i}-\frac{L'(y_i|\widehat{\eta_i},\widehat{\phi})}{L''(y_i|\widehat{\eta_i},\widehat{\phi})}\tag{4}\\
\sigma^2_i=-\frac{1}{L''(y_i|\widehat{\eta_i},\widehat{\phi})}
$$
* **例子：二项logistic模型**

在具有logistic链的二项广义线性模型中，每个观测值的对数似然是：
$$
L(y_i|\eta_i)=y_ilog(\frac{e^{\eta_i}}{1+e^{\eta_i}})+(n_i-y_i)log(\frac{1}{1+e^{\eta_i}})\\
=y_i\eta _i-n_ilog(1+e^{\eta_i})
$$
（二项模型中没有离散参数$\phi$）。对数似然导数为：
$$
\frac{dL}{d\eta_i}=y_i-n_i\frac{e^{\eta_i}}{1+e^{\eta_i}}\\
\frac{d^2L}{d\eta^2_i}=-n_i\frac{e^{\eta_i}}{(1+e^{\eta_i})^2}
$$
因此，第$i$个采样单位的正态近似值的伪数据$z_i$和方差$\sigma^2_i$为：
$$
z_i=\widehat{\eta_i}+\frac{(1+e^{\eta_i})^2}{e^{\eta_i}}(\frac{y_i}{n_i}-\frac{e^{\eta_i}}{1+e^{\eta_i}})\\
\sigma^2_i=\frac{1}{n_i}\frac{(1+e^{\eta_i})^2}{e^{\eta_i}}
$$
基于线性预测器$\widehat{\eta}$，近似值取决于$\widehat{\beta}$。

后验概率模式可以使用迭代加权线性回归找到：在每一步，根据$(\beta,\phi)$的当前猜测计算似然的正态近似值，并通过加权线性回归找到得到的近似后验分布的模式。（如果有关于$\beta$的任何先验信息，则应将其作为附加数据行和解释变量包含在回归中。）迭代该过程相当于求解$k$个非线性方程组，$\frac{dp(\beta|y)}{d\beta}=0$，利用牛顿法，快速收敛到标准广义线性模型的模态。一个可能会遇到的困难是$\beta$的估计将趋于无穷大，例如，在logistic回归中，如果存在一些解释变量组合，其中$\mu$几乎等于$0$或$1$，则可能出现这种情况。大量的先验信息往往可以消除这一问题。

如果存在离散参数$\phi$，则可以在迭代的每一步通过最大化其条件后验密度（一维）来更新$\phi$，给出$\beta$的当前猜测。类似地，可以在迭代中包括需要估计的任何层次方差参数，并在每一步更新其值。

***近似正态后验分布***

一旦达到模式$(\widehat{\beta},\widehat{\phi})$，就可以通过最近加权线性回归计算的输出来近似给定$\widehat{\phi}$的$\beta$的条件后验分布，即：
$$
p(\beta|\widehat{\phi},y)\approx N(\beta|\widehat{\beta},V_{\beta})
$$
式中，本例中的$V_{\beta}$为$X^Tdiag(-L''(y_i,\widehat{\eta_i},\widehat{\phi}))X)^{-1}$。（通常，只需计算$V_{\beta}$的Cholesky因子。）如果样本量$n$较大，且$\phi$不是模型的一部分（如二项分布和泊松分布），我们可以到此为止，并通过$p(\beta|y)$的正态近似来总结后验分布。

如果存在离散参数$\phi$，可以使用以下公式近似条件模式$\widehat{\beta}(\phi)$下的$\phi$的边缘分布：
$$
p_{approx}(\phi|y)=\frac{p(\beta,\phi|y)}{p_{approx}(\beta|\phi,y)}\propto p(\widehat{\beta}(\phi),\phi|y)|V_{\beta}(\phi)|^{1/2}
$$


其中，最后一个表达式中的$\widehat{\beta}$和$V_{\beta}$是以$\phi$为条件的正态近似的模式和方差矩阵。

## 弱信息先验在logistic回归中的应用及相关实例

不可识别性是logistic回归中的一个常见问题。除了线性回归中常见的共线问题外，离散数据回归也可能因分离而变得不稳定，当预测因子的线性组合完全预测结果时，就会出现这种情况。分离在应用logistic回归中非常常见，尤其是在二元预测中，在实际工作中常常处理不当。例如，分离的一个常见“解决方案”是删除预测值，直到生成的模型可识别为止，这通常会导致从模型中删除最强的预测值。

获得稳定logistic回归系数的另一种方法是使用贝叶斯推理。在这里，我们提出了一类基于$t$分布的弱信息先验，以及一个默认选择，该选择依赖于在大多数问题中的影响不大的假设。

我们假设系数的先验独立性，并理解如果存在先验相关性合适的地方，则可以重新参数化模型。对于每个系数，我们假设一个$Student-t$先验分布，平均值为$0$，自由度参数为$v$，标度为$s$，选择$ν$和$s$以提供最小先验信息，从而将系数限制在合理范围内。我们的动机是考虑$T$族，因为平尾分布允许稳健的推断。

我们可以使用通常的MCMC方法对该模型执行完全的贝叶斯计算。此外，我们可以使用近似EM算法构造快速迭代计算，以获得回归系数和标准误差的点估计。从点估计的角度来看，先验分布的目的是稳定（正则化）未建模参数的估计。

***分离问题***

当一个拟合模型“分离”（即，完美地预测了离散数据的某个子集）时，最大似然估计可能是未定义的或不合理的，并且统一先验分布下的贝叶斯推理也可能失败。我们用一个在常规分析中出现的例子来说明，将logistic回归拟合到一系列数据集。

***例子：从性别、种族和收入预测投票***

表1显示了在给定性别、种族和收入（编码为$−2,−1,0,1,2$），分别适用于一系列选举的选前投票。除了1964年的情况外，这些估计看起来不错，当时的情况完全不同：在那一年的调查中，87名非裔美国人中，没有一人表示对共和党候选人有偏好。我们在R中拟合模型，即使在1964年，该模型实际上也给出了黑人系数的有限估计，但该数字及其标准误差基本上没有意义，是迭代拟合过程在放弃之前需要多长时间的函数。当年黑人系数的最大似然估计为$-\infty$.

![1](/figure/fyy_figure1.png)

在表1中，预测选前民意调查中共和党投票意向的logistic回归（具有统一的先验分布）估计值和标准误差分别适用于1960年至1972年四次总统选举的调查数据。这一估计是合理的，但1964年除外，当时完全分离（没有黑人受访者支持共和党候选人巴里·戈德沃特）。

![2](/figure/fyy_figure2.png)

图1显示了投票示例中黑色系数的轮廓可能性，这一参数的可能性取决于其他参数设置为其最大似然估计值。最大似然估计（或等效地，均匀先验密度下的后验模式）位于$-\infty$ ,这在本应用程序中毫无意义。根据1964年共和党投票的logistic回归，以模型中其他系数的点估计为条件,最大值为$\beta$。

***具有特定正态先验分布的计算***

在logistic回归模型的背景下工作，
$$
Pr(y_i=1)=logit^{-1}(X_i\beta)\tag{5}
$$
我们采用经典的最大似然算法，以估计$\widehat{\beta}$和协方差矩阵$V_{\beta}$的形式获得系数$\beta$的近似后验推断。

我们构建的标准logistic回归算法是通过近似线性化对数似然的导数，使用加权最小二乘法求解，然后迭代该过程，每一步评估最新估计$\widehat{\beta}$的导数。在每次迭代中，算法根据对数似然导数的线性化确定伪数据$z_i$和伪方差$(\sigma^z_I)^2$。

最简单的信息先验分布为$\beta$分量分配正态分布：
$$
\beta_j \sim N(\mu_j,\sigma^2_j), j=1,...,J\tag{6}
$$
在经典算法中，只需改变加权最小二乘步长，增加先验分布的近似似然，就可以轻松地包含这些信息。如果模型的$J$系数$\beta_j$具有独立的$N(\mu_j,\sigma^2_j)$先验分布，则我们添加$J$个伪数据点，并对观测值$z_*$，解释变量$X_*$，和权向量$\omega_*$进行加权线性回归。
$$
z_*=(^z_\mu),X_*=(^X_{I_J}),\omega_*=((\sigma^z)^{-2},\sigma^{-2})\tag{7}
$$
向量$z_*$, $\omega_*$和矩阵$X_*$ 通过结合似然（$z$和$\sigma^z$是$z_i$和$\sigma^z_i$的向量，$X$是回归设计矩阵（5））和先验（$\mu$和$\sigma$是（6）中$\mu_j$和$\sigma_j$的向量，$I_J$是$J\times J$单位矩阵）。因此，$z_*$和$\omega_*$是长度为$n+J$，$X_*$是一个$(n+J)\times J$的矩阵。用增广矩阵$X_*$, 这种回归是确定的，因此得到的估计$\widehat{\beta}$定义良好，方差有限，即使原始数据具有的共线性或分离性将导致最大似然估计的不可识别性。

然后，整个计算是迭代加权最小二乘法，从$\beta$的猜测开始，然后计算对数似然的导数来计算$z$和$\sigma_z$，然后对伪数据使用加权最小二乘法来产生$\beta$的更新估计，然后重新计算该新$\beta$值处的对数似然导数，依此类推，收敛到估计值$\widehat{\beta}$。协方差矩阵$V_{\beta}$只是在$\widehat{\beta}$处计算的对数后验密度的逆二阶导数矩阵，即，对于不在参数空间边界上的估计，通常的正常理论不确定性估计。

**具有$t$先验分布的近似EM算法**

如果系数$\beta_j$具有独立的$t$先验分布，且具有中心$\mu_j$和尺度$s_j$，则我们可以采用刚才描述的迭代加权最小二乘算法，使用近似EM算法估计系数。我们将简要描述算法的步骤；其思想是将每个系数$\beta_j$的$t$先验分布表示为具有未知尺度$\sigma_j$的正态分布的混合物，
$$
\beta_j\sim N(\mu_j,\sigma^2_j),\sigma^2_j\sim Inv-\chi^2(v_j,s^2_j)\tag{8}
$$
然后在每一步对$\beta_j$进行平均，将其视为缺失数据，并执行EM算法来估计$\sigma_j$。该算法通过交替一步迭代加权最小二乘法（如上所述）和一步EM进行。一旦执行足够多的迭代以达到近似收敛，我们将得到向量参数$\beta$和估计值$\sigma_j$的估计和协方差矩阵。

我们通过将每个$\sigma_j$设置为值$s_j$（先验分布的尺度）进行初始化，并且与之前一样，从$\beta$的猜测开始（或者从粗略估计中获得，或者简单地选择一个起始值，例如$\beta=0$）。然后，在算法的每一步，我们通过最大化其（近似）对数后验密度的期望值来更新$\sigma$，
$$
logp(\beta,\sigma|y)\approx -\frac{1}{2}\sum_{i=1}^{n}\frac{1}{(\sigma^2_i)^2}(z_i-X_i\beta)^2-\frac{1}{2}\sum_{j=1}^{J}(\frac{1}{\sigma^2_j}(\beta_j-\mu_j)^2+log(\sigma^2_j))\\
-p(\sigma_j|v_j,s_j)+constant\tag{9}
$$
算法的每次迭代如下所示：

1. 根据$\beta$的当前估计值，对对数似然进行正态近似，确定向量$z$和$\sigma^z$，如经典logistic回归计算中所示。

2. 近似E-步：首先根据扩充数据（7）运行加权最小二乘回归，得到方差矩阵$V_{\beta}$的估计值$\widehat{\beta}$。然后通过替换（9）中$(\beta_j-\mu_j)^2$确定对数后验密度的期望值，
   $$
   E((\beta_j-\mu_j)^2|\sigma,y)\approx (\widehat{\beta}_j-\mu_j)^2+(V_\beta)_{jj}\tag{10}
   $$
   这只是近似值，因为我们在正态分布上求平均值，正态分布只是广义线性模型似然的近似值。

3.  M-步：最大化对数后验密度（9）的（近似）期望值，以获得估计值，
   $$
   \widehat{\sigma}_j^2=\frac{(\widehat{\beta}_j-\mu_j)^2+(V_{\beta})_{jj}+v_js_j^2}{1+v_j}\tag{11}
   $$
   它对应于$\sigma^2_j$的（近似）后验模式，给定一个值为（10）的单一测量值和一个$Inv-\chi^2(v_j,s_j^2)$先验分布。

4. 在给定当前$\widehat{\beta}$的情况下，重新计算对数后验密度的导数，使用（11）中的估计值$\widehat{\sigma}$建立增强数据（7），并重复上述步骤1、2、3。

在算法收敛时，我们总结了使用最新估计$\widehat{\beta}$和协方差矩阵$V_{\beta}$的推论。

***logistic回归系数的缺省先验分布***

如上所述，我们对系数使用独立的$t$先验。把$\sigma$设置为无穷大后，在分离时失败。相反，我们希望添加一些先验信息，以限制估算远离不切实际的极端值。

在建立任何默认的先验分布时，一个挑战是如何正确地确定比例：例如，假设我们预测给定年龄（以年为单位）的投票偏好。如果将年龄表改为月，我们就不需要相同的先验分布。但离散预测有其自身的自然尺度（最显著的是，二元预测中的变化为$1$），这是我们想要遵循的。

我们模型的第一步是标准化输入变量：

1. 二进制输入转化成平均值为$0$，并且在其下限和上限条件下相差$1$。（例如，如果一个群体中有10%的黑人和90%的其他人种，我们将定义以c.black为中心的变量，使其值分别为$0.9$和$−0.1$。）

2. 将其他输入转化为平均值为$0$，并缩放至标准偏差为$0.5$。这种比例将连续变量置于与对称二进制输入相同的比例上（取$\pm0.5$的值，标准偏差为$0.5$）。

![3](/figure/fyy_figure3.png)

<font size=2>图2:（实线）标度为$2.5$的Cauchy密度，（虚线）标度为$2.5$的$t_7$密度，（虚线）$\theta$对应于概率$logit^{-1}(\theta)$的单个二项试验的似然性（一半成功率，一半失败率）。所有这些曲线都有利于绝对值低于$5$的值；我们选择Cauchy模型作为默认模型，因为它允许偶尔出现较大值的概率。</font>

我们区分回归输入和预测。例如，在年龄、性别及其相互作用的回归中，有四个预测因子（常数项、年龄、性别和年龄$\times$性别），但只有两个输入：年龄和性别。标准化的是输入变量，而不是预测值。

标准化变量的先验分布取决于数据，数据的范围可以提供有关系数合理范围的信息。从完全贝叶斯的角度来看，我们可以将我们的缩放过程看作是一个层次模型的近似，在该模型中，根据数据估计适当的缩放参数。

我们的默认先验分布集中在零，因为在没有任何问题特定信息的情况下，我们不知道系数$\beta$是正的还是负的。我们现在需要在重定标系数的系数上选择$t$先验分布中尺度$s$和自由度$v$的默认值。

一种选择默认$v$和$s$值的方法是考虑概率为$p=logit^{-1}(\theta)$的单二项试验的一半成功一半失败的基线情况。即，只有常数项的logistic回归。相应的似然为$e^{\theta/2}/(1+e^{\theta})$，接近于具有$7$个自由度和$2.5$标度的$t$密度函数。我们将选择一个稍微保守的选择，Cauchy分布，或者$t_1$分布，同样是$2.5$。图2显示了三个密度函数：它们都优先选择小于$5$的值，Cauchy允许偶尔出现非常大的值。

我们将中心为$0$、标度为$2.5$的独立Cauchy先验分布分配给logistic回归中除常数项外的每个系数。当与标准化结合时，这意味着在任何输入变量中，当从低于平均值的一个标准偏差移动到高于平均值的一个标准偏差时，logit概率的绝对差异应小于$5$。在logit量表上加$5$等于将预测概率从$0.01$移到$0.5$，或从$0.5$移到$0.99$，这些变化比我们在大多数应用程序中预期的任何变化都要大。（例如，在美国，不吸烟男性死于肺癌的概率约为1%，重度吸烟者的概率不到50%。）

如果我们将中心为$0$且标度为$2.5$的Cauchy先验分布也应用于常数项，我们将说明，对于所有输入中平均的单元，成功概率可能在$1\%$到$99\%$之间。像罕见疾病的流行病学建模，这可能没有意义，因此默认情况下，我们应用较弱的先验分布——中心为$0$、标度为$10$的Cauchy分布。

这意味着我们预计平均案例的成功概率在$10^{-9}$到$1-10^{-9}$之间。我们通常有更多关于截距的信息，而不是关于任何特定系数的信息，因此我们可以使用较弱的先验知识。

![4](/figure/fyy_figure4.png)

<font size=2>图3：最大似然和贝叶斯logisti回归的估计值，以及生物测定示例的推荐默认先验分布。除了绘制拟合曲线（右侧），我们还展示了原始计算机输出，以说明我们的方法将如何在日常实践中使用。glm和bayesglm的估计系数的大变化一开始可能看起来很奇怪，但经过思考，我们更喜欢第二个估计，其系数$x$较低，这是基于对可能性所允许的最极端可能性进行降权。</font>

***其他模型***

* **线性回归**

我们的算法基本上与线性回归相同，只是加权最小二乘法是精确的而不是近似的最大惩罚似然法，并且需要添加一个步骤来估计数据方差。此外，在分配先验分布之前，我们将通过重新调整结果变量的平均值$0$和标准偏差$0.5$来预处理$y$（或者，等效地，将先验尺度参数乘以数据的标准偏差）。分离不是线性回归中的一个问题；然而，当常规应用时（例如，在迭代插补算法中），可能会出现共线性，在这种情况下，有一个适当但较弱的先验分布是有帮助的。

* **其他广义线性模型**

同样，基本算法不变，除了伪数据和伪方差（由对数似然的一阶和二阶导数得出）发生了变化。对于泊松回归和其他具有对数链接的模型，我们通常不会期望对数尺度上的影响大于$5$，因此本文中给出的先验分布可能是一个合理的默认选择。此外，对于具有离散参数的负二项式等模型，可以使用额外的步骤进行估计，就像在正态线性回归中估计数据水平方差时所做的那样。对于更复杂的模型，如多项式logit和probit，我们考虑将系数上的独立$t$先验分布与伪数据相结合，以在可能存在稀疏数据的情况下识别切点。这样的模型也带来了计算上的挑战，因为我们没有简单的现有迭代加权最小二乘算法来适应。

***生物测定实例***

将默认的弱信息先验分布应用于生物测定实例中。本例中没有分离问题。但是数据中的信息量很低，只有20个二进制数据点。数据来源于二十只动物暴露于四种不同剂量的毒素的结果。图3显示了实验最后产生的logistic回归，首先使用最大似然法拟合，然后使用我们的默认Cauchy先验分布，中心为$0$，标度为$10$（对于常数项）和$2.5$（对于计量系数）。按照我们的一般程序，在重新缩放$x$后，我们将此先验分布分配为平均值为$0$，标准偏差为$0.5$。

对于如此小的样本，先验分布实际上会产生差异，将标准剂量的估计系数从$10.2\pm6.4$降低到$5.4\pm 2.2$。（在重定标参数化中，预测值的平均值为$0$，标准偏差为$0.5$，估计值从$7.7\pm4.9$变为$4.4\pm1.9$。）如此大的变化似乎令人不安，但出于上述原因，我们怀疑其在logistic标度上的影响是否达到$10.2$，分析表明这些数据与更小的效应大小$5.4$是一致的。大量的收缩仅仅证实了给出原始最大似然估计的信息是多么的脆弱。图3右上角的图表以不同的方式显示了比较：最大似然估计几乎完美地拟合了数据；然而，考虑到每组只有5只动物的样本量，数据和贝叶斯拟合之间的差异很小。

***种族预测投票实例***

将默认先验分布应用于本节前面讨论的选举前民调，在这一部分中，我们可以很容易地拟合logistic回归，每个选举年（1964年除外）的先验不变，其中黑人的系数由于数据中的完全分离而爆炸。

![5](/figure/fyy_figure5.png)

图4的左栏显示了估计系数的时间序列和logistic回归的四个系数的误差范围，这四个系数分别适用于1952年至2000年的每个选举年的投票数据。除了1964年黑人的截距和系数外，所有的估计都是合理的，当时最大似然估计是无限的。如前所述，我们不认为1964年的这些系数估计是合理的：在整个人口中，我们不认为人口中的非裔美国人投票给共和党的概率为零。然而，完全可以预测的是，在中等样本量的情况下，偶尔会出现分离，从而产生无限的估计。（如前所述，此处显示的1964年的估计值是有限的，因为R中的广义线性模型拟合例程在有限次迭代后停止。）

图4的其他三列显示了使用系数的默认Cauchy先验分布以及$t_7$和正态分布的系数估计。在所有情况下，先验分布都以$0$为中心，对于常数项，尺度参数设置为$10$，对于所有其他系数，尺度参数设置为$2.5$。所有三个先前的分布在稳定1964年种族的估计系数方面做了合理的工作，而其他年份的估计基本保持不变。这个例子说明了弱信息先验分布如何在日常实践中发挥作用。

* **与实际先验信息相比，默认先验信息较弱**

Cauchy（$0,2.5$）先验分布并不代表我们对1964年或任何其他年份logistic回归中黑人系数的先验知识。甚至在看到这一系列调查数据之前，我们就知道黑人比白人更不可能投票给共和党候选人；因此，我们先前认为该系数为负值。此外，任何给定年份的先验知识都将从其他年份的数据中得知。例如，根据图4中的一系列估计，我们可以猜测2004年黑人的系数可能介于$−2$及$−5$。最后，我们没有使用有关这些选举的具体事先知识。1964年的共和党候选人在种族问题上特别保守，反对《民权法案》；基于这些理由，我们预计他在非洲裔美国人中的投票率会特别低（他确实如此）。总之，我们觉得使用一个排除了许多潜在有用信息的默认模型很舒服，同时，如果认为这些信息很重要，我们可以添加这些信息（例如，不是对每个选举年进行单独的估计，而是建立一个分层模型，允许系数随时间逐渐变化，并包括选举级别的预测值，包括候选人在经济和种族问题上的立场等信息）。

## 一些实例

### 警察拦截的层次泊松回归

纽约市和其他地方都有人抱怨警察骚扰少数民族成员。1999年，纽约州总检察长办公室发起了一项对纽约市警察局“停止搜查”政策的研究：即“临时拘留、讯问、有时搜查平民”的合法做法警方有一项保留停车记录的政策，我们在1998-1999年的15个月内获得了所有这些记录（总共约175000份）。我们分析了这些数据，以了解不同种族群体在多大程度上被警方拦截。我们重点关注黑人（非裔美国人）、西班牙裔（拉美裔）和白人（欧洲裔美国人）。种族分类是由停车的警察记录的。我们排除了其他种族的成员（约4%的停车），因为分类可能存在歧义。（由于“其他”的频率如此之低，即使是很小的错误分类率也可能导致该群体的估计值出现很大的扭曲。例如，如果只有4%的黑人、西班牙裔和白人被错误地标记为“其他”，那么这将使“其他”类别的估计值几乎翻一番，而对三个主要类别几乎没有影响。）

***综合数据***

黑人和西班牙裔分别占警察拦截总数的51%和33%，尽管分别只占该市人口的26%和24%。然而，每个种族成员所犯罪行的数量更值得比较。由于无法获得实际犯罪的数据，因此我们使用了刑事司法服务司（DCJS）记录的1997年纽约市的逮捕人数作为代替。这些被认为是按种族分类的当地犯罪率的最佳可用指标。我们使用这些数字来表示警方可能怀疑每个群体的成员所犯罪行的频率。通过这种方式进行比较，白人的停车率与DCJS逮捕率之比为$1.24$，黑人为$1.54$，西班牙裔$1.72$：基于这个比较，黑人比白人多$23\%$，西班牙裔比白人多$39\%$。

***区域控制的回归分析***

到目前为止，分析还着眼于整个城市的平均比率。假设警察在高犯罪率地区进行更多的拦截，但在任何地方都平等对待不同的种族群体。那么，即使停车完全由地点而不是种族决定，全市的比率也可能显示出种族群体之间的巨大差异。为了将这两种预测因素分开，我们使用该市的75个辖区进行了分层分析。由于不同种族组成的社区的模式可能系统性不同，我们根据黑人人口将辖区分为三类：黑人比例低于10%的辖区，10%–40%为黑人的辖区，40%以上为黑人的辖区。每一个都代表了该市大约1/3的辖区，我们对每一组进行了单独的分析。

对于每个种族群体$e=1,2,3$和辖区$p$，我们使用带有种族群体指标的过度分散泊松回归（辖区的分层模型）以及使用$n_{ep}$（该辖区该种族的DCJS逮捕人数）（乘以$15/12$，以15个月为一个比例）对拦截次数$y_{ep}$进行建模。
$$
y_{ep}\sim Poisson(n_{ep}e^{\alpha_e+\beta_p+\epsilon_{ep}})\\
\beta_p\sim N(0,\sigma^2_{\beta})\\
\epsilon_{ep}\sim N(0,\sigma^2_{\epsilon})\tag{12}
$$
其中，系数$\alpha_e$用于控制种族群体，$\beta_p$用于调整辖区之间的差异，而$\epsilon_{ep}$用于考虑过度分离。最令人感兴趣的是指数系数$exp(\alpha_e)$，它表示控制辖区后，相对于逮捕的相对拦截率。

通过比较逮捕率，我们还可以分别分析与不同类型犯罪相关的拦截次数。我们对四种犯罪类型中的每一种进行了单独比较：暴力犯罪、武器犯罪、财产犯罪和毒品犯罪。对于每一种犯罪，我们根据种族$e$和辖区$p$对该犯罪类型的拦截次数进行了建模，并将上一年该犯罪类型的DCJS逮捕率$n_{ep}$作为基线。

![6](/figure/fyy_figure6.png)

<font size=2>图5：以前一年的逮捕率为基线，控制辖区之间的差异，根据分层回归（12）估计的不同种族人群因不同类别犯罪而被拦截的估计比率$exp(\alpha_e)$。分别对黑人人口少于10%、10%–40%和超过40%的辖区进行分析。在暴力犯罪和武器犯罪中，拦截率最高，黑人和西班牙裔被拦截的频率约为白人的两倍。比率以对数标度绘制。</font>

对应于四种犯罪类型和三类辖区，我们估算了12个独立数据子集的模型（12）。我们使用Bugs进行计算。图5显示了估算的比率$exp(\alpha_e)$。对于每种类型的犯罪，不同种族群体的相对拦截频率在每一组辖区中的顺序相同。我们还进行了包括逮捕月份在内的分析。在15个月期间，拦截率大致保持不变，没有为种族群体的比较增加任何信息。

图5显示，在与暴力犯罪和武器犯罪相关的频率最高的拦截中，所有辖区中，黑人和西班牙裔更可能被拦截。在暴力犯罪中，黑人和西班牙裔被拦截的次数分别是白人的$2.5$倍和$1.9$倍。在武器犯罪中，黑人和西班牙裔被拦截的次数是白人的$1.8$倍和$1.6$倍。在其他两类中，白人被拦截的次数略多，这与他们去年在任何特定辖区的被捕人数成比例。

少数族裔人数过多的总体格局是否意味着纽约警察局以不公平或种族主义的方式行事？一点也不。合理的假设是，有效的警务需要对许多人进行拦截和审问，以收集有关任何特定犯罪的信息。在双方关系困难的情况下纽约市的警察和少数族裔社区，对争议中的问题有一些定量的认识是有用的。鉴于有人抱怨警察频繁地拦截黑人和西班牙裔，需要了解这确实是一种统计模式。警察局有向受影响社区解释其政策的机会。

### 政治观点的分层logistic回归实例--方差分析的应用

在每次选举前，媒体组织都会进行几十次全国性的民意调查，人们希望能够评估各个州以及整个国家的民意。这些民意调查通常基于全国性的随机数字拨号，并根据人口统计因素（如性别、民族、年龄和教育）对无应答者进行更正。我们从这些民意调查中估计了州一级的意见，同时分两步更正了无回应。对于任何感兴趣的调查回应：

1. 我们为给定的人口统计和状态拟合了个人回应$y$的回归模型。因此，该模型估计了人口统计和状态的每个交叉分类$j$的平均反应$\theta_y$。在我们的例子中，我们有性别（男性或女性）、种族（非裔美国人或其他）、年龄（4类）、教育（4类）和50个州；有3200个类别。
2. 根据美国人口普查，我们得到了每个类别$j$的成年人口$N_j$。在任何州$s$中，回应$y$的估计人口平均值为$\theta_s=\sum_{j\in s}N_j\theta_j/\sum_{j\in s}N_j$，该州64个人口统计类别的总和。

我们需要大量的分类，因为（a）我们有兴趣按州划分回答，（b）无回应调整迫使我们将人口统计数据包括在内。因此，任何给定的调查在许多类别中都只有很少或没有数据。但是，如果采用多水平模型，这不是问题。模型中的每个因素或交互作用集对应于方差分析图中的一行，并自动给出方差分量。

正如调查抽样文献中所讨论的，在估计州一级的结果时，这种推理程序运行良好，并且优于标准调查估计。例如，我们选择了一个单一的结果，即根据1988年总统选举前一周进行的七次哥伦比亚广播公司新闻民意调查的logistic回归模型，受访者倾向于共和党总统候选人的概率。我们将重点放在估计过程的第一阶段——logistic回归模型的推断，并使用方差分析工具显示模型中每个因素的相对重要性。

对于共和党候选人的支持者，我们将调查结果$y_i$标注为$1$；对于民主党的支持者，我们将其标注为$0$（未决定者除外），并将其建模为独立的，且$Pr(y_i=1)=logit_(-1)(X_i\beta)$。设计矩阵$X$均为$0$和$1$，包含CBS在调查权重中使用的人口统计变量指标：性别、种族、年龄、教育以及性别$\times$种族和年龄$\times$教育的相互作用。我们还将50个州和全国4个地区（东北、中西部、南部和西部）的$X$指标包括在内。由于州嵌套在地区内，因此不需要对州产生主要影响。与我们对线性模型的一般方法一样，我们为每批回归系数提供一个以零为中心的独立正态分布，并在给定统一先验密度的情况下，按层次估计标准偏差。

![7](/figure/fyy_figure7.png)

<font size=2>图6：方差分析显示了两个logistic回归模型，该模型基于七次哥伦比亚广播公司新闻调查的数据，表明调查对象倾向于共和党候选人参加1988年美国总统选举。点估计和误差条显示了有限总体标准偏差$s_m$的后中位、50%区间和95%区间。人口统计因素是CBS用来进行无回应调整的因素，之所以将州和地区包括在内，是因为我们想要按州估算平均意见。对种族、地区和州的巨大影响表明，将互动包括在内可能是有意义的，因此在第二个模型中包含了种族$\times$地区和种族$\times$州的效应。</font>

我们对模型进行拟合，并使用后验模拟计算有限样本标准差并绘制结果。图6的左图显示了方差分析，这表明种族是迄今为止最重要的人口因素，州也解释了大部分差异。

下一步是考虑最重要的影响之间的相互作用，如图6右侧的图所示。种族$\times$州$\times$地区的相互作用非常大：非洲裔美国人和其他人之间的差异因州而异。方差分析是理解层次模型中不同组成部分重要性的有用工具。

## 推广

### 多元或多项式响应模型

***多变量结果***

我们使用二元正态分布的二项数据模型对元分析实例进行分析，该模型用于描述个体研究的参数。

* **实例：具有二项式结果的元分析**

22项临床试验的结果通过一个$2\times2$的两种治疗下的死亡和存活表进行总结，我们对治疗对死亡概率的影响分布感兴趣。第5.6节的分析基于经验对数优势比的正态近似值研究。由于样本量大，在这种情况下，正态近似相当准确，但对于一般问题，需要有更精确的程序。

此外，第5.6节中的单变量分析使用了每个试验中死亡率的比率，而不是平均值；如果平均死亡率与治疗效果相关，即使在大样本情况下，忽略这些信息也会产生影响。

*数据模型* 

$y_{ij}$为接受治疗$i=0,1$和研究$j=1,...,22$的$n_{ij}$患者的死亡人数。在前面的讨论中，我们采用$i=1$表示治疗组，因此对数优势比的负值表示治疗组的死亡频率降低。我们的数据模型是二项式的：
$$
y_{ij}|n_{ij,p_{ij}}\sim Bin(n_{ij},p_{ij})
$$
其中$p_{ij}$是研究$j$中治疗$i$下的死亡概率。我们现在必须对44个参数$p_{ij}$进行建模，这自然遵循多变量模型，因为它们分为22组，每组两个。我们首先$p_{ij}$将转换为logit标度，因此他们可以在范围$(-\infty,+\infty)$内定义，以及用正态分布拟合。

*根据转化参数的层次模型*

我们将正常模型转换为每个实验的平均和差异效应：
$$
\beta_{1j}=(logit(p_{oj})+logit(p_{1j}))/2\\
\beta_{2j}=logit(p_{1j})-logit(p_{0j})\tag{13}
$$
参数$\beta_{2j}$对应于第5.6节中的$\theta_j$。我们将22个可交换对$(\beta_{1j},\beta_{2j})$建模为具有未知参数的二元正态分布：
$$
p(\beta|\alpha,\Lambda)=\prod_{j=1}^{22}N((^{\beta_{1j}}_{\beta_{2j}})|(^{\alpha_1}_{\alpha_2}),\Lambda))
$$


这相当于参数对上的正态模型$(logit(p_{0j}),logit(p_{1j}))$；然而，线性变换应使$\beta$在其总体分布中保持大致独立，使我们的推断对其相关性的先验分布不太敏感。

*超先验分布*

对于参数$\alpha_1$和$\alpha_2$，我们使用通常的非信息一致先验分布。对于分层方差矩阵$\Lambda$，没有标准的非信息选择；对于这个问题，我们为方差$\Lambda_{11}$和$Lambda_{22}$以及相关性$\rho_{12}=\Lambda_{12}/(\Lambda_{11}\Lambda_{22})^{1/2}$分配独立的均匀先验分布，得出的后验分布是正确的。

*后验计算*

我们以基于连续近似的常规方法从后验分布中提取样本。（使用Stan可以很容易地拟合此模型，但我们使用此示例来说明如何直接构造此类计算。）就计算机时间而言，我们这里使用的计算方法几乎肯定不是最有效的，但它相对容易以通用方式编程并产生可信的推论。该模型根据$\beta$、$\beta$、$log(\Lambda_{11})$、$log(\Lambda_{22})$和相关的费歇尔的$z$转化$\frac{1}{2}log(^{1+\rho_{12}}_{1-\rho_{12}})$进行参数化，将参数范围转换为整条实线。我们从基于条件模式的近似中随机抽取样本，然后进行重要性重采样，以获得Metropolis算法十次并行运行的起点。我们使用了一个正常的跳跃核，其协方差来自模式的后验密度曲率，比例因子为$2.4/\sqrt{49}$（因为跳跃是在49维空间中）。模拟运行了40000次迭代，此时所有参数的估计缩尺$\widehat{R}$均低于$1.2$，且大多数都低于$1.1$。我们使用从模拟序列的后半部分得到的200000个$(\beta,\alpha,\Lambda)$模拟来总结表2中的后验分布。

![8](/figure/fyy_figure8.png)

<font size=2>表2：表5.4中$\beta$受体阻滞剂试验元分析的双变量分析的后验推断总结。所有的影响都在对数赔率范围内。推论与第5.6节中logit差异的单变量分析结果相似：将个体研究效应与表5.4进行比较，将平均logit的平均值和标准偏差与表5.5进行比较。”Study1 avg logit&apos;作为22个参数$\beta_{1j}$的代表包含在上述研究中。（我们通常倾向于以图形方式显示所有这些推论，但使用此处的表格提供后验推论的更详细视图。）</font>

*后验模拟结果*

$\rho_{12}$的后验分布集中在$0.21$附近，具有相当大的可变性。因此，多变量模型对从单变量分析中获得的关于个体研究的对数优势比或相关分层参数的后验推断只有很小的影响。将表2中的结果与表5.4和表5.5中的结果进行比较，结果表明推论是相似的。基于精确后验分布的多变量分析修复了先前分析中要求的正态近似中的任何缺陷，但并未显著改变对重要数量的后验推断。

***logistic关系的推广***

多分类数据的适当模型可以发展为泊松模型或二项模型的扩展。我们首先展示了如何将二进制数据的logistic模型扩展到处理多项式数据。具有样本量$n_i$和$k$中可能的结果的多项式随机变量$y_i$的表示为（即，$y_i$是向量，$\sum^k_{j=1}y_{ij}=n_{ij}$）$y_i\sim Multin(n_i;\alpha_{i1},\alpha_{i2},...,\alpha_{ik})$，$\alpha_{ij}$表示第$j$类的概率，并且$\sum^k_{j=1}\alpha_{ij}=1$。多项式广义线性模型参数化的标准方法是根据每个类别相对于基线类别的概率比率的对数，我们记$j=1$，则：
$$
log(\alpha_{ij}/\alpha_{i1})=\eta_{ij}=X_i\beta^{(j)}
$$
其中，$\beta^{(j)}$是第$j$类的参数向量。然后对数据分布进行分析：
$$
p(y|\beta)\propto\prod_{i=1}^n\prod_{j=1}^k(\frac{e^{\eta_{ij}}}{\sum^k_{l=1}e^{\eta_{il}}})^{y_{ij}}
$$
$\beta^{(1)}$设为$0$，因此$\eta_{i1}=0$。向量$\beta^{(j)}$表示$X$的变化对观察$j$类相对于1类结果的概率的影响。通常，线性预测器包括一组用于结果类别的指标变量，当解释变量取默认值$X=0$时，指示相对频率；在这种情况下，我们可以将$\delta_j$作为$j$类和$\eta_{ij}=\delta_j+X_j\beta^{(j)}$的指标系数，其中$\delta_1$和$\beta^{(1)}$通常设为$0$。

***有序类别的特殊方法***

有序类别（例如，$A、B、C、D$级）的多项式结果与标称类别（例如，疾病）的多项式结果之间存在区别。对于有序类别，广义线性模型通常用累积概率$\pi_{ij}=\sum_{l\leq j}\alpha_{il}$表示，而不是类别概率$log(\frac{\pi_{ij}}{1-\pi_{ij}})=\delta_J+X_i\beta^{(j)}$（通常取$\delta_1=\beta^{(1)}=0$）。由于类别的排序，可以合理地考虑具有一组共同的回归参数$\beta^{j}=\beta$的模型。有序类别的另一个常见选择是多项式概率模型。这些模型中的任何一个都可以用潜变量形式表示。

***多项式响应的泊松模型***

多项式响应数据也可以使用泊松模型通过适当的总数进行分析。假设$y=(y_i,...,y_k)$是独立的泊松随机变量，平均数$\lambda=(\lambda_1,...,\lambda_k)$。那么$y$的条件分布（$n=\sum^k_{j=1}y_j$）：
$$
p(y|n,\alpha)=Multin(y|n;\alpha_1,...,\alpha_k)\tag{14}
$$
其中，$\alpha_j=\lambda_j/\sum^k_{i=1}\lambda_i$。此关系还可用于允许使用泊松广义线性模型拟合具有多项响应变量的数据。通过在泊松回归中加入附加的协变量，对多项概率之和施加约束，泊松回归的系数分配为均匀先验分布。

### 多元离散数据的对数线性模型

描述多个分类变量之间关联的标准方法使用对数线性模型族。在对数线性模型中，响应或结果变量是多变量和离散的：计数列联表根据多个分类变量交叉分类。计数建模为泊松随机变量，而泊松平均数的对数由一个线性模型描述，该模型包含各种分类水平的指标变量。或者，如果总样本量或一些边际总数是设计固定的，则表中每个边缘的计数可以建模为多项式随机变量。对数线性模型可以这是广义线性模型的一个特例。对数线性模型通常用于多元离散数据分析，特别是多重插补。因为有一种替代的计算策略，当人们的兴趣集中在期望的计数上，并且使用共轭先验分布时，这种策略是有用的。

### 广义线性模型在R中的使用

R中用glm(formula,family...)函数来做广义线性模型，不同分布族下的默认连接函数如下表3所示：

![9](/figure/fyy_figure9.png)

与glm()函数经常连用的函数如下表4所示：

![10](/figure/fyy_figure10.png)

***logistic回归的拓展***

稳健logistic回归：robust包中的glmRob()函数，解决离群点和强影响点的问题。


多项分布回归：mlogit包中的mlogit()函数，应用于响应变量包含两个以上的无序类别。

序数Logistic回归：rms包中的lrm()函数，应用于响应变量是一组有序类别。

***泊松回归的拓展***

时间段变化的泊松回归：在glm()函数里增加offset参数，offset = log(time)。

零膨胀的泊松回归：pscl包中的zeroinfl()函数可做零膨胀泊松回归。

稳健泊松回归：使用robust包中的glmRob()函数。
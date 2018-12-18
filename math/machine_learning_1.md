---
title: 机器学习笔记（一）
layout: math
---

# 机器学习笔记（一）

## 机器学习内容大纲

这一节介绍机器学习的大纲，即本文包含哪些内容。首先，我们介绍机器学习的定义。然后，我们介绍机器学习的四个主题，即监督学习，学习理论，非监督学习，以及强化学习。

机器学习的一个简单定义是：不显式地编程，而让计算机获得学习能力。这个定义由Arthur Samuel在1959年给出。

>Field of study that gives computers the ability to learn without being explicitly programmed.  --Arthur Samuel (1959)

一个正式的定义：一个良好定义的学习问题是指，如果给定任务T，在评判标准P下，一个计算机程序能够通过获得经验E提高完成任务T的能力，就称这个计算机程序，关于T和P，能够从E中学习。这个定义由Tom Mitchell在1998年给出。

>Well-posed Learning Problem: A computer program is said to learn from experience E with respect to some task T and some performance measure P, if its performance on T, as measured by P, improves with experience E.  --Tom Mitchell (1998)

机器学习的基础知识包括以下几个方面的内容。

**监督学习**。对于这种学习算法，我们提供训练数据和正确答案，并期望这个算法在面对其他数据时能够给出正确答案。

回归问题是监督学习问题的一种。例如，给定一系列房屋的大小和价格，用线性或者二次曲线对数据进行拟合。最后，给定房屋大小，用拟合的函数来预测房屋的价格。

分类问题是另一种监督学习问题。例如，给定一系列肿瘤的数据，并标记这些肿瘤为良性或者恶性。通过学习后，算法能够针对新的数据预测肿瘤是良性或者恶性。

> Alvin系统是一个监督学习类型的自动驾驶系统。Alvin系统通过学习人类司机的驾驶行为来学习驾驶。汽车上装有摄像头，用来拍摄前方图像。同时，方向盘上装有传感器，用来监测方向盘的转向。在训练时，Alvin系统每两秒拍摄一次图像，同时记录人类司机控制方向盘的角度，作为监督学习中的“正确答案”。通过这些数据训练之后，Alvin系统可以根据前方的图像，输出自己认为的最佳方向盘角度，以及对这个结果的自信程度。这个过程在不同类型的路面上重复，得到不同类型的预测算法。在自动驾驶时，Alvin系统每秒拍摄12次图像，同时运行不同类型路面的预测算法，并根据自信程度最高的算法给出的结果进行转向。

**学习理论**。这个主题用来解释为什么机器学习算法能够起作用，算法什么时候能够表现更好，以及影响算法好坏的因素。这些理论能够帮助判断是该收集更多数据还是花更长时间训练。机器学习的工具其实数量有限，精通机器学习的人，能够让同样的工具发挥更大作用，能够把机器学习的理论应用于解决现实生活中的问题。

**非监督学习**。对于这种学习算法，我们输入一个数据集，却不提供任何答案，期望这个算法能够在数据中发现有趣的结构。

聚类问题是非监督学习的一个例子。这类问题在现实生活中应用很多：处理基因数据，处理图片（将属于同一物体的像素聚类）。聚类算法甚至可以仅仅通过一张图片建立环境的3D模型（其实需要结合监督和非监督学习，不过聚类算法是第一步）。

非监督学习还可以解决鸡尾酒会问题。在鸡尾酒会中，人能够准确将自己感兴趣的声音从嘈杂的背景音中区分出来。非监督学习算法能够将两个同时说话的人的声音区分开来，或者从背景音乐中提取出说话声。

**强化学习**。在强化学习中，不像监督学习和非监督学习那样只做一次性判断。强化学习算法需要作出一系列的判断以达到目标。强化学习算法的关键是奖励函数，这个函数直接定义了你想要达成的目标。

这一节介绍了机器学习的大纲，包括机器学习的定义，以及监督学习，学习理论，非监督学习和强化学习这四个主题。

## 线性回归

线性回归模型是监督学习中的基础模型。我们首先描述问题，建立问题的数学模型，这其中会介绍用到的数学符号和变量。我们会介绍如何用梯度下降（Gradient Descent）求解线性回归问题。最后，我们用矩阵重新描述这个线性模型，并给出线性回归问题的理论求解方程。

我们想要根据已有的房屋面积和价格数据建立模型，用来根据房屋面积预测价格。我们用$m$表示训练样本大小（这里指已有的房屋数据数量），$x$表示输入变量，或者说特征（这里指房屋面积），$y$表示输出变量，即预测目标（这里指房屋价格）。单个训练样本用$(x,y)$表示。训练集中第$i$个样本则表示为$(x^{(i)},y^{(i)})$。我们希望通过这$m$个样本数据，得到一个预测函数$h$，$h(x)$即为针对输入$x$预测的结果。在线性模型中，$h(x)=h\_{\theta}(x)=\theta\_0+\theta\_1 x$。其中$\theta$为参数。我们的目标就是得到一个$\theta$，使得$h\_{\theta}$的预测尽可能准确。

一个房屋可能有不止一个特征影响到价格，除了大小之外，可能还有房间数，因此样本输入变量$x$可能是具有多个维度的向量。一般地，设一个样本的特征数量为$n$。于是$x$就是$n$维向量。为了简便起见，把$\theta$看作$n+1$维向量，把$x\_0$看作常数$1$，则$x$也可以看作$n+1$维向量。于是$h\_{\theta}(x)$可以表示为$\sum\_{i=0}^n \theta\_i x\_i$。

我们的目标是选择合适的$\theta$，最小化$h\_{\theta}(x)$的预测结果相对于目标变量的差错。我们选择最小化$h\_{\theta}(x)$在所有样本上预测的差错的平方和。我们把这个目标函数记为$J(\theta)$

$$
J(\theta)=\frac{1}{2}\sum_{j=1}^m (h_{\theta}(x^{(j)})-y^{(j)})^2
$$

> 这里的系数1/2的作用接下来就会看到。

我们的目标是最小化$J(\theta)$，即

$$
\min_{\theta} J(\theta)
$$

**梯度下降算法**是常见的优化算法。这个算法选取一个$\theta$的初始值（比如零向量），不断更新$\theta$的值，每次更新就缩小一次$J(\theta)$。每次更新时，计算出在当前位置下降最快的方向，也就是负梯度方向，并在这个方向上将$\theta$移动一小步。重复这一过程直到收敛。用数学公式表示，每一步做的计算为

$$
\theta_i:=\theta_i-\alpha\dfrac{\partial J(\theta)}{\partial \theta_i},\quad 0\leq i\leq n
$$

这里$\alpha$为学习速率，决定了每一步走多大。

接下来，我们将上式展开

$$
\begin{eqnarray*}
\frac{\partial J(\theta)}{\partial \theta_i} &=& \frac{1}{2}\sum_{j=1}^m \frac{\partial}{\partial \theta_i}(h_{\theta}(x^{(j)})-y^{(j)})^2 \\
&=& \sum_{j=1}^m (h_{\theta}(x^{(j)})-y^{(j)})\cdot\frac{\partial}{\partial \theta_i} (h_{\theta}(x^{(j)})-y^{(j)}) \\
&=& \sum_{j=1}^m (h_{\theta}(x^{(j)})-y^{(j)})\cdot\frac{\partial}{\partial \theta_i}(\sum_{i=0}^n \theta_i x_i^{(j)}-y^{(j)}) \\
&=& \sum_{j=1}^m (h_{\theta}(x^{(j)})-y^{(j)})\cdot x_i^{(j)}
\end{eqnarray*}
$$

其中第一个等式是根据$J(\theta)$的定义直接展开。第二个等式应用了求导的链式法则。第三个等式将$h\_{\theta}$按照定义展开，第四个等式是因为除了$\theta\_i x\_i^{(j)}$这一项外，其他项和$\theta\_i$无关，导数直接归零。

因此，我们得到了最终的梯度下降算法：迭代执行下面的公式，直到收敛

$$
\theta_i:=\theta_i-\alpha\sum_{j=1}^m (h_{\theta}(x^{(j)})-y^{(j)})\cdot x_i^{(j)},\quad 0\leq i\leq n
$$

有两个需要注意的点：

1. 收敛的评判条件。常用的方法是比较一次迭代前后$\theta$的差，如果小于一定阈值，则认为收敛。
2. 上述梯度下降算法是最普通的形式，即批量（Batch）梯度下降，每次迭代时用上全部的数据。当数据量（即$m$）巨大时，每次迭代所需的计算量会很大。在这种情况下，可以采取随机梯度下降方法（Stochastic Gradient Descent），即每一步只选取一个样本迭代。这个样本可以随机选取，也可以按顺序选取。按顺序选取时，每一步迭代公式为

$$
\theta_i:=\theta_i-\alpha (h_{\theta}(x^{(j)})-y^{(j)})\cdot x_i^{(j)},\quad 0\leq i\leq n,\quad 1\leq j\leq m
$$

你可能会觉得对$j$循环与用连加号求和没什么不同。实际上，对$j$循环和连加的区别是，连加号对每个$j$都是用同一个$\theta$计算的，而此时是用更新过的$\theta$计算下一个$j$。

我们用矩阵算子重新描述这个过程。首先介绍计算向量梯度的$\nabla$算子。记

$$
\nabla_{\theta}J=\begin{bmatrix}
\frac{\partial J}{\partial \theta_0}\\
\vdots\\
\frac{\partial J}{\partial \theta_n}
\end{bmatrix}\in\mathbb{R}^{n+1}
$$

于是每次迭代更新$\theta$就可以表示为

$$
\theta:=\theta-\alpha\nabla_{\theta}J(\theta)
$$

将每个样本$x^{(j)}$看作行向量，$m$个样本组成的$m\times n$的矩阵称为**设计矩阵**，记为$X$。于是就有

$$
\begin{eqnarray*}
X\theta&=&\begin{bmatrix}(x^{(1)})^T\\\vdots\\(x^{(m)})^T\end{bmatrix}\theta\\&=&\begin{bmatrix}(x^{(1)})^T\theta\\
\vdots\\
(x^{(m)})^T\theta\end{bmatrix}=\begin{bmatrix}h_{\theta}(x^{(1)})\\\vdots\\h_{\theta}(x^{(m)})\end{bmatrix}
\end{eqnarray*}
$$

将所有样本目标$y$组成的$m$维向量记为$\vec{y}$。于是

$$
X\theta-\vec{y}=\begin{bmatrix}h_{\theta}(x^{(1)})-y^{(1)}\\\vdots\\h_{\theta}(x^{(m)})-y^{(m)}\end{bmatrix}
$$

于是$J(\theta)=\frac{1}{2}(X\theta-\vec{y})^T(X\theta-\vec{y})$。

接下来，为了求出$J(\theta)$的矩阵表示，我们介绍矩阵的$\nabla$算子，以及一些相关的公式。

设$f:\mathbb{R}^{m\times n}\to\mathbb{R}$为一个矩阵空间上的函数，$A\in\mathbb{R}^{m\times n}$。记

$$
\nabla_A f(A) = \begin{bmatrix}
\frac{\partial f}{\partial A_{11}} & \cdots & \frac{\partial f}{\partial A_{1n}}\\
\vdots & & \vdots \\
\frac{\partial f}{\partial A_{m1}}&\cdots& \frac{\partial f}{\partial A_{mn}}
\end{bmatrix}
$$

如果$m=n$，即$A$为方阵，记矩阵的迹（trace）为$tr(A)=\sum\_{i=1}^n A\_{ii}$。

关于矩阵的$\nabla$算子和迹，以下公式成立（注意$A$和$B$并不一定是方阵）

1. $tr(AB)=tr(BA)$
2. $tr(ABC)=tr(CAB)=tr(BCA)$
3. $\nabla\_A tr(AB)=B^T$
4. $\nabla\_A tr(ABA^TC)=CAB+C^TAB^T$

应用以上公式，我们计算$J(\theta)$的梯度

$$
\begin{eqnarray*}
\nabla_{\theta}J(\theta) &=& \frac{1}{2}\nabla_{\theta}(X\theta-\vec{y})^T(X\theta-\vec{y}) \\
&=& \frac{1}{2}\nabla_{\theta}(tr(\theta^TX^TX\theta)-2tr(\theta^TX^T\vec{y})+\vec{y}^T\vec{y}) \\
&=& \frac{1}{2}(X^TX\theta+X^TX\theta)-X^T\vec{y} \\
&=& X^TX\theta-X^T\vec{y}
\end{eqnarray*}
$$

这里，第一个等式是之前计算过的。第二个等式来自于乘法的展开，以及常数的trace就是它本身。第三个等式应用了上面的四个公式：首先应用第二个公式$tr(\theta^TX^TX\theta)=tr(\theta\theta^TX^TX)$，然后将其看做$\theta\cdot I\cdot\theta^T\cdot(X^TX)$，应用第四个公式；最后，应用第三个公式$\nabla\_{\theta} tr(\theta^TX^T\vec{y})=X^T\vec{y}$。

于是，我们得到了$J(\theta)$的梯度的矩阵表示。

利用这个公式，我们还可以直接求出最低点时$\theta$的理论值。令$\nabla\_{\theta}J(\theta)=0$，于是有

$$
\theta=(X^TX)^{-1}X^T\vec{y}
$$

这一节中，我们讲了线性回归模型。我们首先举了房屋价格预测的例子，并建立数学模型，然后用梯度下降算法求解模型。最后，我们给出了模型的矩阵表示，以及在矩阵表示下$\theta$的理论解。

### 加权线性回归

如果拿到的数据一看就不是线性的，这时候该怎么拟合呢？一般的做法是考虑将$h(x)$设为二次或更高次数的函数。不过，这个次数的设定很难把握，如果太高，则容易过拟合；如果太低，容易拟合不足。

用加权线性回归解决这个问题的思路是每次只拟合其中一小部分数据，这一小部分局部的数据是比较线性的。当然，这一小部分数据必须在需要预测的$x$的附近。因此，加权线性回归和普通线性回归的区别是对每个需要预测的点都拟合一次。因此，加权线性回归属于另一类学习算法：非参数学习算法。

线性回归的目的是训练一组参数$\theta$，训练好之后就可以把训练数据丢掉了，只根据$\theta$来做预测，因此线性回归属于参数学习算法。而非参数学习算法是指这样一类算法：参数的数量随着训练集大小的增加而增加。

对于加权线性回归而言，因为每次预测时都需要针对被预测的点重新拟合一次，所以整个训练集就作为参数保存在模型中。

加权线性回归的优点就是不需要关心训练数据特征的问题，只要在每个点附近的数据都是近似线性的就可以了。

和线性回归一样，加权线性回归也需要最小化损失函数$J(\theta)$。不过，$J(\theta)$的表达式和普通线性回归中有些区别，即对每个样本的误差乘以一个权重$w$

$$
J(\theta)=\frac{1}{2}\sum_{j=1}^m w^{(j)}(h_{\theta}(x^{(j)})-y^{(j)})^2
$$

一般将这个权重取为钟形曲线，中心在被预测的点$x$。即

$$
w^{(j)} = \exp
\left\{\frac{-(x^{(j)}-x)^2}{2\tau^2}\right\}
$$

其中$\tau$决定了这个钟形曲线的宽度。

对于$\theta$而言，权重$w$为常数，所以它的梯度计算和一般的线性回归类似，这里就不再重复了。

### 线性回归模型的概率解释

线性回归模型的损失函数$J(\theta)$为什么要用误差的平方和？实际上，$J(\theta)$的设置有概率上的意义。这种从概率的角度解释一个模型的方法，在下一节中直接引出分类问题的学习算法的推导。

假设对每个样本$j$

$$
y^{(j)}=\theta^T x^{(j)}+\epsilon^{(j)}
$$

其中$\epsilon$是独立同分布的噪音，表示没有被模型考虑到的因素。假设$\epsilon$满足高斯分布$N(0,\sigma^2)$。于是，给定$x$的情况下，$y$的分布为$N(\theta^T x, \sigma^2)$。

用$L(\theta)$表示在给定$\theta$的情况下，$\vec{y}$的分布函数。因为$\epsilon$是独立的，所以

$$
L(\theta)=\prod_{j=1}^m \frac{1}{\sqrt{2\pi}\sigma} \exp\left\{-\frac{(y^{(j)}-\theta^T x^{(j)})^2}{2\sigma^2}\right\}
$$


直观上，如果$\vec{y}$在某个$\theta$下的分布函数中的概率比较高，那么这个$\theta$就更接近真正的$\theta$。因此选择的$\theta$要最大化$L(\theta)$。这等价于最大化$\log L(\theta)$，而后者更方便计算，所以设$l(\theta)=L(\theta)$，于是

$$
\begin{eqnarray*}
l(\theta)&=&\log L(\theta)=\log\prod_{j=1}^m \frac{1}{\sqrt{2\pi}\sigma} \exp\left\{-\frac{(y^{(j)}-\theta^T x^{(j)})^2}{2\sigma^2}\right\} \\
&=& \sum_{j=1}^m \log\left( \frac{1}{\sqrt{2\pi}\sigma} \exp\left\{-\frac{(y^{(j)}-\theta^T x^{(j)})^2}{2\sigma^2}\right\} \right) \\
&=& m\log\frac{1}{\sqrt{2\pi}\sigma}+\sum_{j=1}^m\left(-\frac{(y^{(j)}-\theta^T x^{(j)})^2}{2\sigma^2}\right)
\end{eqnarray*}
$$

因此，最大化$l(\theta)$，就等于最小化$\sum\_{j=1}^m (y^{(j)}-\theta^T x^{(j)})^2$。

## 分类问题

分类问题是监督学习中的另一个基础问题。一个典型的分类问题是这样的：假设有一些肿瘤的数据，比如$m$个，每个肿瘤有一些特征，如大小、存在时间、患者年龄等，以及一个目标变量$y$，表示肿瘤是否为良性。如果$y=1$则代表良性，如果$y=0$则代表恶性。现在需要训练一个模型，用给定数据进行训练，使得模型在给定新的肿瘤特征时，能够预测肿瘤是否为良性，即$y$是0还是1。

显然，如果用线性回归模型来求解分类问题，得到的模型的预测结果会相当糟糕。通常，求解这种01分类问题时，会使用一个新的预测函数$h\_{\theta}(x)​$，这个函数和线性回归相比，仅仅是在最外层嵌套一个Signoid函数，用来把线性模型的结果压缩在区间$[0,1]​$中。

$$
g(x)=\frac{1}{1+e^{-x}}
$$

于是，分类问题中的预测函数为

$$
h_{\theta}(x)=g(\theta^T x) = \frac{1}{1+e^{-\theta^T x}}
$$

有了预测函数，怎样量化这个预测函数的误差呢？和线性模型不同，这里不再直接把预测函数的输出当做目标值（因为目标值是离散的），而是当做一个概率，即预测目标为1的概率。于是，对于$y=1$的样本，预测正确的概率为

$$
P(y=1\mid x;\theta) = h_{\theta}(x)
$$

类似地，对于$y=0$的样本，预测正确的概率为

$$
P(y=0\mid x;\theta)=1-h_{\theta}(x)
$$

将两个等式综合，预测正确的概率为

$$
P(y\mid x;\theta) = h_{\theta}(x)^y(1-h_{\theta}(x))^{1-y}
$$

令$L(\theta)$为对所有样本预测正确的概率，则

$$
L(\theta)=\prod_{j=1}^m P(y^{(j)}\mid x^{(j)}; \theta) = \prod_{j=1}^m h(x^{(j)})^{y^{(j)}}(1-h(x^{(j)}))^{1-y^{(j)}}
$$

于是最佳的$\theta$就是能最大化这个函数的值。同样，考虑最大化它的对数

$$
l(\theta)=\log L(\theta)=\sum_{j=1}^m \log h(x^{(j)})+(1-y^{(j)})\log (1-h_{\theta}(x^{(j)}))
$$

求其梯度得

$$
\frac{\partial}{\partial \theta_i} l(\theta) = \sum_{j=1}^m (y^{(j)}-h_{\theta}(x^{(j)}))x_i^{(j)}
$$

于是得到最终的梯度下降算法的每次迭代的公式

$$
\theta_i:=\theta_i+\alpha \sum_{j=1}^m (y^{(j)}-h_{\theta}(x^{(j)}))\cdot x_i^{(j)}
$$

注意到这个公式和线性回归的迭代公式几乎一样，区别只是第一个减号变成了加号（因为这里不是在最小化误差函数，是在最大化概率），以及$h\_{\theta}$的涵义不同。


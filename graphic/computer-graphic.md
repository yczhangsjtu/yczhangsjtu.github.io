---
title: 计算机图形学基础
layout: math
---

### 计算机图形学基础

这里讨论的内容特指3D图形学。计算机处理3D图形的过程主要分为三步：建模（Modeling），动画（Animation），绘制（Rendering）。这里首先介绍建模，然后介绍绘制，最后介绍动画。

建模部分负责构造要被绘制的3D对象，简单来说就是告诉绘制部分“什么地方有什么”。绘制部分负责把3D对象渲染出来。目前绘制技术包括两大类，栅格（Rasterization）和光线追踪（Raytracing）。这两类技术的思路刚好相反。栅格技术的出发点是3D对象，判定一个3D对象应该怎样绘制到屏幕上。光线追踪考虑的则是屏幕上的每个像素，分析这个像素该渲染成什么颜色，方法是追踪从这个点出发的光线。光线追踪技术绘制的画面更加逼真，但绘制的代价也更高。

首先从最基础的数学部分讲起。这里假设读者对大一课上学的基础的线性代数非常熟悉，所以对向量是什么、矩阵是什么、矩阵的乘法以及线性变换等等最基本的东西就不再介绍了。

#### 外积

外积这种运算，因为是三维空间特有的，一般线性代数课上很少涉及，在空间几何中却是用处巨大。

设有两个向量$a$和$b$，它们的外积仍然是向量，记为$c$。

* $c$的方向：和$a$与$b$都垂直，换句话说$c$和$a$与$b$决定的平面（如果$a$和$b$不平行的话）垂直。具体方向由右手定则判定；
* $c$的大小：$\|c\|=\|a\|\cdot\|b\|\sin\theta$，这里$\theta$为$a$和$b$的夹角（所以如果$a$和$b$平行，$c=0$）；
* 外积不满足交换律或结合律，但满足分配率：$a\times(b+c)=a\times b+a\times c$；
* 外积可以用行列式计算
$$a\times b=\left|\begin{matrix}e_x & e_y & e_z \\ x_a & y_a & z_a \\ x_b & y_b & z_b\end{matrix}\right|$$
这里$e\_x,e\_y,e\_z$为三个方向上的单位向量。
* 外积也可以用如下公式计算：$a\times b=A^\*b$。这里$A^\*$是$a$的对偶矩阵.
$$A^*=\begin{bmatrix}0 & -z_a & y_a \\ z_a & 0 & -x_a \\ -y_a & x_a & 0\end{bmatrix}$$

外积这种和原来的向量都垂直的特性是它最有用的特性。计算机图形学中常常用外积来计算和两个给定向量都垂直的向量。

#### 坐标系

一个坐标系（Coordinate Frame）由三个向量$u,v,w$表示，这三个向量满足

* 单位向量
* 两两正交
* $u\times v=w$

显然，以$u,v,w$按顺序为行向量组成的矩阵，就是一个行列式为1的三阶单位正交阵。反过来，任意一个行列式为1的三阶单位正交阵的三个行向量也可以构成一个坐标系。

#### 变换

变换是建模中最重要的概念之一。一个对象的大小、位置、方向，全部由变换决定。不同对象之间的相对位置和方向，也都由变换来表示。

3D图形学中用到的变换，全都是线性变换。也就是说，所有变换都可以表示成一个矩阵。变换的组合，也就对应着矩阵的乘法。

下面介绍基本变换。一般的变换可以通过这些基本变换组合出来。

**放缩变换:**
$$\begin{bmatrix} s_x & 0 & 0 \\ 0 & s_y & 0 \\ 0 & 0 & s_z \end{bmatrix}$$

三个方向分别乘以对应的放缩倍数。

**剪切变换:**
$$\begin{bmatrix}1 & a \\ 0 & 1\end{bmatrix}$$

剪切变换的效果如下图所示。

![image-20180905104423587](/assets/images/shear.png)

**2D旋转:** 
$$\begin{bmatrix}\cos\theta & -\sin\theta \\ \sin\theta & \cos\theta\end{bmatrix}$$

之所以把二维变换和三维变换分开来讲，是因为它们的区别实在太大了。2D旋转满足交换律，两个旋转的组合就是它们的旋转度数简单的相加。

**3D旋转:** 三维旋转没有一个一般的公式。实际上，所有不改变位置、不镜面反射的刚性变换，就是全部的旋转变换。换句话说，三维旋转的矩阵集合就是所有行列式为1的单位正交矩阵集合。

三维旋转可以说是最复杂、最绕脑子的变换。一个很大的原因就是它是不满足交换律的。放缩和平移变换都能够用三个坐标表示，因为在三个方向上的变换可以随意交换顺序。三维旋转不可以。

当使用3D建模软件或者3D游戏开发工具的时候，一个对象的控制面板上都会有“变换”这一模块，一般是三行三列数字，通常来说，第一行是平移，第二行是旋转，第三行是放缩。平移和放缩都很容易理解，只有旋转的三个数字代表什么效果，不是那么直观。实际上，只有其他数字是零的时候，改变其中一个值的效果才比较容易理解：**某个方向上的旋转，就是绕这个方向的坐标轴旋转，迎着坐标轴的方向看去是逆时针旋转**。所以三个基本的3D旋转的矩阵如下所示：

 $$\begin{bmatrix}1& 0 & 0 \\ 0 & \cos\theta & -\sin\theta \\ 0 & \sin\theta & \cos\theta\end{bmatrix} \begin{bmatrix} \cos\theta & 0 & -\sin\theta \\ 0 &1&  0 \\ \sin\theta &0 &  \cos\theta\end{bmatrix} \begin{bmatrix}\cos\theta & -\sin\theta & 0 \\ \sin\theta & \cos\theta & 0 \\ 0 & 0 & 1\end{bmatrix}$$

任何一个旋转都可以看做绕某个轴的旋转。所以，一个3D旋转可以表示成轴对应的单位向量和旋转角度的组合。给定单位向量$a$和旋转角度$\theta$，对应的矩阵可以由如下公式算出: $R(a,\theta)=I \cos\theta+aa^T(1-\cos\theta)+A^\*\sin\theta$. 这个公式名为Rodrigues旋转公式。

这个公式的推导也不是很难。设有任意一个向量$b$，要想办法推出$b$绕$a$旋转$\theta$之后得到的向量是什么，在结果中把$b$提取出来，左边的系数就是要求的矩阵。$b$可以拆开为沿$a$方向的分量和与$a$垂直的分量。沿$a$方向的分量在旋转中保持不变，也就是$a(a^Tb)$，贡献了上式中的$aa^T$。$b$的与$a$垂直方向的分量表示成$1-aa^Tb$，这个分量旋转了$\theta$度，构成了上式中其他的部分。

#### 同态坐标

到目前为止，我们仍然没有讲相对简单的平移变换。那是因为平移变换在三维空间中是一个尴尬的存在：它不对应于一个线性变换，无法用三维矩阵表示。平移变换只能用加一个向量的方式表示，但这显然不是漂亮的解决办法。

为了解决这个问题，三维空间的坐标并不用三维向量表示，而是扩充了一个维度，变成了四维空间。三维空间中无法用线性变换表示平移，但三维空间中的平移可以用四维空间的剪切变换来表示。想象一下二维空间中的剪切变换，如果沿着剪切的方向取一个一维的横截面，那么在这个一维空间中，剪切变换就投影成了平移变换。四维空间和三维空间之间的关系也是如此。

一个三维空间中的点$(x,y,z)$对应着四维空间中的一条线$(xt,yt,zt,t)$。反过来任意一个四维空间中的点$(x,y,z,w)$可以投影为三维空间中的一个点$(x/w,y/w,z/w,1)$，前提是$w\neq 0$。对于$w=0$的情况，可以将其看作无穷远点。

现在的3D处理框架，在做变换处理时，内部的点几乎全都用四维坐标表示。只有变换完毕，需要交付渲染了，才进行**去同态化**，转换为$w=1$的点，在这之前不做任何除法。因为所有变换都是线性的，把一个点在任意时刻乘以非零常数，都不会影响最终的变换结果。

之前介绍的所有变换对应的矩阵，都可以简单扩充为四维矩阵：左上角的三阶矩阵为原始的矩阵，右下角为1，其他都是0。只有接下来介绍的平移矩阵不是这种格式。

**平移:**
$$\begin{bmatrix}1 & 0 & 0 & t_x \\ 0 & 1 & 0 & t_y \\ 0 & 0 & 1 & t_z \\ 0 & 0 & 0 & 1\end{bmatrix}$$

**变换的组合:** 变换的组合对应于矩阵的乘法。和矩阵的乘法一样，这种组合是不可交换的。先平移后旋转，和先旋转后平移，结果可能天差地远。

一般来说，先放缩、再旋转、最后平移，是比较直观的做法。一般的3D建模软件都是这个套路。当看到一个对象的九个变换参数时，要想到它们是按照这个顺序作用到对象上的。

用到先平移再旋转的情况，一般是模型造好后，选择摄像头的角度时，会出现这种变换的组合。这种组合的效果就是，旋转变换会造成整个世界的旋转。

**法向量的变换矩阵:** 当变换一个曲面时，曲面的法向量有可能不能用同一个变换来映射。比如拉伸一个球，同样的拉伸变换作用到法向量上，得到的结果可能和曲面不再垂直。

为了得到变换$M$对应的法向量变换矩阵$Q$，考虑到法向量$n$和所有切向量$t$垂直这一事实，也就是$n^Tt=0$。变换之后得到$n^TQ^TMt=0$。要让这个式子对所有$n$和$t$成立，必然有$Q^TM=I$，所以 $Q=M^{-T}$。

注意到这个结果符合一个直观：当变换为刚性变换时（也就是只有旋转），法向量也遵从同样的变换。此时$M$为单位正交矩阵，而这种矩阵的一个特点就是和自己的逆转置相等。

上述关于法向量变换矩阵讨论只针对三维空间。平移变换显然不会影响法向量。

**实现OpenGL函数gluLookAt:** 有了以上这些知识，就可以实现OpenGL的”对准“函数了。这个函数的作用就是让摄像头看着目标点。这个函数有三个向量作为参数：$eye$，$center$，$up$。$eye$和$center$比较好理解。即使固定了眼睛的位置和要看的物体，你的头还是能左右摆的，所以要指定$up$方向，表示头顶朝哪里。

给定这三个向量后，首先可以计算出一个坐标系来，即摄像头定义的坐标系。最终呈现在画面上的，是每个对象在摄像头的坐标系中的位置。所以，模型建立好之后，最终交付绘制之前，要把每个对象在**世界**中的位置，换算成这个对象在**摄像头坐标系**中的位置。这个变换该如何来做呢？

事实上，这个变换等价于这么一个变换：把摄像头坐标系变换到标准正交坐标系。

另外要注意到的一点是，在OpenGL中，摄像头是往$-z$方向看的，头顶是$y$方向，右手边是$x$方向。

也就是说，如果不调用gluLookAt这个函数，OpenGL渲染的结果就是站在原点往下俯视看到的图景，头顶$y$方向，右手边是$x$方向。而gluLookAt这个函数，是要做这么一个变换：把摄像头坐标系平移到原点，旋转后对准标准坐标系。这样，原来的整个世界，在OpenGL渲染之后，就成了摄像头坐标系中看到的场景。

首先计算摄像头坐标系。最容易确定的方向是$z$方向。因为目光的方向是$-z$方向，所以$z$方向是$eye-center$归一化。$up$方向并不一定和$z$垂直，$y$方向取它的垂直的分量。不过，更容易的计算方式是先算$x$：只要计算$up$和$z$的外积然后归一化就可以了，最后$y$为$z$和$x$的外积。

把摄像头坐标系变到标准坐标系的变换，其实就是标准坐标系到摄像头坐标系的逆变换。标准坐标系到摄像头坐标系的变换为：首先旋转，旋转变换的矩阵就是以摄像头坐标系的三个基向量作为列向量的矩阵，然后平移$eye-center$。所以，它的逆变换就是先平移$center-eye$，再逆旋转。因为旋转矩阵是单位正交阵，只需转置即可得到逆矩阵。

#### 正交投影

以上变换做完之后，3D目标对象就做好了准备，可以呈现在画面上了。但电脑屏幕是二维平面，把三维对象画到二维平面上，势必要做一个压扁的处理。这个处理就是投影。

投影需要指定六个参数**left, right, top, bottom, near, far**。

正交投影比较简单。上述六个参数确定了六个平面的位置，框起一个长方体，长方体外的部分被忽略。正交投影首先应用一个变换，这个变换能够将这个长方体放缩为一个大小为2，中心在原点的正方体。对整个世界应用这个变换。最终得到的画面，就是世界在这个正方体near面上的投影。

#### 透视投影

对于透视投影来说，指定的参数为**foy**，即$y$方向的视野角度；**aspect**即宽高比，以及**near, far**。这些参数定义了一个被砍掉头的金字塔。金字塔的塔尖位于摄像头，（如果做过gluLookAt变换的话，就是原点）。near和far参数分别指定了砍掉的截面和金字塔底。

OpenGL中，near和far都是正的，far大于near。但OpenGL的摄像头是往$-z$方向看的，所以实际上这两个平面的位置分别是$-near$和$-far$。

我们希望在这个范围内的点刚好投影到$[-1,1]\times[-1,1]$这个方块内。首先关注$y$方向。设$\theta=foy/2$，$d=\cot\theta$，我们希望一个点$(x,y,z)$向原点方向投影，在$z=-d$平面上的坐标变为$(-xd/z,-yd/z,d)$。

为了实现这个变换，考虑如下矩阵

$$\begin{bmatrix}1&0&0&0\\0&1&0&0\\0&0&1&0\\0&0&-1/d&0\end{bmatrix}$$

这样，对于点同态坐标$(x,y,z,1)$，变换之后就成了$(x,y,z,-z/d)$，也就是$(-xd/z,-yd/z,d)$。所以，只要$\|y/z\|$不超过$1/d$，也就是在$y$方向上处于这个金字塔之内，$y$坐标就会映射到$[-1,1]$之内。

接下来调节$x$方向。既然$y$方向已经在范围里了，只需要在$x$方向上缩小**aspect**倍，那么$x$也就刚好落到$[-1,1]$上。于是矩阵变为

$$\begin{bmatrix}1/aspect&0&0&0\\0&1&0&0\\0&0&1&0\\0&0&-1/d&0\end{bmatrix}$$

因为矩阵是同态的，全部乘以一个系数不会改变矩阵的作用效果，所以可以全部乘以$d$，得到

$$\begin{bmatrix}d/aspect&0&0&0\\0&d&0&0\\0&0&d&0\\0&0&-1&0\end{bmatrix}$$

far最后，我们用待定系数法调整这个矩阵，使得**near**和**far**平面分别落在$z=-1$和$z=1$上。因为$x$和$y$坐标不能影响$z$的映射，所以只能考虑右边两列。因为只考虑$z$坐标的映射结果，所以实际上只能调节第三行右边两个系数。设其分别为$A$和$B$。于是我们需要有$A\times (-near)+B=-(-1\times (-near))$，$A\times (-far) + B = -1\times (-far)$。最后解出来矩阵为

$$\begin{bmatrix}d/aspect&0&0&0\\0&d&0&0\\0&0&-(f+n)/(f-n)&-2fn/(f-n)\\0&0&-1&0\end{bmatrix}$$

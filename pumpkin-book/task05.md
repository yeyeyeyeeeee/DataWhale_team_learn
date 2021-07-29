# task05

本次参加datawhale组队学习27期的吃瓜教程

参考书籍是西瓜书和南瓜书

参考链接：https://github.com/datawhalechina/pumpkin-book

---

## 第六章 支持向量机

1.支持向量（support vector）:就是距离超平面最近的几个训练样本点使得满足某方程式的成立。个人理解就是超平面一侧的点（距离最近）与超平面另外一侧的点（距离最近）其绝对值是相等的。而这个方程式有两个关键量：w=（w1,w2..）法向量、b位移项。
2.二次规划（Quadratic Programming，简称QP）是一类典型的优化问题，包括凸二次优化和非凸二次优化。在此类问题中，目标函数是变量的二次函数，而约束条件是变量的线性不等式。
 　1）.常用二次规划解法有：椭圆法（ellipsoid method）、内点法（interior point）、增广拉格朗日法（augmented Lagrangian）、梯度投影法（gradient projection）等。
3.SMO（Sequential Minimal Optimization）:就是为了解决对偶问题（dual problem）在实际任务中会造成很大开销而引入的。
4.如果原始空间是有限维，即属性数有限，那么一定存在一个高维特征空间使样本可分。
5.核函数（kernel function）
 　1）.只要一个对称函数所对应的核矩阵半正定，他就能作为核函数使用。
 　2）.任何一个核函数都隐式地定义了一个称为“再生核希尔伯特空间”（Reproducing Kernel Hilbert Space，简称RKHS）的特征空间。
 　3）.因为我们希望样本在特征空间内线性可分，因此特征空间的好坏对支持向量机的性能至关重要。
 　4）.因为核函数隐式地定义特征空间，所以“核函数选择”是支持向量机的最大变数。
6.软间隔（soft margin）：在现实实际问题中，很难找到合适的核函数使得训练样本在特征空间中线性可分，或者说，即使找到合适的核函数使得训练集在特征空间中线性可分，也很难断定这个貌似线性可分的结果不是由于过拟合所造成的，缓解该问题的一个办法就是允许支持向量机在一些样本上出错，所以引入软间隔。
7.支持向量机的求解通常是借助于凸优化技术。

8.支持向量机：

　　1）.优点：泛化错误率低，计算开销不大，结果易解释。

　　2）.缺点：对参数调节和核函数的选择敏感，原始分类器不加修改仅适用于处理二类问题

　　3）.适用数据类型：数值型和标准型数据

9.支持向量机是一种分类器。因为它可以产生一个二值决策结果，所以称为“机“，即它是一种决策“机”。

10.支持向量机是一种监督学习算法。

11.SVM是针对二分类问题的学习方法

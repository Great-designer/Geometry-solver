# Geometry-solver
小型Matlab的实现方式

作为面向对象（Java）大作业感觉不错，建议未来的助教们可以考虑考虑。

# Version I 初始

某人对数学有敏锐的直觉，尤其是在几何问题上。

几何问题是这样的：求∠CAM的值。

或者是这样的：如果AC=x−3，BE=20，AB=16，CD=x+5，求x。

为了更容易地分析问题，输入将包含逻辑形式，而不是原始的问题文本和图表。

给出若干个符合一定规则的几何题条件。

要求出某个角或者某条边或未知数x的值。

## 基本逻辑形式

  * 数字。使用十进制整数表示数字。
  * 未知数字。x是唯一未知的数字。
  * 表达式（Expression）。表达式可以是一个数字，也可以是一个表达式，其中x只出现一次，最多一次加减法，最多一次乘法。乘法符号可以省略。
    * 例如：233，3x+5，x\*2+3，x-2是有效表达式，但3x+5-3，x+2x，5\*3，2y不是。
  * 点。使用单大写字母表示点。
  * 线。用Line(Point, Point)来表示一条线（实际上它是一条线段）。
    * 例如：Line(A, B)。
  * 角。使用Angle(Point, Point, Point)来表示一个角。
    * 例如：Angle(A,B,C)。
  * 线段长。使用LengthOf(Line)来获得特定线段的长度值。
    * 例如：LengthOf(Line(A, B))。
  * 角度。用MeasureOf(Angle)得到特定角度的度数值。
    * 例如：MeasureOf(Angle(A,B,C))。
  * 项（Term）。项=线段长|角度|表达式。
  * 相等。使用Equals(Term,Term)来声明这两个项的值相等。
    * 例如：Equals(LengthOf(A,B), 2)、Equals(MeasureOf(Angle(A, B, C))。
  * 垂直。使用Perpendicular(Line, Line)表示两条垂直线。
    * 例如：Perpendicular(Line (A, C), Line(B, D))。
  * 平行。使用Parallel(Line,Line)表示两条平行线。保证各点都是有序的。
    * 例如：Parallel(Line (A, C), Line(B, D))。
  * 点在线上。使用PointLiesOnLine(Point, Line)来表示位于线段上的点。
    * 例如：PointLiesOnLine(A, Line(B, C))。
  * 问题。使用Find(Term)来询问给定项的确切值。
    * 例如：Find(x)、Find(LengthOf(Line(A,B)))。

请注意，图和文本中的所有条件都将转换为逻辑形式。你现在得到了一个只有一个问题（Find phrase）的逻辑表单列表，并希望找到解决方案。

## 定理

  * 平角定理：如果点C位于AB上，则∠ACB=180∘。
  * 三角形内角和定理：任何三角形的三个内角加起来等于180∘。
  * 平行线定理：如果两条直线平行，则同位角（corresponding）相等，内错角（alternate）相等，同旁内角（interior）互补（supplementary）。
    * 对于平行线，同位角可能不存在，所以可以只用内错角和同旁内角建立关系。

## 建议

* 逻辑形式可以嵌套。
* 保证每个案例至少有一个好的解决方案。一个好的解决方法是：你可以用上面的定理一步一步地解决问题；每一步之后，你获得的新值都是有效表达式（这也意味着表达式中涉及的数字总是整数）；步骤数不超过4。
* 给定的逻辑形式是充分的。
    * 例如：如果一个线段上有四个点A，B，C，D，那么将列出四个相应的PointLiesOnLine短语（A-B-C，A-B-D，A-C-D，B-C-D）。
    * 例如：如果给你一个短语Perpendicular(Line (A, C), Line(B, D))，这两条线的交集也会给出。
      * 即：PointLiesOnLine(E, Line(A, C))和PointLiesOnLine(E, Line(B, D))，如果交集是E。
* 如果AO、BO、CO是三个不同的线段，则很难检测哪个线段位于中间。在这个问题中，您不需要在∠AOB、∠AOC、∠BOC之间建立关系。
    * 除非在这种明显的情况下：如果B位于AC上，则可以使用∠AOC=∠AOB+∠BOC。
* 所有的数据都来自现实世界的问题，而不是人工构建的。

## 核心思路

题目保证了每一步的结果依然是 expressions。

我们不断用已知的条件和定理扩展我们知道的量，直到找到答案。中途可能需要构方程和解方程。

整个过程有点像迭代加深搜索。

## 注意点

同一个角的表示有很多种，注意要对它们建立Equal关系。

对于所有 PointLiesOnLine，要构建边长相加的 Equal 关系和角度相加的 Equal 关系。

可以用 Find 导向去优化搜索方向。不过这道题是不必要的，你把所有可以求的量求出来也是 OK 的。

在解出 x 后，要把之前所有用 x 表示的表达式都带入一遍。

复杂度为 所有状态量 * 定理数量 * 步数上限（4）。

## 输入输出描述

输入包含多个样例。输入的第一行包含一个整数T，即样例数。

对于每个样例，第一行包含一个整数N（3≤N≤12），表示逻辑形式的数量。第二行包含几个以空格分隔的大写字母，表示与此问题相关的所有点。第三行包含几个长度为2的大写字母字符串，表示与此问题相关的所有直线（线段）。在接下来的N行中，有一个字符串si（|si|≤50），表示第i个逻辑形式。

保证最后一个逻辑形式以“Find”开头。

对于每种情况，输出一个表示答案的整数。不应该牵涉到未知的数字。

## 备用题目及答案

题目I：三角形内角和

```

12
A B C D F
CB CD CA CF BD BA BF DA DF AF
Equals(MeasureOf(Angle(F, A, D)), 20)
Equals(LengthOf(Line(D, A)), 9)
Equals(MeasureOf(Angle(F, A, B)), 32)
Equals(LengthOf(Line(B, A)), 6)
Equals(MeasureOf(Angle(A, D, B)), 40)
PointLiesOnLine(F, Line(C, A))
PointLiesOnLine(F, Line(B, D))
Parallel(Line(A, D), Line(B, C))
Equals(LengthOf(Line(A, D)), LengthOf(Line(B, C)))
Parallel(Line(A, B), Line(D, C))
Equals(LengthOf(Line(A, B)), LengthOf(Line(D, C)))
Find(MeasureOf(Angle(D, B, A)))

```

答案：88

题目II：一次方程

```

5
A B C
AB BC AC
Equals(LengthOf(Line(A, B)), 2x-7)
Equals(LengthOf(Line(B, C)), 4x-21)
Equals(LengthOf(Line(A, C)), x-3)
Equals(LengthOf(Line(A, B)), LengthOf(Line(B, C)))
Find(LengthOf(Line(A, C)))

```

答案：4

题目III：平行线同旁内角

```

12
A B C D F
CB CD CA CF BD BA BF DA DF AF
Equals(MeasureOf(Angle(F, A, D)), 20)
Equals(LengthOf(Line(D, A)), 9)
Equals(MeasureOf(Angle(F, A, B)), 32)
Equals(LengthOf(Line(B, A)), 6)
Equals(MeasureOf(Angle(D, B, C)), 40)
PointLiesOnLine(F, Line(C, A))
PointLiesOnLine(F, Line(B, D))
Parallel(Line(A, D), Line(B, C))
Equals(LengthOf(Line(A, D)), LengthOf(Line(B, C)))
Parallel(Line(A, B), Line(D, C))
Equals(LengthOf(Line(A, B)), LengthOf(Line(D, C)))
Find(MeasureOf(Angle(A, D, C)))

```

答案：128

题目IV：三角形内角和

```

3
A B C
AB AC BC
Equals(MeasureOf(Angle(A, B, C)), 40)
Equals(MeasureOf(Angle(C, A, B)), 25)
Find(MeasureOf(Angle(B, C, A)))

```

答案：115

题目V：平行线同旁内角

```

6
D A B K G
KG GD DA KA AB KB
Equals(MeasureOf(Angle(B, A, D)), 3x-70)
Equals(MeasureOf(Angle(K, G, D)), 120)
Equals(MeasureOf(Angle(G, D, A)), x)
Parallel(Line(K, G), Line(A, D))
PointLiesOnLine(A, Line(K, B))
Find(x)

```

答案：60

# Version II 勾股及相似

## 新定理

  * 勾股定理：在直角三角形中，如果三条边的长度分别是a，b，c（其中c是斜边），那么a^2+b^2=c^2。
    * 这个定理的逆定理也成立，但是暂时用不到。
  * 相似三角形定理：两个三角形在满足以下条件时是相似的。
    * SSS：三条对应边成比例的两个三角形相似。
    * SAS：两条对应边成比例，且夹角相等，则两个三角形相似。
    * AA：两个对应角相等的两个三角形相似。
    * HL：对应斜边和对应直角边成比例的两个直角三角形相似。
    * 在相似三角形中，对应角相等，对应边成比例。
  * 既然有了相似三角形，那么下面的全等三角形事实上已经暗含于其中了。
  * 全等三角形定理：两个三角形满足下列条件时为全等。
    * SSS：如果三条边对应相等，则两个三角形全等。
    * SAS：如果两边及其夹角对应相等，则两个三角形全等。
    * AAS和ASA：如果有两个对应角相等（相似），又有一组对应边相等，则两个三角形全等。
    * HL：对应斜边和对应直角边相等的两个直角三角形全等。
    * 在全等三角形中，对应边和对应角相等。
  * 将相似（全等）的关系用于自身，可以得到下面等腰的关系。
    * 等腰三角形定理：在三角形ABC中，如果AB=AC，则∠ACB=∠ABC，反之亦然。

## 新注意点
    
可以先不断地用“基本定理”传播 expression，到最后再用勾股定理、相似定理等复杂的定理解方程。

要能检测出图中的三角形（只要三点不共线就算），并积极寻找全等和相似的三角形对。

在运用一些定理时，所用的条件可能不是“完全”的。比如我们知道两条边的长度都是 2x+3，我们依然可以利用它们相等来构建 对角相等 或者 三角形全等 的关系。

## 新备用题目及答案

题目I：基本平行线相似

```

8
A B C D E
AC AD AB AE BC BE CD DE
Equals(LengthOf(Line(A, C)), x-3)
Equals(LengthOf(Line(A, B)), 16)
Equals(LengthOf(Line(C, D)), x+5)
Equals(LengthOf(Line(B, E)), 20)
PointLiesOnLine(C, Line(A, D))
PointLiesOnLine(B, Line(A, E))
Parallel(Line(B, C), Line(E, D))
Find(x)

```

答案：35

题目II：勾股定理

```

5
A B C
AB AC BC
Equals(LengthOf(Line(A, C)), 3)
Equals(LengthOf(Line(A, B)), 5)
Equals(LengthOf(Line(B, C)), x)
Perpendicular(Line(A, C), Line(B, C))
Find(x)

```

答案：4

题目III：基本平行线相似

```

7
A C B D E
AB AC AE AD BE BC DE CD
Equals(LengthOf(Line(A, C)), 16)
Equals(LengthOf(Line(E, D)), 5)
Equals(LengthOf(Line(A, B)), 12)
PointLiesOnLine(B, Line(A, C))
Parallel(Line(C, D), Line(B, E))
PointLiesOnLine(E, Line(A, D))
Find(LengthOf(Line(A, E)))

```

答案：15

# Version III 圆

## 新基本逻辑形式

  * 圆。使用Circle(Point)表示具有特定中心的圆。
    * 例如：Circle(O)。
  * 点在圆上。使用PointLiesOnCircle(Point, Circle)表示位于圆上的点。
    * 例如：PointLiesOnCircle(A, Circle(O))。

## 新定理

  * 直径相等定理：同一个圆的不同直径相等。圆的中心也是每个直径的中点。
  * 圆上点定理：如果AB是圆O的直径，另一个点C位于圆O上，则∠ACB=90∘。

## 新备用题目及答案

题目I：直角三角形全等

```

11
N M B C T A D
MA MB MT MD AB AC BC TC TN CN
Equals(MeasureOf(Angle(M, T, N)), 28)
PointLiesOnLine(B, Line(A, C))
PointLiesOnLine(D, Line(T, N))
PointLiesOnCircle(N, Circle(M))
PointLiesOnCircle(C, Circle(M))
PointLiesOnCircle(T, Circle(M))
PointLiesOnCircle(A, Circle(M))
Perpendicular(Line(A, B), Line(M, B))
Perpendicular(Line(D, M), Line(T, D))
Equals(LengthOf(Line(B, M)), LengthOf(Line(D, M)))
Find(MeasureOf(Angle(C, A, M)))

```

答案：28

题目II：对顶角

```

12
A B C D E F G
GC GD GB GF GA GE CE BF BA FA
Equals(MeasureOf(Angle(A, G, C)), 60)
PointLiesOnLine(F, Line(G, A))
PointLiesOnLine(G, Line(C, E))
PointLiesOnLine(G, Line(B, F))
PointLiesOnLine(G, Line(B, A))
PointLiesOnLine(F, Line(B, A))
PointLiesOnCircle(C, Circle(G))
PointLiesOnCircle(B, Circle(G))
PointLiesOnCircle(A, Circle(G))
PointLiesOnCircle(E, Circle(G))
Perpendicular(Line(G, F), Line(G, D))
Find(MeasureOf(Angle(B, G, E)))

```

答案：60

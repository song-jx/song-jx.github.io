---
title: 部分题解合集
date: 2021-09-08 15:09:11
tags:
top: 1
---
懒得分开写咕咕咕。

### TC13459

“1”的限制分两种，在同一行或在同一列，但 “1” 的数量很多，不能枚举每个“1”是哪一种，设“在同一行”的边为白边，“在同一列的”的边为黑白，考虑边之间的约束关系。

考虑两条边 $(i,j),(i,k)$，当边 $(j,k)$ 存在时说明 $(i,j)$ 和 $(i,k)$ 的颜色相同，反之亦然。

这样就可以表示出所有的约束，必要性显然，充分性是因为合法解中同色边一定构成了若干不含公共点的团，这种对于相邻两条边的约束关系就很充分了。

在所有的约束条件下，所有的边及其约束关系构成类似二分图的结构，联通块分两类，一类是整个连通块一定同色，另一类是一定包含两种颜色。

设第一类连通块有 $x$ 个，第二类连通块有 $y$ 个，枚举第一类连通块有 $i$ 个白色，即可得到答案：
$$
\sum_{i=0}^x\binom xi2^yn^{\underline{i+y}}n^{\underline{x-i+y}}
$$
复杂度 $O(n^3)$（DFS 求连通块）或 $O(n^3\alpha(n^2))$（并查集求连通块）。

### TC12909

任意时刻局面的样子都是若干个连续段，我们只关心每个连续段的样子和它们在环上的相对顺序，而不关心空白的位置，因为只有知道前者的方案数，当前局面的方案数是可以算的。

设 $f_{i,j}$ 表示当前已经来了 $i$ 个朋友，共构成 $j$ 个连续段的方案数，转移分三种：

- 第 $i+1$ 个朋友新开一个连续段，$f_{i+1,j+1} \leftarrow j \cdot f_{i,j}$。
- 第 $i+1$ 个朋友加入一个连续段的开头或结尾，$f_{i+1,j} \leftarrow 2j \cdot f_{i,j}$。
- 第 $i+1$ 个朋友将两个连续段接在了一起，$f_{i+1,j-1} \leftarrow j \cdot f_{i,j}$。

只需要保证 $j \le G$ 就行了，而不需要考虑连续段过多而导致前两种转移不合法，因为不合法了贡献系数一定为 $0$。

最后是贡献系数，假设 $K$ 个人到齐后有 $x$ 个连续段，则贡献系数为 $N\binom{N-K-1}{K-1}$。

复杂度 $O(N^2)$。

### TC13692

搬家具的排列很像拓扑序，但又有点区别。

枚举 $S_1-S_2$ 路径上第一个选的点 $u$，再对每条边定向定向，然后就转化成了一张图的拓扑序，记这个东西为   $f_u$。当 $u=S_1$ 或 $S_2$ 时这张图就是树，否则这张图和树唯一的区别是点 $u$ 有两个父亲，但这张图的拓扑序并不好算，单次复杂度只能做到 $O(n^2)$，无法通过。

注意到这题只需要求出 $S_1-S_2$ 路径上每个点 $f$ 的总和。

枚举 $S_1-S_2$ 路径上的一条边 $(u,v)$，然后断开 $(u,v)$，再对每条边定向定向，然后就转化成了两棵树的拓扑序，这个是可以 $O(n)$ 算的，发现算出来的正好是 $f_u+f_v$。

最后把前一步骤的计算结果加起来，再加上 $f_{S_1}+f_{S_2}$  并除以二，即是答案。

### AGC017F

容易想到用位向量来表示折线，$0$ 表示这一步向左走，$1$ 表示向右，容易得到折线的形态只有 $2^{N-1}$ 种。

由于相邻两条折线 $S,T$ 的约束关系是 $T$ 的每个前缀后都大于等于 $S$ 的对应前缀和。

不难想到轮廓线 `DP`，设 $f_{i,j,k,S}$ 表示满足以下条件的方案数：

- 第 $i$ 条折线已经填了前 $j$ 位。
- $S$ 的前 $j$ 位是第 $i$ 条折线的，后 $N-1-j$ 位是第 $i-1$ 条折线的。
- 第 $i-1$ 条折线前 $j$ 位之和为 $k$。

转移就枚举第 $i$ 条折线第 $j+1$ 为填什么。

复杂度 $O(n^32^n)$，无法通过。

再次考虑相邻两条折线 $S,T$ 的约束关系，发现从 $S$ 到 $T$ 是以下过程：

- 把每个 $1$ 都往前移或不动，并且不改变相对顺序。

- 最后一个 $1$ 之后的 $0$ 任意变成 $1$。

重新定义 `DP` 状态 $f_{i,j,S}$ 表示正在确定了第 $i$ 条折线，当前为 $S$，已经固定了前 $j$ 个 $1$ 的方案数。

转移为：

- 如果存在第 $j+1$ 个 $1$，就枚举它往前移多少位，不能跨过前一个 $1$，这个枚举量平均是 $O(1)$ 的。
- 如果不存在，要么确定第 $i$ 条折线，要么枚举最后一个 $1$ 之后的一个 $0$ 把它变成 $1$，这个枚举量平均也是 $O(1)$ 的。

当确定第 $i$ 条折线后把不合法的状态置成 $0$。

复杂度 $O(n^22^n)$。

### ARC078D

考虑从 $1-n$ 只有一条点不重复的路径的充要条件：

- 把这条唯一路径上的边都断开后路径上的点两两不连通。

假设知道这条唯一路径是 $u_1,u_2,\cdots,u_k$（$u_1=1,u_k=n$），要将点集划分成 $k$ 份，第 $i$ 份包含 $u_k$，最大化每个点集内部的边权之和。

可以得到一个状压做法：

- 设 $g_S$ 表示两个端点都在点集 $S$ 内部的所有边的权值之和。
- 设 $f_S$ 表示点集 $S$ 已经被考虑时，最大的保留边权之和。
- 转移为：$f_S \leftarrow f_{S-T} + g_T({T \subseteq S})$，其中 $T$ 恰好包含一个关键点。

由于并不知道这条路径，所以需要该一下 `DP` 状态：

设 $f_{i,S}$ 表示点集 $S$ 已经被考虑且 $1-i$ 只有一条路径时，最大的保留边权之和。

转移为:

- $f_{i,S \cup \{i\}} \leftarrow f_{j,S} + w(j,i)(j \not\in S)$。
- $f_{i,S\cup T} \leftarrow f_{i,S}+g_{T\cup\{i\}}(S \cap T = \varnothing)$。

复杂度 $O(n3^n)$。

ARC068D

AGC004F

ARC097D

TC10265

TC9844

ARC067C

ARC097C

### TC10727

根据“跳跳棋”的结论，所有的三元组构成二叉森林的形态，于是问题就转化成了：

- 在一棵无限满二叉树上，从点 $u$ 走到点 $v$ 长度恰好为 $k$ 的路径条数。

直接算感觉很困难，考虑 `DP`，容易想到记录当前步数和所在点 $x$，但 $x$ 显然是记不了的。考虑用关键信息来替代 $x$，记录 $\text{dis}(x,\text{lca}(x,v)),\text{dis}(v,\text{lca}(x,v))$ 就够了。

状态 $f_{i,j,k}$ 为走了 $i$ 步，$\text{dis}(x,\text{lca}(x,v))=j,\text{dis}(v,\text{lca}(x,v))=k$ 的路径条数。

转移为：

- $f_{i+1,j+1,k} \leftarrow 2f_{i,j,k},f_{i+1,j-1,k} \leftarrow f_{i,j,k}(j>0)$
- $f_{i+1,1,k} \leftarrow f_{i,0,k},f_{i+1,\max(1-k,0),\max(k-1,0)} \leftarrow f_{i,0,k}(k>0)$
- $f_{i+1,0,k+1} \leftarrow f_{i,0,k}(k < \text{depth}_v)$

TC10664

TC10566

TC10773

### TC10993

容易发现把所有的环缩成点之后这张图就变成的一棵树，把 $0$ 结点所在的环看出根。树边和环边分开考虑。

对于树边，显然所有人都只会向上走，最坏情况就是 $C$ 个人全在这条树边的下面，故每条树边需要 $C$ 个急救仓。

对于一个环，子树中的人都是先向上走到这个环上，再聚集到向上的树边的下端点，最后一起离开这个环。

可以看出环之间是独立的，考虑一个环怎么做。对于一个环来说，最坏的情况肯定是 $C$ 个人聚集在一个点上，然后这 $C$ 个人再分成两批，一批从左边绕到终点，另一批从右边绕到终点，要求 $\min 左边 + \min 右边  \ge C$。

方法是 `DP`，设 $f_{i,j}$ 表示已经确定了从终点开始向左的 $i$ 条边，它们的 $\min$ 为 $j$ 时的最小代价。

转移时枚举第 $i+1$ 条边的急救仓数 $k$：$f_{i+1,\min(j,k)} \leftarrow f_{i,j}(j+k \ge C)$。因为 $j$ 只会变小，所以对于第 $i+1$  条之后的边，它们的限制会更严，所以转移只需要使第 $i+1$ 条边满足限制。

复杂度 $O(nC^2)$，无法通过。

考虑将这个转移拆开：

- $f_{i+1,j} \leftarrow f_{i,j}(k \ge \max(j,C-j))$，此时肯定要最小化 $k$。
- $f_{i+1,k} \leftarrow f_{i,j}(k \le j \le C -k)$，考虑 $k$ 从小到大时，可行的 $j$ 组成的区间在扩展，容易做到均摊 $O(1)$ 转移。

复杂度 $O(nC)$。

TC10741

TC10854

TC10848

TC10902

TC10737

TC10758

TC11003

TC11026

TC11032

TC10748

TC11213

TC11305

TC12620



TC10758

TC11032

### Gym102391E

先二分一个直径 $D$，建出圆方树，设 $f_u$ 表示：

- 已经确定了 $u$ 子树内的所有方点表示的环怎么断。
- 子树内直径不超过 $D$。
- $f_u$ 为子树内到 $u$（圆点）/ $fa_u$（方点）的最大距离。

转移分两种：

- $u$ 为圆点，判断一下儿子 $f$ 最大的两个之和是否小于等于 $D$，大于 $D$ 说明 $D$ 小了，停止 `DP`，否则继承儿子 $f$ 的最大值。

- $u$ 为方点，设 $fa$ 为 $u$ 的父亲，枚举一下断该环上的哪条边，然后算一下子树内直径，直径有两种可能情况。

  - 一个儿子 $v$ 到断边的较大环上距离 $+f_v$。
  - 两个儿子 $v_1,v_2$ 在不跨过断边时的环上距离 $+f_{v_1}+f_{v_2}$。

  如果直径大于 $D$，就说明这条边不能断，否则

  $f_u \leftarrow \max\limits_vf_v+$ $v$ 在不跨过断边时到 $fa$ 的环上距离。

  $f_u \leftarrow$ 断边到 $fa$ 的环上距离。

  直接转移复杂度为 $O(儿子数量^2)$，记录一些儿子前后缀信息就可以优化到线性，以前缀为例：

  - 第 $i$ 个儿子向左绕到 $fa$ 的距离。
  - 前 $i$ 个儿子子树内在不跨过 $fa$ 时到第 $i$ 个儿子的最大距离。
  - 前 $i$ 个儿子子树内和断边向左绕到 $fa$ 的最大距离。
  - 前 $i$ 个儿子子树内的直径。

复杂度 $O(n\log V)$。

### ZOJ3970

考虑在操作序列中有相邻的加减操作并且加操作在前面，应用如下调整：

- 如果两个操作区间无交，则交换操作顺序。
- 如果有交，那么相交的部分相当于什么都没做，直接去掉相交部分，变成上一种情况。

经过有限步调整，操作序列变成了若干减后若干加。

假设已知第 $i$ 个位置需要进行 $a_i$ 次减操作，那么最小操作次数为 $\sum_{i=2}^n\max(0,a_i-a_{i-1})$。

加操作同理。

设 $pre_i$ 表示上一个满足 $t_j>0$ 的位置 $j$。

$f_{i,j}$ 表示考虑了前 $i$ 个位置，其中第 $i$ 个位置被 $j$ 个减操作覆盖时的最小操作次数。

转移为：

$$
f_{i,j}=\min_kf_{pre_i,k}+\max(0,j-k)+\max(0,t_i-s_i+j-t_{pre_i}+s_{pre_i}-k)+\max(0,\max_{pre_i<x<i}s_x-\max(j,k))
$$

其中最后一项表示将区间 $(pre_i,i)$ 减成 $0$ 需要的额外减操作次数。

复杂度 $O(nV^2)$，无法通过。

打表发现函数 $f_i$ 分三段，每一段都是一次函数，并且斜率递增。

注意到转移方程后面的每一项都是分段一次函数，因此它们的和也是分段一次函数，所以 $\min$ 只会在拐点处取到，这样就可以 $O(1)$ 算出一个 $f_{i,j}$。

求函数 $f_i$ 的两个拐点？考虑分治，对于一个区间 $[l,r]$，如果 $f_{i,l},f_{i,mid},f_{i,r}$ 等差，说明 $[l,r]$ 一定在同一个段，否则递归左右两半。

复杂度 $O(n\log V)$。

### ZOJ3989

为了方便处理，先对所有点旋转一个角度，使所有点横坐标两两不同。

对于一个三角剖分，考虑一个维护折线过程：

- 初始为下凸包。
- 每次将折线上一条边换成它上方三角形的另外两条边，要保证这两条边不在折线上。
- 或者将折线上在同一三角形内的相邻两条边换成第三条边，要保证三角形在原来两条边的上方。
- 最终为上凸包。

这个过程会遍历三角剖分中的所有边，容易想到把折线作为 ```DP``` 状态，但折线数量太大了。

给折线加一条限制：折线上的拐点横坐标递增。但这样可能会导致折线找不到合法的转移。

事实上这种情况不存在：

- 对于二换一的转移，显然合法，所以考虑只能进行一换二的时候。
- 对于最左边的折线，假设它的横坐标区间为 $[l,r]$，进行一换二后新的拐点横坐标为 $x$，要么 $l < x < r$，这时可以直接转移，否则 $x > r$，即它上方的三角形向右偏。
- 对于最右边的折线，如果它不能直接转移，同理可以得到它上方的三角形向左偏。
- 最左边的折线上方的三角形向右偏，最右边的折线上方的三角形向左偏，故中间一定存在一条折线可以进行合法的一换二。

这样的折线就可以用拐点集合来表示了，状态数为 $2^{n-2}$，转移时不能跨过点。

这样就解决了最优化问题，但计数会算重。

定义一次转移的「横坐标」为它涉及到的两个或三个点中横坐标的最大值，所有当前能进行的转移的「横坐标」一定两两不同。

考虑将折线的转移序列标准化，使得转移序列与三角剖分一一对应：每次进行「横坐标」最大的合法转移。

另一个等价的定义是每次转移的「横坐标」单调不减。

设 $f_{S,i}$ 表示当前折线为 $S$，上次转移的「横坐标」为 $i$ 时的最小代价及其方案数。

复杂度 $O(n^22^n)$。

### 来源不明的题

> 给定一个二分图，左右各 $n$ 个点。对于左部点的一个集合 $S$，设 $f(S)$ 表示与 $S$ 中至少一个点相邻的右部点集合。判断是否存在一个集合 $S \ne \{1,2,\cdots,n\}$，使得 $|f(S)| \le |S|$，输出方案。
>
> $n, m \le 10^5$

Hall 定理：一个二分图存在完美匹配的充要条件是对于任意 $S$，$|f(S)| \ge |S|$。

如果不存在完美匹配，就存在 $S$ 使得 $|f(S)| \le |S|$，考虑求出一个这样的 $S$。

先求出一个最大匹配，找一个未匹配点为根建匈牙利树，树上所有的左部点就是 $S$。

如果存在完美匹配，解的形式一定是一个左部点集合 $S$，它们的匹配点集合为 $f(S)$。

所以如果选了右部点 $v$，就一定会选它的匹配点 $\text{match}(v)$。

把原来的每条边 $(u,v)$ 换成 $(u,\text{match}(v))$ 再求出拓扑序最小的强连通分量即可。

### ARC107F

由于一个连通块的贡献带有绝对值符号，不太好处理，变成枚举符号不会影响答案。

现在变成如下问题：

- 每个点有三种状态：正、负、删，代价分别为 $-B_i,A_i,B_i$。
- 对于相邻的点 $u,v$，如果它们的状态都不是删，就必须相同。
- 求最小代价。

想到最小割模型，由于每个点有三种状态，所以把每个点 $i$ 变成两个点 $U_i,V_i$。

用 $(S,U_i),(U_i,V_i),(V_i,T)$ 三条边表示三种状态。

令它们的代价分别为 $\infty-B_i,\infty+A_i,\infty+B_i$，那么这三条边一定恰好割掉一条。

对于相邻的点 $(i,j)$，有两个约束关系：

- 不能同时割 $(S,U_i),(V_j,T)$，如果要割 $(S,U_i)$，说明 $U_i$ 能到达 $T$，如果要割 $(V_j,T)$，说明 $S$ 能到达 $V_j$，所以连一条 $(V_j,U_i)$，代价为 $\infty^2$ 的边。
- 不能同时割 $(S,U_j),(V_i,T)$，同理连一条 $(V_i,U_j)$，代价为 $\infty^2$ 的边。

最后答案为最小割减去 $n\infty$。

![.png](https://i.loli.net/2021/09/08/4ragjEUYIev5wot.png)

### Gym101471J

CF1307G

### CF1307F

P3980

CF1368H2

### AGC038E

先考虑一个弱化版问题：$B_i=1$ 时怎么做。

这是一个经典问题，一般做法有两种：状压 `DP` 和 `min-max` 容斥。

它们的复杂度都是 $O(n2^n)$ 或 $O(2^n)$ 的，然而这题数据范围是 $400$，说明需要用的此题的特殊性质。

通过 `min-max` 容斥可以得出答案为
$$
\sum_{S}(-1)^{|S|}\frac{\sum_{i=1}^n A_i}{\sum_{i \in S} A_i}
$$
发现分母是小于 $400$ 的非负整数！可以用背包数出每种分母的贡献 $\sum_S(-1)^{|S|}$。

设
$$
f_{i,j} = \sum_{S \subseteq \{1,2,\cdots,i\}}(-1)^{|S|}[\sum_{i \in S}A_i=j]
$$
转移为 $f_{i,j}=f_{i-1,j}-f_{i-1,j-A_i}$，答案为
$$
(\sum_{i=1}^n A_i)\sum_{i=0}^{400}\frac{f_{n,i}}i
$$
现在回到原问题，还是考虑 `min-max` 容斥，答案就是
$$
\sum_{S}(-1)^{|S|}[S 中第一次有元素达到目标时的期望步数]
$$

设 $p_i=\frac{A_i}{\sum_{j \in S}A_j}$。

根据期望的线性性质，期望步数可以分摊到经过每个状态上。所以后面那坨东西为：
$$
\begin{aligned}
&\sum_{\forall i \in S,c_i<B_i}[到达c状态的概率]\cdot[离开c状态的期望步数]\\
&=\sum_{\forall i \in S,c_i<B_i}\frac{(\sum_{i \in S}c_i)!}{\prod_{i \in S} c_i!}\prod_{i \in S}p_i^{c_i} \cdot \frac{\sum_{i=1}^n A_i}{\sum_{i \in S} A_i}\\
&=\sum_{\forall i \in S,c_i<B_i}\frac{(\sum_{i \in S}c_i)!}{\prod_{i \in S} c_i!}\prod_{i \in S}A_i^{c_i} \cdot \frac{\sum_{i=1}^n A_i}{(\sum_{i \in S} A_i)^{(\sum_{i \in S}c_i)+1}}
\end{aligned}
$$

把前面说的东西拼起来，答案为：
$$
\begin{aligned}
&\sum_S(-1)^{|S|}\sum_{\forall i \in S,c_i<B_i}\frac{(\sum_{i \in S}c_i)!}{\prod_{i \in S} c_i!}\prod_{i \in S}A_i^{c_i} \cdot \frac{\sum_{i=1}^n A_i}{(\sum_{i \in S} A_i)^{(\sum_{i \in S}c_i)+1}}\\
&=(\sum_{i=1}^n A_i)\sum_S(-1)^{|S|}\sum_{\forall i \in S,c_i<B_i}\frac{(\sum_{i \in S}c_i)!}{(\sum_{i \in S} A_i)^{(\sum_{i \in S}c_i)+1}} \cdot \prod_{i \in S}\frac{A_i^{c_i}}{c_i!}
\end{aligned}
$$

式子中比较难转移的东西就是 $\sum_{i \in S} A_i$ 和 $\sum_{i \in S}c_i$，把它们记状态里就行了。

状态为
$$
f_{i,j,k}=\sum_{S \subseteq \{1,2,\cdots,i\}}(-1)^{|S|}\sum_{\forall i \in S,c_i<B_i}\prod_{i \in S}\frac{A_i^{c_i}}{c_i!}[\sum_{i \in S} A_i=j \land \sum_{i \in S}c_i=k]
$$
转移为
$$
f_{i,j,k}=f_{i-1,j,k}-\sum_{c=0}^{B_i-1}f_{i-1,j-A_i,k-c}\frac{A_i^c}{c!}
$$
答案为
$$
(\sum_{i=1}^n A_i)\sum_{i=0}^{400}\sum_{j=0}^{400}\frac{j!f_{n,i,j}}{i^{j+1}}
$$
分析一下时间复杂度，虽然每次转移的枚举量是 $B_i$，但由于 $\sum_{i=1}^nB_i$ 是 $O(n)$ 的，所以总复杂度是 $O(n^3)$，空间复杂度可以用滚动数组优化到 $O(n^2)$。

---

### AGC037D

考虑第三次操作前第 $i$ 行一定由 $(i-1)m+1$ 到 $im$ 构成，记 $(i-1)m+1$ 到 $im$ 的颜色为 $i$。

第二次操作的目标就是使颜色为 $i$ 的数在第 $i$ 行，所以第一次操作的目标就是使每一列 $n$ 种都颜色各有一个。

先考虑如何确定第一列的颜色，这显然是一个行与颜色的完美匹配问题。由于任意选 $i$ 行，这 $i$ 行的颜色数至少为 $i$，根据 Hall 定理，一定存在完美匹配。每一列依次求完美匹配就可以构造出一组解。

然后第二三次操作就非常简单了，复杂度 $O(n^4)$。

### AGC043D

考虑什么样的排列 $P$ 是能被造出来的。

考虑构造过程：每次选择一个头元素最小的序列 $A_i$，删除 $A_i$ 开头单调递减的一段，再继续找头元素最小的序列。

这启发我们把同时删除的元素看成一段，分段具有如下性质：

- 每一段是长度不超过 $3$ 的单调递减序列。
- 每一段的头元素递增。
- 长度为 $1$ 的段不少于长度为 $2$ 的段（因为每一个长度为 $2$ 的段必须要对应一个长度为 $1$ 的段来一起构成一个 $A_i$）。

同时，只要满足上面三个条件，这个 $P$ 就能被造出来的，将每个段配配对就可以得到一个生成 $P$ 的 $A$ 序列。

由于 $P$ 和分段内容是一一对应的，问题转化为对合法的分段内容计数。

枚举长度分别为 $1,2,3$ 的段数 $cnt_1,cnt_2,cnt_3$，满足 $cnt_1+2cnt_2+3cnt_3=3n$ 和 $cnt_1 \ge cnt_2$。

贡献即为
$$
\binom{cnt_1+cnt_2+cnt_3}{cnt_1,cnt_2,cnt_3}\frac{(3n)!}{(cnt_1+cnt_2+cnt_3)!2^{cnt_2}3^{cnt_3}}
$$
前面的组合数是划分出每一段的方案数，除以 $(cnt_1+cnt_2+cnt_3)!$ 是保证每一段的头元素递增，除以 $2^{cnt_2}3^{cnt_3}$ 是保证每一段的头元素为最大值。

复杂度 $O(n^2)$。

### AGC049D

考虑如何描述一个非负凸序列。

- 枚举最小值 $c$，以及取到最小值的第一个位置 $i$，令 $A=(c,c,\cdots,c)$。
- 多次选一个位置 $j<i$，将 $A_j,A_{j-1},A_{j-2},\cdots,A_1$ 分别加上 $1,2,3,\cdots,j$。
- 多次选一个位置 $j>i$，将 $A_j,A_{j+1},A_{j+2},\cdots,A_n$ 分别加上 $1,2,3,\cdots,n-j+1$，若 $i>1$ 则 $i-1$ 必须被选到一次。

第三步可以事先选 $i-1$ 一次，对总和产生 $\frac {i(i-1)}2$ 的贡献，然后第三步就和第二步一样了。

先枚举 $i$，第二三步本质上就是完全背包，由于体积的特性，有用的物品数量是 $O(\sqrt m)$ 的，可以 $O(m\sqrt m)$ 预处理出背包数组，然后 $O(\frac mn)$ 枚举 $c$，计算贡献。

这样做的复杂度为 $O(nm\sqrt m)$，无法通过。

考虑 $i \rightarrow i+1$ 时，物品最多删一个，也最多添一个，并且总改变次数是 $O(\sqrt m)$ 的，动态维护背包即可做到 $O(m\sqrt m)$ 的复杂度。

### AGC050D

设 $f_{i,a,b,j}$ 表示从以下局面出发，还没有赢的人中从左到右第 $j$ 个人最终赢的概率。

- 有 $a$ 个人还没有赢且已经排除了 $i$ 个错误选项。
- 有 $b$ 个人还没有赢且已经排除了 $i+1$ 个错误选项。

转移就枚举这 $a$ 个人中下一个人是赢还是输即可（这里 $f_{i,0,b,j}=f_{i+1,b,0,j}$）。

- $f_{i,a,b,j}=win \cdot f_{i,a-1,b,j-[j>b]} + lost \cdot f_{i,a-1,b+1,j}(j\ne b+1)$
- $f_{i,a,b,b+1}=win + lost \cdot f_{i,a-1,b+1,b+1}(j\le b)$

复杂度 $O(n^4)$。

### NOI2021Day1T1

如果把修改操作看成把路径上的点染成一种新的颜色，那么重边就是两端同色的边，轻边就是两端异色的边。

所以询问操作就是查询路径上两端同色的边数，这个可以用树链剖分和线段树维护。

复杂度 $O(n\log^2n)$。

### NOI2021Day1T2

当 $k=2$ 时，交点就是逆序对，自然联想到行列式，发现答案就是行列式。

当 $n_1=n_2=\cdots=n_k$ 时，答案就是把每相邻两层邻接矩阵的行列式乘起来。

对于原问题，答案就是相邻两层邻接矩阵乘积的行列式。

证明：

- 对于一个合法的路径组，考虑两条路径 $(P_1,P_2,\cdots,P_k)$ 和 $(Q_1,Q_2,\cdots,Q_k)$，两条路径有奇数个交点 $\iff$ $(P_1,Q_1)$ 和 $(P_k,Q_k)$ 逆序。

- 对于一个不合法的路径组，考虑对其进行以下变换：

  - 找到最靠上的一个点被覆盖多次，多个在同一层时取最靠左的一个。
  - 找到经过此点的编号最小两条路径，将它们的下一半交换。

  这样的变换是相互的，并且会使每条路径头尾形成的逆序对总数的奇偶性改变，故不合法的路径组的贡献会两两抵消。

复杂度 $O(n^4)$。

### NOI2021Day1T3

容易发现对于一次询问，答案为 $s$ 能到达且能到达 $t$ 的点数。

由于研究的是可达性，先进行强连通分量缩点。

再考虑限制：若 $x\Rightarrow z$ 且 $y\Rightarrow z$，则 $x\Rightarrow y$ 或 $y\Rightarrow x$。这说明能到达 $z$ 的点在一条链上，进一步，整张图是一棵树加上若干条从祖先到儿子的边。

如何求出这棵树？一个点的父亲就是所有连向它的点中拓扑序最大的一个，注意所完点后编号就是拓扑序的逆序。

对于加边操作，对 $s,t$ 以及所有边的端点建虚树，统计一下虚树上的点和边的贡献即可。

由于此题卡常，树剖求 `LCA` 效果最佳，用邻接链表存虚树，不能用 `vector`。

复杂度 $O(n+q\log n)$。

### NOI2021Day2T1

$k \le 15$ 是此题的突破点，这意味着把 $256$ 平均分成 $16$ 段后必然有一段是完全相同的。

枚举完全相同的是哪一段，确定了 $16$ 位后，期望只有 $7$ 个符合条件的串，对它们用 `popcount` 检验即可。

### NOI2021Day2T2

~~结论题。~~

假设已知 $a$ 序列，怎么算答案。

维护最后一项 $a_n$​ 的分子分母 $x,y$​，一次变换后 $\frac {x'}{y'}=a_{n-1}+\frac 1{a_n}=\frac {a_{k-1}x+y}x$​。发现不会发生约分，并且相当于对 $(x,y)$​​ 做了一个线性变换：
$$
\begin{bmatrix}
x'\\
y'
\end{bmatrix}
=
\begin{bmatrix}
a_{n-1}&1\\
1&0
\end{bmatrix}
\begin{bmatrix}
x\\
y
\end{bmatrix}
$$
算出
$$
\begin{bmatrix}
a&c\\
b&d
\end{bmatrix}
=
\prod_{i=1}^n
\begin{bmatrix}
a_i&1\\
1&0
\end{bmatrix}
$$
答案即为 $\frac ab$。

然后考虑两种操作：

- `W` 类型：因为 $\begin{bmatrix}x&1\\1&0\end{bmatrix}\begin{bmatrix}1&0\\1&1\end{bmatrix}=\begin{bmatrix}x+1&1\\1&0\end{bmatrix}$​，所以 'W' 操作就是在后面乘一个 $\begin{bmatrix}1&0\\1&1\end{bmatrix}$。

- `E` 类型：虽然定义中如果最后一项为 $1$ 时要特别处理，但发现当最后一项为 $1$ 时两种处理方式的结果是一样的。

  给倒数第二项加 $1$ 的影响：
  $$
  \begin{bmatrix}
  1&1\\
  1&0
  \end{bmatrix}
  \rightarrow
  \begin{bmatrix}
  1&0\\
  1&1
  \end{bmatrix}
  \begin{bmatrix}
  1&1\\
  1&0
  \end{bmatrix}
  =
  \begin{bmatrix}
  1&1\\
  2&1
  \end{bmatrix}
  $$
  给数列的**最后一项**减 $1$，接着在数列尾再加两项，两项的值都是 $1$ 的影响：
  $$
  \begin{bmatrix}
  1&1\\
  1&0
  \end{bmatrix}
  \rightarrow
  \begin{bmatrix}
  0&1\\
  1&0
  \end{bmatrix}
  \begin{bmatrix}
  1&1\\
  1&0
  \end{bmatrix}^2
  =
  \begin{bmatrix}
  1&1\\
  2&1
  \end{bmatrix}
  $$
  所以把 `E` 操作按第二种处理方式就行了，给数列的最后一项减 $1$​ 相当于乘 $\begin{bmatrix}1&0\\-1&1\end{bmatrix}$。

  于是 `E` 操作相当于乘 $\begin{bmatrix}1&0\\-1&1\end{bmatrix}\begin{bmatrix}1&1\\1&0\end{bmatrix}^2=\begin{bmatrix}2&1\\-1&0\end{bmatrix}$。

此时这题就很容易了，`APPEND`，`FLIP ` 和 `REVERSE ` 都是可以平衡树维护的，每个结点不仅要维护区间矩阵乘积，还要维护倒着乘的结果，`FLIP` 后的结果，和 `FLIP` 后倒着乘的结果。

复杂度 $O(n\log n)$。

### NOI2021Day2T3

发现每个机器人对纸带的修改本质上只有 $4$ 种：赋值为 $0$，赋值为 $1$，不变，取反，分别用 $0,1,2,3$ 表示。

不难想到容斥原理：枚举一个起始位置集合 $mask$，计算有多少种输入使得机器人从这些位置出发的输出都一样。每条纸带上每个位置受到的修改是确定且独立的，讨论一下一个位置的可行输入，乘起来就是贡献：

- 如果一个位置同时包含 $0,1$ 或 $2,3$，那么输入只能为空，方案数为 $1$。
- 否则，如果一个位置包含两种操作，那么输入可以为空或 $01$ 中的一种，方案数为 $2$。
- 否则，三种输入都可行，方案数为 $3$。

至此，得到一个 $O(2^nmn^2)$ 的做法。

考虑优化计算一个机器人对一个起始位置集合的贡献，记状压数组 $g_{0/1/2/3,S}$ 表示选择起始位置集合 $S$ 时包含 $0/1/2/3$ 的位置，$g$ 可以 $O(2^n)$ 求出，有了 $g$ 也可以 $O(1)$​ 算贡献。

复杂度 $O(2^nm)$。

题目限制 $n \le 32$，猜想是分大小两类计算来平衡复杂度。进一步观察发现当 $mask$ 的最高位大于等于 $\lceil\frac n2\rceil$ 时，修改范围大于 $\lceil\frac n2\rceil$ 的机器人都会爆掉。

枚举 $mask$ 的最高位 $\max$，这样就确定了哪些机器人会爆掉（不用考虑），分两种情况：

- $\max \le \lceil\frac n2\rceil$，这个可以暴力容斥，复杂度 $O(2^{\frac n2}m)$。

- $\max > \lceil\frac n2\rceil$，这意味着需要考虑的机器人修改范围都不超过 $n-\max+1$。

  一个修改范围大小为 $R$​ 的机器人所在的纸带上，一个位置的状态只和两个因素有关：

  - 它前面 $R$ 个位置哪些在 $mask$ 中。

  - 它是否没有被某个区间覆盖。

  如果所有区间都覆盖了某个位置，说明 $mask$​ 的最低位大于等于 $2\max-n+1$​，可以暴力容斥，复杂度 $O(2^{n-\max+1}m)$​。
  否则只需要考虑第一条，可以 `DP`，设 $f_{i,S}$​ 表示已经确定了 $mask$​ 的前 $i$​ 位，其中最后 $n-\max+1$​ 位为 $S$​，转移直接枚举第 $i+1$​ 位选不选即可，可以 $O(2^{n-\max+1}m)$​ 预处理转移系数，复杂度 $O(2^{n-\max+1}n)$​。

总复杂度 $O(2^{n/2}m)$​。

### CF1548D2

根据 Pick 定理：
$$
S=i+\frac b2-1
$$
合法三角形的条件即为 $S\in \mathbb Z \land2S\equiv b \pmod 4$​。

对于三角形 $ABC$​， $2S=|\overrightarrow A\times \overrightarrow B+\overrightarrow B\times \overrightarrow C+\overrightarrow C\times \overrightarrow A|$​​​，由于 $S$ 是整数，所以绝对值不会影响 $S$ 的奇偶性，只需要各个顶点的坐标模 $4$ 的结果就可以知道 $2S \bmod 4$​。

一条线段 $AB$​​​ 的 **边界数** 为线段上整点数减一，$b$​​ 就是三条线段的边界数之和。线段 $AB$​​ 的边界数 $\text{bounds}(A,B)=\gcd(|X_A-X_B|,|Y_A-Y_B|)$​​​​，不太好简单表示。

由于要求 $S$ 为整数，所以 $b$ 为偶数，这是一个很重要的条件，这意味着合法三角形三条边的边界数中至少有一条是偶数，另外两个奇偶性相同，判断 $\text{bounds}(A,B) \bmod 4$ 是 $0$ 还是 $2$ 要容易得多，
$$
\text{bounds}(A,B) \equiv 0\pmod 4 \iff X_A\equiv X_B\pmod 4 \land Y_A\equiv Y_B\pmod 4
$$
在 $\text{bounds}(A,B) \not\equiv 0\pmod 4$ 的前提下
$$
\text{bounds}(A,B) \equiv 2\pmod 4 \iff X_A\equiv X_B\pmod 2 \land Y_A\equiv Y_B\pmod 2
$$
判断这两个条件只需要各个顶点的坐标模 $4$ 的结果。

此时做法就清晰起来了，合法三角形按三条边的边界数奇偶性可以分成 EEE 和 EOO 两类，设 $cnt_{A,x,y,z}$ 表示有多少个点 $B$​​ 满足
$$
X_B\equiv x\pmod 4 \land Y_B\equiv y\pmod 4 \land \text{bounds}(A,B) \equiv z\pmod 4
$$
这个是可以 $O(n^2\log V)$ 预处理的。

考虑分别对两类合法三角形 $ABC$​​​ 计数，先枚举点 $A$​，再枚举
$$
X_B\bmod 4,Y_B\bmod 4,\text{bounds}(A,B)\bmod 4\\X_C\bmod 4,Y_C\bmod 4,\text{bounds}(A,C)\bmod 4
$$
满足
$$
S \in \mathbb Z\\
\text{bounds}(A,B)\equiv\text{bounds}(A,C)\pmod 2\\
X_B\equiv X_C \pmod 2\\
Y_B\equiv Y_C\pmod 2\\
S\equiv \text{bounds}(A,B)+\text{bounds}(A,C)+\text{bounds}(B,C)\pmod 4
$$
使用 $cnt$ 数组可以 $O(1)$​​ 计算贡献。

这样每个 EEE 三角形会被算 $3$ 遍，每个 EOO 三角形会被算 $1$ 遍。

复杂度 $O(n^2\log V)$。

### CF1548E

~~最简单的 3400。~~

把坏格子填成 $1$，其他填成 $0$，问题就是求矩阵中有多少个“1”的四-连通块。

> 引理：对于任意两行 $i,j$​​​，“1” 所在列的集合一定是相互包含的。

不妨假设 $a_i \ge a_j$​​，$a_i+b_k \le x \Rightarrow a_j+b_k \le x$​。

同时，此引理也就是这个矩阵的全部性质了，因为任何一个符合引理的矩阵都是可以构造出 $a,b$ 数组的。此题唯一的条件也就是这个引理了，目标很明确。

我们数连通块的思路是这样的：

- 对于一个连通块 $S$​​​​​​，它上到 $L_r$​​​​​​，下到 $R_r$​​​​​​，左到 $L_c$​​​​​，右到 $R_c$​​​​​​。
- 设 $a_{L_r},a_{L_r+1},\cdots,a_{R_r}$​​​ 中第一个取到最小值的位置为 $i$，显然 $(i,L_c),(i,L_c+1),\cdots,(i,R_c)$​ 都为 “1”。
- 我们希望 $S$ 被 $i$ 数到。

再考虑对于 $i$​，有多少个连通块会被它数到，对于第 $i$ 行的一个 “1” 的连续段 $[l,r]$，它所在的连通块会被 $i$​​ 数当且仅当：

- 它向上不能走到一行 $j$​​​ 满足 $a_j \le a_i$​​​​，形式化地，$\min_{k\in [l,r]}a_k+\max_{k\in (j,i]}b_k>x$​​。
- 它向下不能走到一行 $j$​​ 满足 $a_j<a_i$​​​，形式化地，$\min_{k\in [l,r]}a_k+\max_{k\in [i,j)}b_k>x$​​​。

综上，记 $i$ 前面第一个满足 $a_j\le a_i$ 的 $j$ 为 $pre$， $i$ 后面第一个满足 $a_j< a_i$ 的 $j$ 为 $suf$，连续段 $[l,r]$ 造成贡献当且仅当 $\min_{i\in [l,r]}a_i>x-\max_{i\in (pre,suf)}$，不等式右边对于每个 $i$ 是确定的，而且是可以通过单调栈 $O(n)$​ 预处理的东西，对于左边则可以使用数据结构来维护。

下面我们进一步讨论这个数据结构需要干什么：

- 这个数据结构维护所有连续段 $b$ 的最小值。
- 将所有行以 $a_i$​ 为第一关键字，$i$​​ 为第二关键字从大到小排序。每次序列中一个 $0$ 改成 $1$​，会导致新增连续段，也会导致两个连续段合并，修改就是加入元素和删除元素。
- 询问操作就是查询有多少个元素大于 $key$。

对于新增连续段和合并连续段可以用并查集维护，元素则用反向树状数组维护。

复杂度 $O(n\log n)$。

### 2021“MINIEYE杯”中国大学生算法设计超级联赛（5）T1

把两端同色的边看成实边，两端异色的边看成虚边。

- `1` 操作就是 `access(u)`。
- `2` 操作就是查询两点间的虚边条数，转化一下变成查询一个点到根路径上的虚边条数。
- `3` 操作转化一下就是查询 `dfs` 序区间中的点到根路径上的虚边条数总和。
- `4` 操作就是每条实链点数选二之和。

对于 `23` 操作，需要维护每个点到根路径上的虚边条数，支持区间加、区间求和，树状数组即可。

对于 `4` 操作，在 `LCT` 中维护实链的点数，就可以在 `access` 时维护答案。

复杂度 $O(n\log^2n)$。

### 2021“MINIEYE杯”中国大学生算法设计超级联赛（5）T2

记 $f_{i,j}$ 表示有多少个串恰好有 $i$ 个 `a`、$j$​​ 个 `b`，不难写出它的生成函数 $(x+y+k-2)^L$。

而题目中求的就是 $\sum_{i=0}^L\sum_{j=0}^L[n|i-p][n|j-q][x^i][y^j](x+y+k-2)^L$​​。

题目还保证 $n|P-1$​，考虑单位根反演，得到答案为
$$
\frac 1{n^2}\sum_{i=0}^{n-1}\sum_{j=0}^{n-1}(\omega_n^i+\omega_n^j+k-2)^L\omega_n^{-ip}\omega_n^{-jq}
$$
记 $A_{i,j}=(\omega_n^i+\omega_n^j+k-2)^L$ 可以 $O(n^2\log L)$ 预处理，答案矩阵 $B$ 就是 $A$ 对两维分别做 `IDFT` 得到的。每次固定一维，对另一维做 `IDFT` 就行了，暴力做复杂度 $O(n^3)$，或者 Bluestein + MTT 可以做到 $O(n^2\log n)$。

复杂度 $O(n^3)$ 或者 $O(n^2\log L)$。

### 2021“MINIEYE杯”中国大学生算法设计超级联赛（5）T9

称区间中数量超过一半的数为 **主元素**。

由于一个合法区间只有一个主元素，可以考虑每个主元素的贡献。

#### 做法 1

考虑枚举一个值 $v$​，计算这个值的贡献。

假设 $v$​ 在序列中的出现位置为 $p_1,p_2,\cdots,p_k$​​，区间 $[l,r]$ 中第一个 $v$ 在 $p_i$ 出现，最后一个 $v$ 在 $p_j$​ 出现。

考虑枚举 $j$​，快速查询有多少对合法的 $(l,r)$​。把等于 $v$​ 的位置变成 $1$​，不等于 $v$​ 的位置变成 $-1$​，记前缀和为 $sum_i$​，那么区间 $[l,r]$​ 合法的充要条件为 $sum_{l-1}<sum_r$​，因此需要维护的是 $sum_1,sum_2,\cdots,sum_{p_j}$​ 组成的集合，$j\rightarrow j+1$​ 时，加入的元素是 $sum_{p_j},sum_{p_j+1},\cdots,sum_{p_{j+1}-1}$​，它们的值是连续的一段，所以这是一个区间 $+1$​，对于一个 $r\in[p_j,p_{j+1})$​，合法 $l$​ 的数量就是集合中小于 $sum_r$​​ 的元素数量，这是一个前缀和，而 $[p_j,p_{j+1})$​ 内所有的 $sum_r$​ 构成一个区间，所以询问操作是查询前缀和的前缀和，可以用三个树状数组实现。

复杂度 $O(n\log n)$。

#### 做法 2

考虑分治，计算有多少个合法区间 $[l,r]$​​ 满足 $l\le mid+1 \land r\ge mid$​​。

可以发现区间 $[l,r]$​​ 合法的必要条件是 $[l,mid]$ 和 $[mid+1,r]$ 中至少有一个是合法的。

> 引理：一个序列所有前缀的主元素中本质不同只有 $O(\log n)$ 个。

所以可能产生贡献的 $v$ 只有 $O(\log n)$ 个。枚举 $v$。把等于 $v$ 的位置变成 $1$，不等于 $v$ 的位置变成 $-1$，记前缀和为 $sum_i$，枚举 $r$ 后，需要查询有多少个 $l\le mid+1$ 满足 $sum_{l-1}<sum_r$，这个可以预处理前缀和做到 $O(1)$ 查询。

复杂度 $O(n\log^2n)$。

### 2021“MINIEYE杯”中国大学生算法设计超级联赛（5）T13

先二分一个直径 $D$，考虑用 `DP` 去判定。

设 $f_{u,0/1}$ 表示以下情形以 $u$​ 为端点向子树内延伸的最长链的最小值（不存在时为 $\infty$）：

- 确定了 $u$​ 子树内每个点选哪条边。
- $u$ 选的边是否是 $u$​ 和父亲的连边。
- 子树内直径不超过 $D$。

$f_{u,1}$​​​ 可以直接从每个儿子 $v$​​​ 用 $\min(f_{v,0}+w(u,v),f_{v,1}+\max(w(u,v)-p_v,0))$​​​ 转移过来，如果前两大的值之和大于 $D$​​，$f_{u,1}=\infty$​​，否则就取这些值中的最大值。

$f_{u,0}$​ 的转移需要分析一下，$u$​ 选择的边 $(u,v)$​​ 必须要满足 $v$​ 是 $\min(f_{v,0}+w(u,v),f_{v,1}+w(u,v)-p_v)$​​ 前两大的儿子，所以枚举一下 $v$，就可以直接转移了。

复杂度 $O(n\log V)$。

### AGC041D

考虑任意 $k$​ 道题的总分都小于任意 $k+1$​ 道题的总分这个限制，发现它等价于前 $\lceil\frac n2\rceil$​ 道题的总分小于后 $\lceil\frac n2\rceil-1$​ 道题的总分。

考虑如何生成一个合法的序列 $A$：

- 枚举第 $\lfloor\frac n2\rfloor+1$​ 道题的分值 $c$​，令 $A=(c,c,\cdots,c)$​。
- 多次选一个位置 $j<\lfloor\frac n2\rfloor+1$​​，将 $A_1,A_2,\cdots,A_j$​ 全部减一。
- 多次选一个位置 $j>\lfloor\frac n2\rfloor+1$​，将 $A_j,A_{j+1},\cdots,A_n$​​​ 全部加一。
- 由于 $A_1 \ge 1$​​​，所以第二种操作的次数不得超过 $c-1$​​​，同理第三种操作的次数不得超过 $n-c$​​​。
- 设前 $\lceil\frac n2\rceil$​ 道题的总分减后 $\lceil\frac n2\rceil-1$​ 道题的总分为 $x$​，第一步后 $x=c$​，第二种操作每一次都会使 $x$​ 减小 $j$​，第三种操作每一次都会使 $x$​ 减小 $n-j+1$​​，因此第二、三种操作的总贡献要小于 $c$。

可以看出这是一个完全背包，第二种操作就是添加体积为 $1,2,\cdots,\lfloor\frac n2\rfloor$​​ 的物品，而且最多添加 $c-1$​ 个，第三种操作就是添加体积为 $1,2,\cdots,\lceil\frac n2\rceil-1$​​​​ 的物品，最多添加 $n-c$​ 个，总体积要小于 $c$​。

对于第二种操作，考虑预处理 $L_{i,j}$​ 表示选择 $i$ 个物品，总体积为 $j$ 的方案数。用传统背包做复杂度肯定不行，事实上它能直接转移：
$$
L_{i,j}=L_{i-1,j-1}+L_{i,j-i}-L_{i-1,j-i-\lfloor\frac n2\rfloor}
$$
对于第三种操作，同理可以预处理 $R_{i,j}$。

然后就可以枚举 $c$​ 后 $O(n)$​​ 计算合法方案数。

复杂度 $O(n^2)$。

### AGC027D

构造的思路是先黑白染色，然后填好黑格，再让每个白格满足：

- 它大于周围四个黑格。
- 它模周围四个黑格都等于 $1$。

填白格的过程是容易的，对于一个白格，先算出周围四个黑格的 $\text{lcm}$，然后尝试填 $\text{lcm}+1$​，如果已经填过了，就继续尝试 $2\text{lcm}+1,3\text{lcm}+1,\cdots$​。

如果没有值域限制，这题就做完了，考虑怎样让填的数尽可能小。

填白格没有什么好优化的（尝试过优先填 $\text{lcm}$​​ 较大的格子，但完全没有效果），所以考虑如何填黑格，才能使 $\text{lcm}$ 比较小。

- 填法一：顺序填或随机填，大概只能构造 $N$ 等于一百多。
- 填法二：考虑到一个白格周围四个黑格有两个是同一行的，有两个是同一列的，令 $A_{i,j}$ 是 $\text{lcm(i,j)}$ 的倍数，具体怎么确定，像确定白格那样确定，大概能构造 $N$ 等于两百多。
- 填法三：考虑到一个白格周围四个黑格只涉及四条斜线，令 $A_{i,j}$ 是 $\text{lcm}(i+j,i-j+n)$ 的倍数，大概能构造 $N$ 等于 $425$ 左右。
- 填法四：经过一番尝试，令 $A_{i,j}$​​ 是 $\text{lcm}(i+(n-j+1),i-(n-j+1)+n)$​（就是把列编号倒过来）可以通过。
- 填发五：考虑给每条斜线分配一个质数，黑格就等于所在的两条斜线质数的乘积，白格就等于周围四条斜线质数的乘积加一，一定不会有数重复。

填发四需要用 `set` 维护哪些数填过，复杂度 $O(n^2\log n)$​​。

填发五复杂度 $O(n^2)$。

### AGC025D

对于两个距离为 $\sqrt D$​ 的点 $(x_1,y_1),(x_2,y_2)$​，考虑 $x_1-x_2,y_1-y_2$​​ 的奇偶性：
$$
x_1 \equiv x_2 \pmod 2,y_1 \equiv y_2 \pmod 2 \iff D \equiv 0 \pmod 4\\
x_1 \equiv x_2 \pmod 2,y_1 \not\equiv y_2 \pmod 2 \iff D \equiv 1 \pmod 4\\
x_1 \not\equiv x_2 \pmod 2,y_1 \not\equiv y_2 \pmod 2 \iff D \equiv 2 \pmod 4\\
$$
因此 $D \bmod 4$​ 说明了两点坐标差的奇偶性。

> 引理：将平面上距离为 $\sqrt D$ 的点对连边后是一张二分图。

考虑构造一个黑白染色方案，设 $\text{color}(x,y,D)=0/1$ 表示点 $(x,y)$ 的颜色。

- 当 $D\equiv 3\pmod 4$ 时，没有边，$\text{color}(x,y,D)=0$。

- 当 $D\equiv 2\pmod 4$ 时，$\text{color}(x,y,D)=x\bmod 2$，这样距离为 $\sqrt D$ 的点对就必然异色。
- 当 $D \equiv 1\pmod 4$ 时，$\text{color}(x,y,D)=(x+y)\bmod 2$，这样距离为 $\sqrt D$ 的点对就必然异色。

- 当 $D\equiv 0\pmod 4$ 时，$\text{color}(x,y,D)=\text{color}(\lfloor\frac x2\rfloor,\lfloor\frac y2\rfloor,\frac D4)$​​，下面证明为什么合法：

  对于两个距离为 $\sqrt D$ 的点 $(x_1,y_1),(x_2,y_2)$，由于 $x_1 \equiv x_2 \pmod 2,y_1 \equiv y_2 \pmod 2$，所以
  $$
  \lfloor\frac {x_1}2\rfloor-\lfloor\frac {x_2}2\rfloor=\frac 12(x_1-x_2)\\
  \lfloor\frac {y_1}2\rfloor-\lfloor\frac {y_2}2\rfloor=\frac 12(y_1-y_2)
  $$
  进一步：
  $$
  \begin{aligned}
  &(x_1-x_2)^2+(y_1-y_2)^2=D\\
  \Rightarrow &(\lfloor\frac {x_1}2\rfloor-\lfloor\frac {x_2}2\rfloor)^2+(\lfloor\frac {y_1}2\rfloor-\lfloor\frac {y_2}2\rfloor)^2=\frac D4\\
  \Rightarrow &\text{color}(\lfloor\frac {x_1}2\rfloor,\lfloor\frac {y_1}2\rfloor,\frac D4)\ne\text{color}(\lfloor\frac {x_2}2\rfloor,\lfloor\frac {y_2}2\rfloor,\frac D4)
  \end{aligned}
  $$

对于 $D_1$​ 和 $D_2$​ 分别黑白染色后本质有 $4$​ 种颜色，这 $4$​​ 种颜色中肯定有一种点数大于等于 $n^2$​，输出这种颜色的 $n^2$​ 个点即可。

复杂度 $O(n^2)$。

### AGC036D

没有负环等价于差分约束有解，假设解为 $d_1,d_2,\cdots,d_n$​，不妨令 $d_1=0$。

由于 $i\rightarrow i+1$ 的边是不能被删的，所以 $d$ 是单调不增的，也就是一段 $0$，一段 $-1
$，一段 $-2\cdots$ 的形式。

考虑一段一段的枚举 $d$​​​，上一段是 $[a,b]$​​​，即 $d_{a,a+1,\cdots,b}=x+1$​​​，枚举了新的一段 $[b+1,c]$​​，即 $d_{b+1,b+2,\cdots,c}=x$​​​​​，​分析哪些边需要删：

- 对于 $b+1\le i < j \le c$，边 $(i,j)$ 需要删除。
- 对于 $b+1\le i\le c,j<a$，边 $(i,j)$ 需要删除。

然后就可以 `DP` 了，设 $f_{a,b}$ 表示填的最后一段为 $[a,b]$ 时的最小代价，转移为
$$
f_{a,b}+\text{cost}_1(b+1,c)+\text{cost}_2(b+1,c,a-1)\rightarrow f_{b+1,c}
$$
其中 $\text{cost}_1$​ 和 $\text{cost}_2$​​ 在预处理二维前缀和后可以 $O(1)$ 算。

复杂度 $O(n^3)$。

### AGC045D

如果 `Snuke` 按到了 $p_i=i$ 的位置就死了，所以他要最小化有解时死的概率，分析 `Snuke` 的最优策略：

- 最初需要按下一个按钮，由于 `Snuke` 不知道排列，所以按每个按钮都是一样的，不妨按 $1$。
- 如果按下了 $p_i=i$ 的按钮就死了。
- 否则，$p_i$​ 一定是一个安全的按钮，继续按下 $p_i$，这样就可以安全地按下许多按钮。
- 又需要尝试一个按钮时就按没按过的编号最小的按钮。

于是，得到了 `Snuke` 胜利的充要条件：假设 $1-A$ 中第一个满足 $p_i=i$ 的 $i$ 为 $\min$​​，$\forall i>A,\exists j<\min$ 使得 $j$ 能到达 $i$。

容易想到枚举 $\min$​，把排列看成若干个循环，要求 $\min$​ 前面没有孤立点，这个可以容斥：

- 钦定 $i$​​​ 个孤立点，系数为 $(-1)^i\binom{\min-1}i$​。
- 对于 $[1,\min-1]$​​ 中剩下的 $\min-1-i$​​ 个点先生成若干个循环，方案数为 $(\min-1-i)!$。
- 对于 $[A+1,n]$ 中的点，它们只能加入前面的循环，方案数为 $(\min-1-i)^{\overline{n-A}}$​。
- 对于 $[\min+1,A]$ 中的点，它们既可以加入前面的循环，又可以新建一个环，方案数为 $(\min-1-i+n-A)^{\overline{A-\min}}$。

综上，得到 $\min$ 的贡献为：
$$
\sum_{i=0}^{\min-1}(-1)^i\binom{\min-1}i\frac{(n-1-i)!(\min-1-i)}{\min-1-i+n-A}
$$
当 $\min$​ 不存在时需要特判，贡献为：
$$
\sum_{i=0}^A(-1)^i\binom Ai(n-i)!
$$
复杂度 $O(A^2+n)$。

### AGC041E

#### 对于 $T=1$

将网络抽象成一张有向图：

- 将每条线的起点、终点和平衡器的端点抽象成结点。
- 同一条线上的结点后面向前面连边。
- 平衡器抽象成两个方向的边。

考虑暴力怎么做，枚举最终汇聚到第 $t$​ 条线，判断 $t$ 的终点是否可以到达所有的起点。

可以用一个 `bitset` 来压哪些汇点能到达这个点，然后 `DFS` 来求这些 `bitset`。可以做到 $O(\frac {nm}{\omega})$​​​ 的复杂度。

#### 对于 $T=2$

$n=2$ 时显然无解，下面构造说明了 $n>2$ 时一定有解。

考虑从右往左依次插入每个平衡器，维护 $size_i$ 表示当前网络有多少个起点会到达第 $i$ 条线的终点。

- 初始时，$size_i=1$。
- 加入平衡器 $(x,y)$ 时，要么 $size_x+1$，要么 $size_y+1$，选择 $size_x$ 和 $size_y$ 中较小的一个 $+1$。一定不会出现 $size_x=n-1\land size_y=n-1$ 的情况，因为 $size_x+size_y\le n$。

复杂度 $O(n+m)$。

### ABC214G

以下解法可以解决 $n \le 10^5$​​ 的问题。

设 $F_k$​​​ 表示确定 $k$​​​ 个位置的值 $r_{i_1},r_{i_2},\cdots,r_{i_k}(i_1<i_2<\cdots<i_k)$​​​​​​​​ 满足以下条件的方案数：
$$
\forall x\in[1,k],r_{i_x}=p_{i_x}\lor r_{i_x}=q_{i_x}
$$
根据二项式反演，答案为
$$
\sum_{i=0}^n(-1)^iF_i(n-i)!
$$
将 $p_i,q_i$​​​​​​​ 连边，得到一张由若干个环组成的图，选择一个满足 $r_i=p_i\lor r_i=q_i$​​ 的位置 $i$​​​​ 就是选择一条边并占用一个端点，这对于每个环是独立的，所以对每个环求出 $F$​​ 数组，再用分治 `FFT` 合并就可以得到整张图的 $F$​ 数组。

考虑求一个大小为 $m$​ 的环的 $F$​ 数组，假设点编号为 $1,2,\cdots,m$，边为 $(1,2),(2,3),\cdots,(m-1,m),(m,1)$，将选择 $i$ 条边的方案分为以下两类：

- 不选边 $(1,2)$​，把边 $(2,3)$ 占用 $2$ 的方式编号为 $1$，边 $(2,3)$ 占用 $3$ 的方式编号为 $2$，边 $(3,4)$ 占用 $3$ 的方式编号为 $3$，边 $(3,4)$ 占用 $4$ 的方式编号为 $4$，依次类推。方案就是从 $2m-2$​ 种选择方式选 $i$​ 种，限制就是编号相邻的不能同时选择。

  > 引理：从 $n$ 个物品中选 $r$ 个，编号相邻的不能同时选择的方案数为 $\binom {n-r+1}r$​。
  >
  > 证明：将选择的第 $i$ 个物品的编号减去 $i-1$ 就得到了从 $n-r+1$ 个物品中选 $r$ 个的方案数。

  这部分方案数为 $\binom{2m-i-1}i$​。

- 选边 $(1,2)$​​，那么边 $(1,2)$​​ 可以占用 $1$​​ 或 $2$​​，但两种方式是等价的，不妨假设占用了 $1$​​。方案就是从 $2m-3$​​ 种选择方式选 $i-1$​​​​ 种，限制还是编号相邻的不能同时选择。

  这部分方案数为 $2\binom{2m-i-1}{i-1}$。

对于一个大小为 $m$ 的环
$$
F_i=\binom{2m-i-1}i+2\binom{2m-i-1}{i-1}=\binom{2m-i}i+\binom{2m-i-1}{i-1}
$$
然后每个环的 $F$​ 就可以 $O(n)$​ 求了，复杂度瓶颈在于分治 `FFT`，复杂度 $O(n\log^2n)$​​​。

分治 `FFT` 有两种优化：

- 由于每个环的大小之和为 $n$，故只有 $O(\sqrt n)$ 种大小不同的环，大小相同的环可以通过快速幂 $O(n\log n)$​ 地算出乘积。
- 整个分治过程形成一棵二叉树的结构，总时间就是 $\sum_{u\in \mathbb{leaf}}\text{degree}_u\text{depth}_u$​，最小化时间就是 `Huffman` 树，每次贪心地将两个次数最小的多项式乘起来。 ​​​

### ABC214H

走到了一个强连通分量就肯定会走完内部的所有点，缩点后图就变成了 `DAG`，假设原图就是 `DAG`。

想到用最小费用流解决：

- 把每个点 $u$ 拆成 $\text{in}_u$ 和 $\text{out}_u$。
- $\text{in}_u$ 向 $\text{out}_u$ 连一条容量为 $1$​，费用为 $-X_u$​ 的边，再连一条容量为 $K$，费用为 $0$ 的边。
- 对于原图中的边 $(u,v)$​，$\text{out}_u$​ 向 $\text{in}_v$​ 连一条容量为 $K$​，费用为 $0$​ 的边。
- $S$ 向 $\text{in}_1$ 连一条容量为 $K$，费用为 $0$ 的边，$\text{out}_u$ 向 $T$ 连一条容量为 $K$，费用为 $0$ 的边。

`SSP` 算法肯定是通过不了的，考虑变成 `Primal-Dual` 算法可以做的问题。

#### Sol 1

初始图是一张 `DAG`，可以跑一遍 `DP` 预处理最短路作为点的初始势能。

#### Sol 2

求出 `DAG` 的一组拓扑序，然后给每个点按拓扑序从大到小重新编号（也就是缩完点后的编号）。

容易构造一组满足差分约束的初始势能：

- $S$​​ 势能为 $\sum X_u$​​，$T$​​​ 势能为 $0$​​。
- $\text{in}_u$​ 的势能为 $\sum_{i=1}^uX_i$​，$\text{out}_u$ 的势能为 $\sum_{i=1}^{u-1}X_i$。

上述两种做法复杂度都是 $O(nK\log n)$。

### AGC027E

考虑什么样的串能变成单个字母 $a$ 或 $b$。

打表发现能变成 $a$ 的串 $S$ 满足的 $a,b$ 数量关系是 $a$ 的数量减 $b$ 的数量模 $3$ 余 $1$，并且这个条件在 $|S|$ 为偶数时也是充分条件，当 $|S|$ 为奇数时恰好多了一个串 $ababab\cdots aba$​。​

> 引理一：记 $p(S)$​ 表示 $S$​ 中 $a$​ 的个数减 $b$​ 的个数模 $3$​，$S$​ 能变成单个字母 $c$​ 当且仅当：
>
> - $p(S)=p(c)$
> - $S=c$ 或 $S$ 中有相邻的相同字母。

必要性显然，下面证明充分性：当 $|S|\le 3$ 时显然成立，当 $|S|>3$ 时取出 $S$ 中最长的连续相同子串，不妨假设它是 $n$ 个 $a$，分两种情况讨论：

- $n \ge 4$，直接将前两个 $a$​​ 合并，$|S|$ 减小了 $1$，并且还满足引理条件。
- $n\le 3$​，由于 $|S|>3$​，这个子串不可能前后都没有字母，不妨假设它不在开头，那么它前面必然是 $b$​，将前两个 $a$​ 合并成 $b$，那么此时有两个 $b$ 会相邻，$|S|$ 减小了 $1$​，并且还满足引理条件。

然后问题就转化成了有多少个串 $T$ 满足以下条件：

- 存在一种将 $S$ 划分为 $|T|$ 段的方式，使得每一段与 $T$​​ 中的对应字母满足引理一。

这个问题的主要难点在于引理一的条件二。

事实上，当 $S$​ 中有相邻的相同字母时，**忽略引理一的条件二** 不会影响答案。

> 引理二：若 $S$ 中有相邻的相同字母，$S$ 能够变成 $T$​ 当且仅当：
>
> - 存在一种将 $S$​ 划分为 $|T|$​ 段的方式，设 $S$​ 被划分成了 $S_1,S_2,\cdots,S_{|T|}$​，$T$​ 中每个字母分别为 $T_1,T_2,\cdots,T_{|T|}$​。
> - 满足 $\forall i,p(S_i)=p(T_i)$。

必要性显然，下面证明充分性：

- 假设存在一组满足上述条件的划分 $(S_1,S_2,\cdots,S_{|T|})$​。
- 将 $S_1,S_2,\cdots,S_{|T|-1}$ 的长度最小化得到新的划分 $(S_1',S_2',\cdots,S_{|T|}')$​。
- 由于最小化，容易发现 $S_1,S_2,\cdots,S_{|T|-1}$​ 已经满足了引理一。
- 此时 $S_{|T|}$ 有可能不合法，比如 $S_{|T|}=abab\cdots aba$，由于不合法时 $|T|>1$，可以让 $S_{|T|}$ 只保留最后一个字母，把前面的部分扔给 $S_{|T|-1}$，于是 $T$ 删去最后一个字母对于 $S$​ 删去最后一个字母满足引理二，故 $S$ 可以变成 $T$。

有了引理二就很好做了，特判掉 $S$ 中没有相邻的相同字母的情况，容易用一个自动机来判断 $T$​ 是否合法，计数可以在自动机上 `DP`。

复杂度 $O(n)$。

### Gym101667G

如果两个楼梯一个朝上一个朝下，那么就没有封闭区域，如果两个朝下可以对称一下，变成两个都朝上的情况。

可以看出一个封闭区域开始的标志是 L 横线和 U 竖线相交，结束的标志是 U 横线和 L 竖线相交。考虑用扫描线来求出每一个封闭区域，从左到右处理每一条竖线，并维护变量 `isRegion` 表示当前竖线是否经过一个封闭区域，`area` 表示当前区域的面积：

- 当 `isRegion` 为真时将 `area` 加上当前竖线和上一条竖线之间矩形的面积。
- 当一个封闭区域开始时清零 `area`。
- 当一个封闭区域结束时，答案累加上 `area`。

复杂度 $O(n+m)$。

![.png](https://i.loli.net/2021/09/08/a4fEq6tJWRbrxDy.png)

### Gym101667J

完美匹配的存在性容易想到 Hall 定理，设 $f(S)$ 表示与点集 $S$ 距离恰好等于 $1$ 的点集，问题转化为判定：
$$
\forall |S|\le\frac n2,|T|=\frac n2,S \cap T=\varnothing\\
|f(S)\cap T| \ge |S|
$$
由于 $|f(S)\cap T|$ 最小值为 $|f(S)|+|S|-\frac n2$，条件改写为 $|f(S)|\ge\frac n2$。

考虑求 $\min_{|S|\le \frac n2}|f(S)|$，事实上这就是图的最小点割（删去最少的点使图不连通）：

- $f(S)$ 是 $S$ 与 $V-S-f(S)$ 之间的一组点割。
- 任意一组点割 $T$ 割出来的多个连通块都有一个点数不超过 $\frac n2$，设这个连通块的点集为 $S$，那么 $|f(S)|\le |T|$。

求最小点割就是每对点都求一遍最小割：

- 把每个点 $u$ 拆成 $\text{in}_u$ 和 $\text{out}_u$。
- $\text{in}_u$ 向 $\text{out}_u$ 连一条代价为 $1$ 的边。
- 对于原图中的边 $(u,v)$，$\text{out}_u$ 向 $\text{in}_v$，$\text{out}_v$ 向 $\text{in}_u$ 分别连一条代价为 $\infty$ 的边。
- 枚举两个不同的点 $u,v$，用 $\text{out}_u$ 到 $\text{in}_v$ 的最小割更新答案。

复杂度 $O(n^2\cdot flow)$。

~~负~~优化：注意到如果最小点割小于 $\frac n2$，那么不在割中的点就超过一半，每次随机选取一个点 $u$，枚举点 $v$，求 $\text{out}_u$ 到 $\text{in}_v$ 的最小割，都有大半的概率求出最优解，如果随机 $k$ 次，复杂度为 $O(nk\cdot flow)$。

### ABC215H

如何判定当前的卷心菜是否能满足所有公司？

- $S$ 向卷心菜 $i$ 连容量为 $A_i$ 的边。
- 公司 $i$ 向 $T$ 连容量为 $B_i$ 的边。
- 如果 $c_{i,j}=1$，那么卷心菜 $i$ 向公司 $j$ 连容量为 $\infty$ 的边。
- $\max flow=\sum_{i=1}^mB_i$。

也等价于 $T$ 的所有入边不是最小割。由于 $S$ 的出边很少，考虑枚举最小割中有哪些 $S$ 的出边，假设这些边为 $mask$，割掉 $mask$ 后有一些 $T$ 的入边就不需要割了，假设这些边的容量和为 $sum$，那么需要吃点的卷心菜数量就是 $\sum_{i\in mask}A_i-sum+1$，要求 $sum>0$。

这时就可以解决第一问了，考虑对于所有 $mask$，怎么求它们的 $sum$，注意到 $B_i$ 贡献给 $mask$ 的条件是 $mask$ 包含所有能供应给公司 $i$ 的卷心菜，可以用子集前缀和（`FMT`）求出。然后枚举 $mask$ 用 $\sum_{i\in mask}A_i-sum+1$ 更新第一问的答案即可。

第二问还要进一步分析，为了不算重，我们枚举一个 $mask$ 表示被吃的卷心菜品种的集合，一个 $mask$ 可行当前仅当存在一个取到第一问答案的 $S$，使得 $mask\subseteq S$，这个同样可以用 `FMT` 做。设第一问答案为 $ans$，最后是对于一个 $mask$，求有多少种从 $mask$ 中吃掉 $ans$ 个卷心菜的方式，满足 $mask$ 中的每种卷心菜至少被吃一个。容易想到容斥，钦定一些卷心菜品种不吃，然后不考虑每种卷心菜必吃的限制，但对每个 $mask$ 都通过容斥来计算复杂度高达 $O(3^n)$。

设 $f_S$ 表示有多少种从 $S$ 中吃掉 $ans$ 个卷心菜的方式，满足 $mask$ 中的每种卷心菜至少被吃一个，发现
$$
\sum_{T\subseteq S}f_T=\binom{\sum_{i\in S}A_i}{ans}
$$
对右边做 `IFMT` 就可以求得 $f$ 数组。

复杂度 $O(n2^n+nm)$。

### AGC020E

先考虑对于一个串 $S$ 如何单独计算答案，这个不难，容易想到用区间 `DP` 做。设 $f_{l,r}$ 表示子串 $[l,r]$ 的改写方案数，转移分两种：

- $s_l$ 没有参与改写，贡献为 $f_{l+1,r}$。
- $s_l$ 参与改写了，枚举最外层的覆盖 $s_l$ 的改写：周期 $T$ 和循环次数 $i>1$，如果合法，则贡献为 $f_{l,l+T-1}\cdot f_{l+Ti,r}$。

当尝试用区间 `DP` 做原问题的时候，发现做不了，因为当 $s_l$ 参与改写时，原来的 $f_{l,l+T-1}$ 不再是一个区间的 `DP` 值。详细地说，设 $f(s)$ 表示字符串 $s$ 的答案（子集的改写方案数总和），设 $suf(i)$ 表示 $s$ 从 $s_i$ 开始的后缀，转移为两种：

- 第一个字符没有参与改写，贡献为 $(s_0+1)f(suf(1))$。

- 第一个字符参与了，枚举最外层的覆盖 $s_l$ 的改写：周期 $T$ 和循环次数 $i>1$，由于每个周期内要相等，所有子集的限制要叠加，设 $s[l,r]$ 表示 $s$ 的第 $l$ 个字符到第 $r$ 个字符的子串，设
  $$
  t=s[0,T-1]\&s[T,2T-1]\&\cdots\&s[T(i-1),Ti-1](\&\ is\ \text{bitand})
  $$
  那么贡献为 $f(t)\cdot f(suf(Ti))$。

这里的复杂度上限看起来是 $O(2^{n+1})$，这个题最重要的地方就是，你要看出来这个做法其实是 $O(能过)$ 的，进而分析出其真正的复杂度，而不是被假上限给吓跑了。

下面证明，有一个上界是 $O(n^3+2^{\frac n8})$。首先长度小于等于 $\frac n8$ 的串最多有 $2^{\frac n8}$ 个，长度大于等于 $\frac n8$ 的串最多被压缩两次（因为每压缩一次长度减半），只有三种压缩方式：

- 先选择一个子段划分成 $2$ 段，再选择一个子段划分成 $2$ 段。
- 先选择一个子段划分成 $2$ 段，再选择一个子段划分成 $3$ 段。
- 先选择一个子段划分成 $3$ 段，再选择一个子段划分成 $2$ 段。

显然第一种压缩方式可以得到的串是最多的，考虑第一种压缩方式得到的串是怎样的，形如：

$s[a,a+k-1]\&s[a+k,a+2k-1]\&s[b,b+k-1]\&s[b+k,b+2k-1]$

显然它是由 $a,b,k$ 三个参数决定的，故数量是 $O(n^3)$，因此三种压缩方式的总和也是 $O(n^3)$。

另外，通过打表可以求出长度大于 $12$ 的串更为精准的上界为 $41703$。

### CF1562E

先分析最长上升子序列有什么性质，假设最长上升子序列为
$$
s[l_1,r_1],s[l_2,r_2],\cdots,s[l_k,r_k]
$$
比较显然的是对于每个 $l$，选择的 $r$ 是一个区间。更强的结论是存在一组最优解满足对于每个 $l$，选择的 $r$ 是一个后缀。

反证法：假设对于一个 $l$，选择的 $r\in[r_1,r_2]$，其中 $r_2<n$，$s[l,r_2]$ 之后的子串为 $s[l',r']$。如果 $s[l,r_2+1]<s[l',r']$，直接在 $s[l,r_2]$ 后插入 $s[l,r_2+1]$，得到一组更优的解。否则 $s[l,r_2]<s[l',r']\le s[l,r_2+1]$，说明
$$
s[l',l'+r_1-l]=s[l,r_1]\\
s[l',l'+r_1+1-l]=s[l,r_1+1]\\
s[l',l'+r_1+2-l]=s[l,r_1+2]\\
\cdots\\
s[l',l'+r_2-l]=s[l,r_2]
$$
于是可以用前者们一一替换后者们，得到一组不存在 $l$ 作为左端点的子串的解。

然后就可以 `DP` 了，设 $f_i$ 表示以 $s[i,n]$ 结尾的最长上升子序列，转移为
$$
f_i=\max_{j<i\land s[j,n]<s[i,n]}f_j+n-\text{lcp}(s[i,n],s[j,n])+1
$$
可以预处理 $\text{lcp}(s[i,n],s[j,n])\leftarrow \text{lcp}(s[i+1,n],s[j+1,n])$，复杂度 $O(n^2)$。

#### 优化

可以优化到 $O(n\sqrt n)$。

首先是 `LCP`  怎么处理，通常是使用后缀数组的 `height` 数组，这里也可以这么处理。

求出后缀数组 $SA$ 和 `height` 数组，转移为
$$
f_{SA_i}=\max_{j<i \land SA_j<SA_i}f_{SA_j}+\max_{k=j+1}^in-height_k+1
$$
注意关于 `height` 的那项是一个后缀 $\max$，考虑单调栈维护，栈内维护两元组 $(v,S)$，每次将 $(n-height_{i+1}+1,\{i\})$ 压栈。假设栈顶元素为 $(v_1,S_1)$，下一个元素为 $(v_2,S_2)$，如果 $v_1\ge v_2$，就把这两个元素合并成 $(v_1,S_1\cup S_2)$。

在插入 $i$ 个元素后，就可以这样计算 $f_{i+1}$：遍历栈内每个三元组 $(v,S)$，用 $\max_{j \in S\land SA_j\le SA_i}+v\rightarrow f_{SA_{i+1}}$。但栈内元素可能很多，不能全部遍历，考虑把 $|S|\le \sqrt n$ 的栈元素的贡献用一个数据结构 $A$ 一起维护，每个 $|S|>\sqrt n$ 的元素用数据结构 $B$ 单独维护，这样就只需要遍历最多 $\sqrt n$ 个 $|S|>\sqrt n$ 的栈元素，即在 $A$ 中查询一次前缀 $\max$，$B$ 中查询 $\sqrt n$ 次前缀 $\max$。

元素合并的时候需要分类维护：

- 当 $|S_1|+|S_2|\le \sqrt n$ 时，相当于把 $S_2$ 的贡献整体加上一个正数，可以在 $A$ 中进行 $|S_2|$ 次增大修改操作，这类操作总共不超过 $n\sqrt n$ 次。
- 当 $|S_1|>\sqrt n\land |S_2|\le \sqrt n$ 时，在 $B$ 中进行 $|S_2|$ 次插入新元素，这类操作总共不超过 $n$ 次。
- 当 $|S_1|,|S_2|>\sqrt n$ 或 $|S_1|,|S_2|\le \sqrt n\land |S_1|+|S_2|>\sqrt n$ 时，用 $|S_1|+|S_2|$ 个元素重构一个 $B$，这类操作总共不超过 $\sqrt n$ 次。

综上，当 $A$ 做到 $O(1)$ 修改，$O(\sqrt n)$ 查询，$B$ 做到 $O(\sqrt n)$ 修改，$O(1)$ 查询时，复杂度为 $O(n\sqrt n)$。

因为修改是增大值，查询是前缀 $\max$，$A$ 和 $B$ 都可以通过分块实现。

### CF1562F

> 有个长度为 $n$ 的序列 $A$，元素两两不同且值域连续，但你不知道这个序列，每次可以询问两个不同数的 $\text{lcm}$，最多使用 $n+5000$ 次询问求出 $A$。
>
> $n\le 10^5,A_i\le 2\cdot 10^5$

如果 $\gcd(a,b)=1$，那么 $\text{lcm}(a,b)=ab$，如果求出了 $A$ 序列中最大的质数 $p$，就只需要 $n-1$ 次询问就可以求出 $A$。

先考虑 $p$ 存在的情况，怎么求出 $p$ 和它的位置？询问 $\text{lcm}(A_1,A_2),\text{lcm}(A_2,A_3),\text{lcm}(A_3,A_4),\cdots,\text{lcm}(A_{n-1},n)$，所有质因子中最大的就是 $p$，同时也可以推断出 $p$ 的位置。然后再进行 $n-1$ 次询问就求出了 $A$，询问次数为 $2n-2$，可以处理 $100<n\le7500$。

再分别考虑 $n\le 100$ 和 $n>7500$ 的情况。

$n\le 100$ 可以先两两询问 $\text{lcm}$，再逐个确定。当 $n>3$ 时至少有两个奇数，根据两个奇数的 $\text{lcm}$ 为奇数就可以确定所有数的奇偶性，再取 $\text{lcm}$ 中最大的一个，它一定是 $\max A_i(\max A_i-1)$，结合奇偶性就可以确定最大的 $A_i$，然后删除最大值，重复上述过程，直到 $n=3$ 时，分类讨论即可。

当 $n>7500$ 时需要用不超过 $5000$ 次询问求出 $A$ 序列中最大的质数 $p$，然而比较困难，考虑不找最大的质数，找一个大于 $450$ 的质数 $p'$ 就行了。

考虑随机询问 $\text{lcm}(A_i,A_j)$，如果它是两个大于 $450$ 的质数 $p,q$ 的乘积，那么 $A_i,A_j$ 一定就是 $p,q$，考虑进一步确定 $A_i$，随机一个 $k$，如果 $p\not|\text{lcm}(A_i,A_k)$ 说明 $A_i=p$，如果 $q\not|\text{lcm}(A_j,A_k)$ 说明 $A_i=q$，期望的总随机次数是 $O(\ln^2n)$。

考虑
$$
\text{lcm}(p',x)=
\begin{cases}
x&(x|p')\\
xp'&(x\not|p')
\end{cases}
$$
如果 $\text{lcm}(p',x)>2\cdot 10^5$ 就说明 $x\not|p'$，可以确定 $x$ 的值，这样至少可以确定 $n-900$ 个数，并且最大的质数 $p$ 一定被确定了，最后再用 $p$ 和剩下的数询问即可。



### CodeChef-btree

> 定义 $S(u,k)$ 表示与 $u$ 距离不超过 $k$ 的点集。
>
> 给定一棵 $n$ 个点的树，$q$ 次询问，每次询问 $|S(u_1,k_1)\cup S(u_2,k_2)\cup S(u_3,k_3)\cup \cdots \cup S(u_{m_i},k_{m_i})|$ 。
>
> $n,q\le 5\cdot 10^4,\sum_{i=1}^qm_i\le 5\cdot 10^5$。

考虑 $|S(u,k)|$ 怎么求，可以离线后点分治，也可以建出点分树在线求。

#### Sol 1

先对 $u_1,u_2,u_3,\cdots,u_{m_i}$ 建虚树，假设虚树中非关键点的半径为 $0$。

如果 $(u_1,k_1),(u_2,k_2)$ 满足 $k_1-\text{dis}(u_1,u_2)>k_2$，就说明了 $S(u_2,k_2)\subsetneq S(u_1,k_1)$，即 $S(u_2,k_2)$ 是没用的，但不能删去它，而是令 $k_2=k_1-\text{dis}(u_1,u_2)$。

考虑对所有点的半径更新，直到不能更新为止，一种方法是像 `Dijkstra` 一样每次取半径最大的点更新周围点的半径，另一种做法是树形 `DP`，自底向上更新一遍，再自顶向下更新一遍。

然后发现一个很好的性质，记虚树点集为 $V$，边集为 $E$，$u$ 的半径为 $r_u$，答案等于 $\sum_{u\in V}|S(u,r_u)|-\sum_{(u,v)\in E}|S(u,r_u)\cap S(v,r_v)|$。证明很简单，考虑自顶向下将每个 $S(u,r_u)$ 并入，每次新增的点数为 $|S(u,r_u)|-|S(u,r_u)\cap S(fa_u,r_{fa_u})|$。

$|S(u,r_u)|$ 可以在点分树上查询，$|S(u,r_u)\cap S(v,r_v)|$ 是一个 $S(w,r)$，其中 $w$ 可能是顶点，也可能是一条边的中点，如果在每条边上新建一个点，$w$ 就一定是顶点了。

复杂度 $O((n+m)\log n)$。

#### Sol 2

答案求的是并集的大小，是 `bitset` 可以优化的。

注意到 $nq \le 2.5\cdot 10^9,n\sum_{i=1}^qm_i\le 2.5\cdot 10^{10}$，一个时间复杂度 $O(\frac{n\sum_{i=1}^qm_i}\omega)$，空间复杂度 $O(\frac{nq}\omega)$ 的算法是可以通过的。

怎么快速求 $S(u,k)$ 的 `bitset` 形式，希望能做到 $O(\frac n\omega)$。

考虑把所有询问离线下来，在点分治的过程中每个 $S(u,k)$ 都被分解成了 $O(\log n)$ 个形如”深度不超过 $d$ 的点集”的并，如果每次都把”深度不超过 $d$ 的点集”并上去，求 $S(u,k)$ 就是 $O(\frac{n\log n}\omega)$ 的，不太行。注意到这 $O(\log n)$ 个点集的范围分别为 $n,\frac n2,\frac n4,\frac n8,\cdots$，总和是 $2n$ 的，但每个点集编号的范围都是 $[1,n]$，每次并上去就太不优秀了，如何缩小编号的范围？只需要把所有点按照点分树的 `DFS` 序重新编号，那么每个点集编号的范围就缩小到了 $n,\frac n2,\frac n4,\frac n8,\cdots$，每次只需要并到一个区间上，复杂度 $O(\frac n\omega)$。

做法就是对每个询问开一个 `bitset`，点分治的过程中更新 `bitset` 的一个区间，答案就是对于 `bitset` 的 `popcount`，需要用 `unsigned long long` 实现 `bitset`，`popcount` 也建议手写，预处理 $[0,65536)$ 内所有数的 `popcount`，把每个 `unsigned long long` 拆成四个 $[0,65536)$ 内的数。

实测比 Sol 1 快。

### CF566C

> 给定一棵 $n$ 个点的带权树，每个点住了 $w_i$ 个人，一个人从 $u$ 到 $v$ 的花费为距离的 $1.5$ 次方。
>
> 定义 $f(u)$ 表示所有人到点 $u$ 的总花费，求 $f(u)$ 最小的点。
>
> $n \le 2\cdot 10^5$

首先研究 $f(u)$ 有什么性质，假设花费等于距离的话 $f(u)$ 就是单峰的，因此猜想 $f(u)$ 是单蜂的。

证明：由于 $w_v\text{dis}^{1.5}_v(u)$ 是下凸函数，所以它们加起来也是下凸函数。

回忆实数上的单蜂函数是怎么求最值的：当前确定最优点在 $[l,r]$ 中，在 $\frac {l+r}2$ 处求导来确定最远点在 $\frac {l+r}2$ 左边还是右边，然后将范围减半。

考虑怎么在树上实现这个过程：求出整棵树的重心，通过确定最优解在重心的哪个子树来将范围减半。

怎么确定最优解在哪棵子树？把 $f(u)$ 的定义域扩大，$u$ 可以是一条边上的位置。求出重心向各个方向的导数，由于 $f(u)$ 单峰，所以最多有一个导数小于 $0$，这是最优解的方向。假设最优解的方向沿着边 $(u,v)$，由于 $f(u)$ 的最优点可能在 $(u,v)$ 上，所以 $v$ 不一定比 $u$ 优，应该把经过的所有点取个最小值作为答案。

根 $u$ 向儿子 $v$ 方向的导数为：
$$
\frac 32\left(\sum_{i=1}^nw_i\sqrt{\text{dis}(i,u)}-2\sum_{i\in \text{subree}(v)}w_i\sqrt{\text{dis}(i,u)}\right)
$$
可以 $O(n)$ 求出 $u$ 向每个儿子的导数，复杂度 $O(n\log n)$。

### CF1545C

### CF1149C

> 给定一个长度为 $2(n-1)$ 的合法括号序列，还有 $q$ 次交换操作（保证交换后合法）。
>
> 求每次交换后括号序列建出来的树的直径。
>
> $n,q \le 10^5$

容易想到直接维护树的形态，但发现很困难。

考虑括号序列的本质是什么，把 `(` 变成 $1$，`)` 变成 $-1$，再做一遍前缀和，就是树的欧拉序的深度序列，记这个序列为 $dep$。

然后问题变得清晰起来了，因为树上两点间的距离只和 $dep$ 有关。假设欧拉序上第 $a$ 个结点为 $u$，第 $b$ 个结点 $v$，满足 $a \le b$，那么

$$
\text{dis}(u,v)=dep_a+dep_b-2\min_{i=a}^bdep_i
$$

于是直径为

$$
\max_{i\le j\le k}dep_i+dep_k-2dep_j
$$

用线段树维护即可，复杂度 $O(n\log n)$。

### CF1566G

> 给定一张 $n$ 个点 $m$ 条边带权无向图，有 $q$ 次加边、删边操作。
>
> 每次操作后你需要找到四个两两不同的点 $a,b,c,d$，满足 $a,b$ 连通，$c,d$ 连通，且 $a,b$ 间最短路加 $c,d$ 间最短路是最小的。
>
> $n,m,q\le 10^9$

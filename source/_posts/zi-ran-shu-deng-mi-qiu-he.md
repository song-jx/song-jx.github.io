---
title: 自然数等幂求和
date: 2021-03-13 23:15:28
updated: 2021-03-13 23:15:28
tags: [知识总结,数论,自然数等幂求和]
categories: 算法
---
### <1> 拉格朗日插值

把 $1,2,\cdots,m+2$ 插值即可
$$
\sum_{i=1}^ni^m=\sum_{i=1}^{m+2}S_m(i)\prod_{j\ne i}\frac{n-j}{i-j}
$$
预处理
$$
pre_i=\prod_{j=1}^in-j,suf_i=\prod_{j=i}^{m+2}n-j
$$
和阶乘逆元。

进一步
$$
\sum_{i=1}^ni^m=\sum_{i=1}^{m+2}S_m(i)\frac {pre_{i-1}suf_{i+1}}{(i-1)!(m+2-i)!(-1)^{m+2-i}}
$$
复杂度 $O(m\log P)$ 或 $O(m)$（欧拉筛 $i^k$）。



### <2> 直接上公式(无需求逆)

$$
\sum_{i=1}^ni^{m+1}=\sum_{k=0}^m\binom{n+k+1}{m+2}\sum_{r=0}^k(-1)^r\binom{m+2}r(k+1-r)^{m+1}
$$

复杂度 $O(m^2)$。

### <3> 第二类斯特林数

预处理
$$
{0 \brace 0} = 1 \\
{n \brace m} = {n - 1 \brace m - 1} + m{n - 1 \brace m}
$$
结论
$$
\sum_{i=1}^mi^m=\sum_{i=1}^n{m \brace i}\frac 1{i+1}(n+1)^{\underline{i+1}}
$$
复杂度 $O(m^2)$ （无需求逆）或 $O(m\log m)$ （需要求第二类斯特林数-行）。

### <4> 递推

$$
f_i=\sum_{j=1}^nj^i\\
f_i=\frac 1{i+1}\left((n+1)^{i+1}-\sum_{j=0}^{i-1}\binom{i+1}jf_j\right)
$$

复杂度 $O(m^2)$。

### <5> 伯努利数

预处理
$$
B_0=1\\
\sum_{i=0}^m\binom{m+1}iB_i=0
$$
结论
$$
\sum_{i=1}^ni^m=\frac 1{m+1}\sum_{i=0}^m\binom{m+1}iB_in^{m+1-i}
$$
另外伯努利数的指数型生成函数
$$
B(x)=\frac x{e^x-1}
$$
通过多项式求逆

可以优化到 $O(m\log m)$。
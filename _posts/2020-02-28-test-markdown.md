---
layout: post
title: ST表
subtitle: ~~st创建的算法~~
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [OI]
comments: true
---
首先我们来看一个问题：

> 长度为N的序列，M次查询，每次查询区间最大值
>
> $N\leqslant 1e5 \ M\leqslant 1e6$
>
> （P3865）

这时候，$log(n)​$的线段树已经不够优秀了，我们的ST表就该出场了

做这道题，一个很自然的想法便是记录 f[i,j] 为 [i,j] 内的最大值，显然有转移方程 $f(i,j)=\max(f(i,j-1),a[j])​$

但是这样预处理是 $O(N^2)​$ 的，不能通过，我们需要进一步优化

我们可以采用倍增思想，是f[i,j]为从$a_i$开始，连续$2^j$个数的最大值，显然：

f(i,0)=$a_i​$

f(i,j)=max(f[i,j-1],f[i+$2^{j-1}$,j-1])

好了，现在我们回归正题：怎么处理询问呢？

我们可以把区间拆成两个相重叠的区间：

![](https://i.loli.net/2019/07/27/5d3bff1f490a472475.png)

询问区间长度为 len ，我们从左端点向右找一段长为 $2^{log\ len}$的区间（蓝色），右端点向左也找一段长为  $2^{log\ len}$的区间（黄色部分），显然这两段区间已经覆盖了整个区间（中间重叠了绿色），取最大值即可

为了保证询问复杂度为 O(1)，我们需要提前预处理出每个log len向下取整后的值

好了好了，上代码啦

```c++
#include <iostream>
#include <cstdio>
using namespace std;
int a[100010];
int LOG[100010];//LOG[i]为log i向下取整后的值
int maxn[100010][50];//刚刚说的f(i,j)
inline int read(){
   int s=0,w=1;
   char ch=getchar();
   while(ch<'0'||ch>'9'){
        if(ch=='-')w=-1;ch=getchar();
   }
   while(ch>='0'&&ch<='9') s=s*10+ch-'0',ch=getchar();
   return s*w;
}
int main(){
    int n,m;
    n=read();m=read();
    int i,j;
    LOG[0]=-1;
    for(i=1;i<=n;i++){
        a[i]=read();LOG[i]=LOG[i/2]+1;
    }
    for(i=1;i<=n;i++) maxn[i][0]=a[i];
    for(i=1;i<=LOG[n];i++)
    for(j=1;j+(1<<i)-1<=n;j++)
    maxn[j][i]=max(maxn[j][i-1],maxn[j+(1<<(i-1))][i-1]);
    int l=0,r=0;
    for(i=1;i<=m;i++){
        l=read();r=read();
        int len=LOG[r-l+1];
        printf("%d\n",max(maxn[l][len],maxn[r-(1<<(len))+1][len]));//1<<len就是2^len
    }
    return 0;
}

```

这道题很坑：必须用快读/scanf + printf不然O2 + iOS::sync_with_stdio(false)都救不了你

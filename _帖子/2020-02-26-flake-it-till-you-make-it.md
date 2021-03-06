---
layout: post
title: 普通莫队
subtitle: 如何优雅的暴力

tags: [OI]
---


论如何进行优雅的暴力

首先要学会分块

分块的思想是分块

把数组分为$\sqrt{N}​$分块修改

首先我们先求出块的数目bct = int( $\sqrt{N}$  )
然后用block数组记录某一个元素在第几块，实现：block[i] = (i - 1) / bct + 1 然后就完成了

----

修改

```c++
void update(int l, int r, int x) {
    for(int i = l; i <= min(r, block[l] * bct); ++i)//同一块的处理
        a[i] += x;
    if(block[l] != block[r]) {//跨块 
        for(int i = block[l] + 1; i <= block[r] - 1; ++i)
            lazy[i] += x;
        for(int i = (block[r] - 1) * bct + 1; i <= r; ++i)
            a[i] += x;  
    }
}
```

lazy记录的这一整块都加多少，大大降低了时间复杂度

----



单点查询

```c++
int query(int x) {
    return a[x] + lazy[block[x]];
}
```

----

整个代码

```c++
#include <iostream>
#include <cmath>
#include <cstring>
#include <algorithm>
using namespace std;
#define MAXN 50003
int n, bct, m;
int a[MAXN], lazy[MAXN], block[MAXN];
void update(int l, int r, int x) {
    for(int i = l; i <= min(r, block[l] * bct); ++i)
        a[i] += x;
    if(block[l] != block[r]) {
        for(int i = block[l] + 1; i <= block[r] - 1; ++i)
            lazy[i] += x;
        for(int i = (block[r] - 1) * bct + 1; i <= r; ++i)
            a[i] += x;  
    }
}
int query(int x) {
    return a[x] + lazy[block[x]];
}
int main() {
    while(cin>>n) {
        if(n == 0) break;
        memset(a, 0, sizeof a);
        memset(block, 0, sizeof block);
        memset(lazy, 0, sizeof lazy);
        bct = (int)sqrt(n);
        for(int i = 1; i <= n; ++i) block[i] = (i - 1) / bct + 1;
        int cmd, l, r, x;
        for(int i = 1; i <= n; ++i) {
            cin>>l>>r;
            update(l, r, 1);
        }
        for(int i = 1; i <= n; ++i) cout<<query(i)<<" ";
    }
    return 0;
}
```

-----

## 莫队通过分块可以跑的更快

### 按双关键字排序

第一比较级是左端点***所在的块***，第二比较级是右端点

----

# 莫队的思想

用两个指针L,R.他们对应的是所指的数的标号。这样我们可以利用这两个指针进行移动，每次只能向左或向右移动一步。

如图

![](C:\Users\华为\Desktop\tmp.png)

现在左右移动，比如L到L-1，只需要更新上一个新的3，即L-1。

那么L到L+1，我们只需要去除掉当前L的值。因为L+1是已经维护好了的。

R同理，但是要注意方向哦！R到R+1是更新，R到r-1是去除。

----

## 这么排序的原因

如果不是按照分块排序，那么一种直观的办法是按照左端点排序，再按照右端点排序。但是这样的表现不好。特别是面对精心设计的数据，这样方法表现得很差。

举个例子，有6个询问如下：(1, 100), (2, 2), (3, 99), (4, 4), (5, 102), (6, 7)。

这个数据已经按照左端点排序了。用上述方法处理时，左端点会移动6次，右端点会移动移动98+97+95+98+95=483次。

其实我们稍微改变一下询问处理的顺序就能做得更好：(2, 2), (4, 4), (6, 7), (5, 102), (3, 99), (1, 100)。

如果指针可以不往后移就不往后移

-----

#### 时间复杂度

最坏情况是O($n^{1.5}$) 

($n^{0.5}$=$\sqrt{n}$)

-----

# 代码实现

**add是先+/-再add,del是先del再+/-**

P3901 数列找不同

```c++
#include <iostream>
#include <cmath>
#include <algorithm>
using namespace std;
int X,ANS;//ANS是有几个相同的数
int a[100010],f[100010];
bool ans[100010];
struct ff{
    int l,r,x;
} x[100010];
bool cmp(ff a,ff b){//排序，更快
    if(a.l/X==b.l/X) return a.r<b.r;
    else return a.l/X<b.l/X;
}
void add(int k){
    if(++f[a[k]]==1) ANS++;
}
void del(int k){
    if(--f[a[k]]==0)  ANS--;
}
int main(){
    int n,m;
    cin>>n>>m;
    X=(int)sqrt(n);//分块思想
    int i;
    for(i=1;i<=n;i++) cin>>a[i];
    int L,R,l=0,r=0;
    for(i=1;i<=m;i++){
        cin>>x[i].l>>x[i].r;
        x[i].x=i;
    }
    sort(x+1,x+m+1,cmp);  
    for(i=1;i<=m;i++){
        L=x[i].l;R=x[i].r;
        while(l<L) del(l++);//移动“指针”
        while(l>L) add(--l);
        while(r<R) add(++r);
        while(r>R) del(r--);
        if(ANS==R-L+1) ans[x[i].x]=true;//标记
    }
    for(i=1;i<=m;i++){
        if(ans[i]) cout<<"Yes"<<endl;
        else cout<<"No"<<endl;
    }
    return 0;
}

```









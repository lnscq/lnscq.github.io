---
layout: article
---

此题总体思路为逆序对，可以使用归并排序，树状数组和线段树等算法。

**本文使用[归并排序](https://oi-wiki.org/basic/merge-sort/)的思路来解决逆序对。**

暴力算法时间复杂度为 $O(n^2)$。

期望得分 $48$ 分。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define N 100001
int a[N];
int n,l,c;
int main(){
	scanf("%d %d %d",&n,&l,&c);
	for(int i=1;i<=n;i++){
		scanf("%d",&a[i]);
	}
	sort(a+1,a+n+1);
	long long ans=0;
	for(int i=n;i>1;i--){
		for(int j=i-1;j>=1;j--){
			ans+=((a[i]-a[j])*l)/a[n]; 
		}
	}
	printf("%lld",ans);
	
}
```
首先我们考虑每头牛跑的圈数，则先需要求出所有牛可以跑步的最大时间 $t$。


> 某头牛跑完时，所有的牛都停下来。

因此只要速度最快的牛完成了赛段，则所有的牛都需要停下，用快速排序求出速度最大值 $V_{max}$。

不难求出 $t=\frac{l \times c}{V_{max}}$。

设第 $i$ 头奶牛跑了 $a_i$ 圈， $a_i =v_i\times (l\div V_{max})$ （数学公式可由上式推出）
。

我们对圈数进行排序，则绕圈数量为 


>$n=a_i -a_j=(v_i-v_j)\times l \div V_{max}$ $(1\leq i<j\leq n)$。


但注意一个**魔鬼细节**。

如果圈数为小数时则会多算一圈，举个例子如果 $a_i=3.6,a_j=2.9$ 时，这个式子就会多算一个 $1$，我们可以对 $a_i$ 的余数部分求出逆序对，可以将所有多算出来的部分求出。

**Q**：

为什么要求出余数部分，而不求小数部分？

**A**：

因为小数部分精度问题复杂，余数方便处理。


代码如下。

```cpp
#include<bits/stdc++.h>
#define int long long
using namespace std;
int n,l,c;
int v[114514];
int cpy[114514];
int guich[114514];
inline int read(){//快读
	int x=0,f=1;
	char c=getchar();
	while(c<'0'||c>'9'){
		if(c=='-'){
			f=-1;
			c=getchar();
		}
	}
	while(c>='0'&&c<='9'){
		x=(x<<3)+(x<<1)+(c^48),c=getchar();
	}
	return x*f;
}
int tmp[114514];
int ans=0,t=0,gary=0;
inline void merge(int l,int mid,int r){//归并板子
	int i=l,j=mid+1,t=l;
	while(i<=mid&&j<=r){
		if(cpy[i]>cpy[j]){
			ans+=mid-i+1;
			tmp[t++]=cpy[j++];
		}
		else tmp[t++]=cpy[i++];
	}
	while(i<=mid) tmp[t++]=cpy[i++];
	while(j<=r) tmp[t++]=cpy[j++];
	for(int i=l;i<=r;i++) cpy[i]=tmp[i];
}
inline void mergesort(int l,int r){
	if(l>=r) return ;
	int mid=l+(r-l)/2;
	mergesort(l,mid);
	mergesort(mid+1,r);
	merge(l,mid,r);
//	return ans;
}
int v_max;
signed main(){//使用了#define int long long 所以将int修改为signed
	n=read();
	l=read();
	c=read();
	for(register int i=1;i<=n;i++){
		v[i]=read();
		v_max=max(v_max,v[i]);
	}
	sort(v+1,v+1+n);
	for(register int i=1;i<=n;i++){
		cpy[i]=(l*v[i])%v_max;//余数部分
		guich[i]=(l*v[i])/v_max;//整数部分
	}
	for(register int i=1; i<=n; i++) {
		gary+=(i-1)*guich[i]-t;//计算未处理前的值
		t=t+guich[i];//计算所有多加的值
	} 
	mergesort(1,n);
	gary-=ans;
	printf("%lld\n",gary);
	return 0;
}
```




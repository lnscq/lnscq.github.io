---
layout: article
---

[题目链接](https://www.luogu.com.cn/problem/P9124)
#### - 题意简述

1. 奶牛需要为他的朋友制作糕点（$a_i$个$A$和$b_i$个$B$）。每位朋友最多等待$c_i$的时间，否则就会伤心的离开。

2. 制作一个饼干需要$t_c$时间,一个松糕需要$t_m$的时间。

3. 奶牛可以使用钞能力升级他的烤箱，每使用$1￥$就可以使制作饼干$or$松糕的时间减少$1$单位时间,不妨设饼干降低$x$时间，松糕降低$y
$的时间
4. 求出最少花费多少$money$使得所有的朋友都可以得到满足。
#### - 解题思路

1. 因为要找到最少的花费首先考虑就是二分或者$dp$，显然它并不存在后效性的问题，因此考虑使用二分。

2. 考虑二分$x$还是$y$,显然无论是二分$x$是$y$,都会存在一个降低另一个升高的情况(因为$x$和$y$都不具备单调性），所以我们不妨对$x+y$进行二分。

#### - 数学过程

设饼干降低了$x$个时间，松糕降低了$y$个时间

显然我们要求的不等式为

 $a_i \times t_c +b_i \times t_m \leq c_i$

 使用钞能力后变为
 
 $a_i \times (t_c-x) +b_i \times (t_m-y) \leq c_i$
 
 设$mid=x+y$则存在
 
 $a_i\times t_c-a_i\times x+b_i\times t_m-b_i\times mid+b_i \times x\leq c_i$
 
 $(b_i-a_i)\times x\leq c_i-a_i\times t_c-b_i\times t_m+b_i\times mid$

 令$k_i=c_i-a_i\times t_c-b_i\times t_m+b_i\times mid$
 
 得到$(b_i-a_i)\times x \leq k_i$
 
 所以我们就可以根据上述的不等式进行二分答案了
 
 接下来我们讨论下不等式的解集
 
 $$
 \left\{
 \begin{aligned}
 &x= \varnothing  \qquad\qquad\quad  b_i-a_i=0\\ 
 &x\leq  \lfloor\frac{k_i}{b_i-a_i}\rfloor\qquad b_i-a_i>0\\
 &x\geq \lceil \frac{k_i}{b_i-a_i}\rceil\qquad  b_i-a_i<0\\
 \end{aligned}
 \right.
 $$
 
#### code

```cpp
#include<bits/stdc++.h>
#define maxn 110
#define tm danny_scq
using namespace std;
long long a[maxn],b[maxn],c[maxn];
long long T,n,tc,tm;
bool check(long long x)
{
	long long maxx=min(tc-1,x);
	long long minn=max((long long)0,x-tm+1);
	for(int i=1; i<=n; i++){
		long long k=c[i]-a[i]*tc-b[i]*tm+b[i]*x;
		if(b[i]-a[i]==0){	
			if(k<0) return false;
		}
		else if(b[i]-a[i]>0) maxx=min(maxx,(long long)floor(k*1.0/(b[i]-a[i])));
		else minn=max(minn,(long long)ceil(k*1.0/(b[i]-a[i])));
	}
	return maxx>=minn;
}
int main(){
	cin>>T;
	while(T--){
		cin>>n>>tc>>tm;
		for(int i=1;i<=n;i++){
			cin>>a[i]>>b[i]>>c[i];
		}
		long long l=-1,r=tc+tm;
		while(l+1<r)
		{
			long long mid=l+r>>1;
			if(check(mid)) r=mid;
			else l=mid;
		}
		cout<<r<<endl;
	}
	return 0;
}
```

 
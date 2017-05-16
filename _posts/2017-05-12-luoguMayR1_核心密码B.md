---
layout: post
title: "Luogu_MayR1 D 核心密码B"
date: 2017-05-12
description: "luogu_MayR1"
tag: 微积分
---

### **Description**：
令g(n)表示n能表示成几种不同的完全k次方数（k>1），求$f(n) = \sum_{i=2}^{n} \frac{g(i)}{i}$

例如，$64=2^6=4^3=8^2$，所以g(64)=3。

### **Inut**：

多组询问，第一行一个整数T表示询问组数。

接下来T行，每行一个整数n，表示询问$f(n)$。

### **Output**：

T行，每行一个实数，表示f(n)，保留十四位小数。

由于精度误差，你的答案和标准答案差的绝对值在$2×10^{-14}$以内即可通过

### **Hint**：

对于20%的数据$n <= 1000$。

对于40%的数据$n <= 10^6，T <= 5$

对于100%的数据$2<=n<=10^{18}，1 <= T <= 50000$。

### **Analysis**：

1.
　　首先是一个小小的转化，发现直接求似乎不可行，所以我们改求每个数的贡献，我们可以发现，求的的东西就是$\sum_{i=2}^{\sqrt{n}}\sum_{y=2}^{trunc(n^{\frac{1}{x}})}$$\frac{1}{i^y}$

2.
　　然后我们预处理前10w项（因为其精度误差较大）
　　（经实验，似乎只有当y=2或3的时候项数超过了10w）
　　
　　然后比较容易想到近似处理，就是用积分了：
　　
　　我们把每一项换为：$\int_{i-0.5}^{i+0.5} \frac{1}{x^y} {\rm d}x$（就是x的取值从(i-0.5)到(i+0.5)），所以说我们只需计算$\int_{max-0.5}^{min+0.5} \frac{1}{x^y} {\rm d}x$即可(max和min是上限和下限，合并过程是定积分的基本运算法则)，枚举y，就可以很快地计算了。

**关于误差分析：**
　　仅作一点简单分析（本问题的出题人说精度本来就有点问题），在此仅用y=2做一点分析（即使我们知道y=2造成的误差是所有不同y值中最小的）
　　
　　首先我们可以发现$\int_{i-0.5}^{i+0.5} \frac{1}{x^2} {\rm d}x =  \frac{1}{i^2-0.25} >  \frac{1}{i^2}$，然后算和，就是要算$\sum_{i=min+1}^{max} \frac{1}{i^2-0.25} -\frac{1}{i^2}$，然后这个需要用积分来求（然而并不知道怎么求），会发现这个数非常小，大约为$8×10^{-17}$，然后就可以了。

### **Thought**:
　　关于积分还是觉得有些生疏，不过“**对答案的贡献**”这种思想还是很好的，很多题目需要这样的转化

　　还有一点就是当精度要求很高的时候要用long double，否则很可能被卡精度

### **Code**：

	    
	#include<cstdio>
	#include<cstdlib>
	#include<cstring>
	#include<cmath>
	#include<iostream>
	#include<algorithm>
	
	using namespace std;
	
	#define LL long long
	#define Double long double
	#define For(i, a, b) for(int i = (a); i <= (int)(b); ++i)
	
	#define MK (60+5)
	#define MN (100000+5)
	
	inline Double Pow(Double a, int Ti){
	    Double ret = 1.0;
	    while(Ti){
	        if(Ti&1) ret *= a;
	        a *= a; Ti >>= 1;
	    }
	    return ret;
	}
	
	inline Double Calc(Double st, Double ed, int k){
	    return ((1.0/Pow(ed, k-1))-(1.0/Pow(st, k-1)))/(1.0-1.0*k);
	}
	
	Double Sum[MK][MN];
	
	int main(){
	#ifndef ONLINE_JUDGE
	    freopen("test.in", "r", stdin);
	    freopen("test.out", "w", stdout);
	#endif
	
	    int T, nk, MAX = 100000;
	    LL n, st, ed;
	    Double ans;
	
	    For(k, 2, 60){
	        ed = pow(1000000000000000000, 1.0/k);
	        For(j, 2, MAX){
	            Sum[k][j] = Sum[k][j-1];
	            if(j <= ed) Sum[k][j] += 1.0/Pow(j*1.0, k);
	        }
	    }
	
	    scanf("%d", &T);
	    while(T--){
	        scanf("%lld", &n);
	        if(n < 4){puts("0"); continue;}
	
	        ans = 0.0; nk = 1;
	        for(LL i = 4; i <= n; i <<= 1){
	            ++nk;     
	            ed = pow(n, 1.0/nk);
	
	            if(ed <= 1ll*MAX){
	                ans += Sum[nk][ed]; continue;
	            }
	            ans += Sum[nk][MAX];
	            st = MAX+1; if(ed < st) continue;
	            
	            ans += Calc(1.0*st-0.5, 1.0*ed+0.5, nk);
	        }
	
	        printf("%.14lf\n", (double)ans);
	    }
	
	    return 0;
	}

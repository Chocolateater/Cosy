---
title: 题解/学习笔记]
date: 2024-10-03 10:32:11
use: katex, twikoo
---
# 完美匹配 题解/学习笔记

非常好状压，使我的大脑旋转。

[题面](https://iai.sh.cn/problem/824) 图片格式的题面在文末。

### 题意

$$2*N$$ 个点，分为两组。

同一个组内的点没友有边相连。~~人话：二分图~~

给出两组点之间的相连状况。

求将两组点一一配对方案数。

### 数据范围

- 对于 $$60\%$$ 的数据，$$N\leq15$$

- 对于 $$100\%$$ 的数据，$$N\leq22$$

  

  一眼状压

### 暴搜

考虑递归。

从第 $1$ 个点开始枚举，将这个点所有可能匹配递归一遍。时间复杂度 $$\text{O}(N!)$$，只能拿到 $$\mathtt{60 pts}$$ 。

```cpp
#include <bits/stdc++.h>
#define mod 1000000007
#define int long long
using namespace std;
int n;
bool vis[30];
int a[30][30];
int dfs(int t){
	if(t==n+1){
		return 1;
	}
	else{
		int sum=0;
		for(int i=1;i<=n;i++){
			if(a[t][i]&&!vis[i]){
				vis[i]=true;//将 t 与 i 匹配
				sum+=dfs(t+1);
				vis[i]=false;//重置
			}
		}
		return sum % mod;
	}
}
signed main(){
	cin>>n;
	for(int i=1;i<=n;i++){
		for(int j=1;j<=n;j++){
			cin>>a[i][j];
		} 
	}
	cout<<dfs(1);
	return 0;
} 
```



### 逝着将函数纯化

为记忆化做准备。

> 纯化函数：将函数化为纯函数。
>
> 纯函数：无论外部变量如何变化，同一参数返回的结果永远一致的函数。

需要将 ``vis`` 数组作为递归函数（``dfs``）的参数传递。

显然不可能直接传数组。

布尔数组 $$→$$ 一个二进制串 $$→$$ 十进制数字

这里不过多解释，请自行阅读状压 $$\mathtt{DP}$$ 入门教程。

空间复杂度 $$2^{22}=4194304$$ 完全可以接受。

``dfs`` 函数的参数 ``s`` 二进制下的第 $i$ 位表示第 $i$ 个点是否已被选择（或者说是占用）。

```cpp
#include <bits/stdc++.h>
#define mod 1000000007
#define int long long
using namespace std;

int getbit(int a,int b){ // 获取 a 的第 b 位，最低位编号为 0
	return (a>>b)&1; 
}
int setbit(int a,int b){// 将 a 的第 b 位设置为 1 ，最低位编号为 0
	return a|(1<<b); 
}

int n;
int a[30][30];

int dfs(int t,int s){
	if(t==n+1){
		return 1;
	}
	else{
		int sum=0;
		for(int i=1;i<=n;i++){
			if(a[t][i]&&!getbit(s,i-1)){
				sum+=dfs(t+1,setbit(s,i-1));
			}
		}
		return sum % mod;
	}
}
signed main(){
	cin>>n;
	for(int i=1;i<=n;i++){
		for(int j=1;j<=n;j++){
			cin>>a[i][j];
		} 
	}
	cout<<dfs(1,0);
	return 0;
} 
```

### 添加记忆化

得益于纯函数的特性，程序不必重复计算同一参数下 ``dfs`` 函数返回的值。

添加两个数组即可。

布尔类型的 ``vis[i][j]`` 数组表示是否已经计算过 ``dfs(i,j)`` 的值。

整数类型的 ``mem[i][j]`` 数组表示已经计算过的 ``dfs(i,j)`` 的值。

空间复杂度 $$2^{22}$$ ，可以接受。

> 为什么空间复杂度不是 $$N*2^{22}$$ ？
>
> 事实上，对于一个固定的 ``s``，无论现在正在处理的点（``t``）究竟是 $1$ 还是 $1e9$ 都没有差别。
>
> 至于为什么，只需略加思考可得，对于不同的状态 ``s`` ，``t`` 是绝对不可能相同的。

```cpp
#include <bits/stdc++.h>
#define mod 1000000007
#define int long long
using namespace std;

int getbit(int a,int b){ // 获取 a 的第 b 位，最低位编号为 0
	return (a>>b)&1; 
}
int setbit(int a,int b){// 将 a 的第 b 位设置为 1 ，最低位编号为 0
	return a|(1<<b); 
}

int n;
int a[30][30];
int mem[5195304];
bool vis[5195304];

int dfs(int t,int s){
	if(t==n+1){
		return 1;
	}
	else{
		if(vis[s]){
			return mem[s];
		}
		vis[s]=true;
		int sum=0;
		for(int i=1;i<=n;i++){
			if(a[t][i]&&!getbit(s,i-1)){
				sum+=dfs(t+1,setbit(s,i-1));
			}
		}
		return mem[s] = sum % mod;
	}
}
signed main(){
	cin>>n;
	for(int i=1;i<=n;i++){
		for(int j=1;j<=n;j++){
			cin>>a[i][j];
		} 
	}
	cout<<dfs(1,0);
	return 0;
}
```



### 题面

![](https://chocolateater.github.io/PicHub/202410022225368.png)

### 样例

#### #1

##### Input

```
4
1 1 1 1
1 1 1 1
1 1 1 1
1 1 1 1
```

##### Output

```
24
```

##### Explain

```
4! = 24
```

#### #2

##### Input

```
3
1 0 0
0 1 0
0 0 1
```

##### Output

```
1
```

##### Explain

```
有一组完美匹配
```

#### #3

##### Input

```
12
1 0 0 0 0 0 0 1 1 1 1 0
0 1 0 0 0 0 1 0 0 0 0 0
0 0 1 0 0 0 0 1 0 0 0 0
0 0 0 1 0 0 0 0 1 0 0 0
0 0 0 0 1 0 0 0 0 1 0 0
0 0 0 0 0 1 0 0 0 0 0 1
0 1 0 0 0 0 1 0 0 0 0 0
1 0 1 0 0 0 0 1 0 0 0 0
1 0 0 1 0 0 0 0 1 0 0 0
1 0 0 0 1 0 0 0 0 1 0 0
1 0 0 0 0 0 0 0 0 0 1 0
0 0 0 0 0 1 0 0 0 0 0 1
```

##### Output

```
112
```


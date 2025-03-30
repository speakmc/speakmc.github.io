# c++集
## 1.头文件
```c++
#include<bits/stdc++.h>
using namespace std;
int main(){
	//freopen("1.in.txt","r",stdin);
	//freopen("1.out.txt","w",stdout);

	return 0;
}
```

<br>

## 2.求最大公因数（GCD）与最小公倍数（LCM）
```c++
//GCD函数递归法
 
int gcd(int a,int b){
    if(b==0) return a;
    return gcd(b,a%b);
}
 
//GCD异或法
 
int gcd(int a,int b){
    while(a^=b^=a^=b%=a);
    return b;
}
 
//求最小公倍数（lcm）即两数乘积除以最大公因数（gcd）
int lcm(int a,int b){
    return a*b/gcd(a,b);
}
```

<br>

## 3.求质数
#### 常规判断质数——isprime函数
```c++
//常规素数判断
int isprime(int x)
{
	if(x==1||x%2==0&&x!=2)return 0;
	for(int i=3;i<=sqrt(x);i+=2)//注意要等于sqrt(x)
	{
		if(x%i==0) return 0;
	}
	return 1;
}
```
#### 线性质数筛——欧拉筛
```c++
//原理：通过标记一个数的倍数，后进行遍历，没被标记的则为质数
 
int n=1e6;//假设求1e6内所有质数
bool a[N];//用于标记是否被筛除
int prime[N],pi=0;//prime用于存放素数，pi为prime数组的专属下标
 
void Eulerprime(int n){
    for(int i=2;i<=n;++i){
	    if(!a[i]) prime[++pi]=i; //++为先加一再用，先记录2，后记录质数
	    for(int j=1;prime[j]*i<=n;++j){
		    a[prime[j]*i]=1;//标记合法范围内，某一较小质数的所有倍数
		    if(i%prime[j]==0) break;//当取模为0的时候，则表示已经为较小的质数，则break
	    }
    }
}
```

<br>

## 4.排序
此处提供两个高效排序法，但是一般排序用sort就行。
#### ①快速排序：平均时间复杂度O(n log n)，最坏情况下为O(n²)
```c++
void quick_sort(int *a,int left,int right)
{
	int key,l=left,r=right;
	key=a[(l+r)/2];
	while(l<=r)
	{
		while(a[l]<key)l++;
		while(a[r]>key)r--;
		if(l<=r)
		{
			swap(a[l],a[r]);
			l++;r--; 
		}
	}
	if(left<r)  quick_sort(a,left,r);
	if(right>l) quick_sort(a,l,right);
}
```
#### ②归并排序：时间复杂度均为O(n log n)。
```c++
void msort(int *a,int *b,int l,int r){
    if(l>=r) return;
    int mid=(l+r)>>1;
    msort(a,b,l,mid);
    msort(a,b,mid+1,r);
    int i=l,j=mid+1,k=l;
    while(i<=mid&&j<=r){
        if(a[i]>a[j]) b[k++]=a[j++];
        else b[k++]=a[i++];
    }
    while(i<=mid) b[k++]=a[i++];
    while(j<=r) b[k++]=a[j++];
    for(int i=l;i<=r;i++) a[i]=b[i];
}
```

<br>

## 5.快速幂
快速幂板子提供两种版本。
#### 常规运算版
```c++
long long Mod=1e9+7;
long long fastPower(long long base, long long power) {
    long long result = 1;
    while (power > 0) {
        if (power % 2 == 1) {
            result = result * base % Mod;
        }
        power = power / 2;
        base = (base * base) % Mod;
    }
    return result;
}
```
#### 位运算版
```c++
long long fastPower(long long base, long long power) {
    long long result = 1;
    while (power > 0) {
        if (power & 1) {//此处等价于if(power%2==1)
            result = result * base % Mod;
        }
        power >>= 1;//此处等价于power=power/2
        base = (base * base) % Mod;
    }
    return result;
}
```

<br>

## 6.背包问题动态规划
#### 01背包（物品有限个）
```c++
for(int i=1;i<=n;i++){
	for(int j=m;j>=w[i];j--){
		dp[j]=max(dp[j],dp[j-w[i]]+v[i]);//状态转移方程
	}
}
```
#### 完全背包（物品无限个）
```c++
for(int i=1;i<=m;i++){
        for(int j=w[i];j<=t;j++){//此时则为正序遍历
            dp[j]=max(dp[j],(dp[j-w[i]]+v[i]));
        }
    }
```

<br>

## 7.搜索
#### 迷宫寻路（回溯法+DFS）
```c++

//模板题：洛谷B3625 迷宫寻路
 
int a[110][110],v[110][110];//a数组记录地图，v数组记录是否走过
int n,m;
 
//dx，dy两个数组用于表示走的方向
int dx[4]={0,1,0,-1};
int dy[4]={1,0,-1,0};
int flag=0;//flag用于标记可以到达
 
 
void dfs(int x,int y){
        if(x<1||y<1||x>n||y>m||v[x][y]==1||a[x][y]==1) return;//防止越界、走重复、走到障碍物上
 
        //找到就返回
        if(x==n&&y==m){
            flag=1;return;
        }
        //走过就标记
        v[x][y]=1;
        //四个方向试探
        for(int i=0;i<=3;i++){
            int tx=x+dx[i];
            int ty=y+dy[i];
            dfs(tx,ty);
        }
}
void solve(){
	cin>>n>>m;
	char x;
	for(int i=1;i<=n;i++){
		for(int j=1;j<=m;j++){
			cin>>x;
			if(x=='#') a[i][j]=1;
		}
	}
    //从起点开始
	dfs(1,1);
 
    //判断是否能到达
	if(flag==1) cout<<"Yes"<<endl;
	else cout<<"No"<<endl;
}
```
#### 查找连通块（DFS）
```c++

//洛谷P1596 Lake Counting S
 
//定义8个方向
int dx[8]={1,0,1,0,-1,-1,1,-1};
int dy[8]={1,1,0,-1,0,-1,-1,1};
int ans=0;
 
//遇到联通的就试探
void dfs(int x,int y){
	v[x][y]=1;
	int tx,ty;
	for(int i=0;i<=7;i++){
		tx=x+dx[i];
		ty=y+dy[i];
		if(x>n||y>m||x<1||y<1){
			continue;//越界就跳过
		}
		else if(a[tx][ty]==1&&v[tx][ty]==0){
			dfs(tx,ty);
		}
	}
}
void solve(){
	cin>>n>>m;
	char x;
	for(int i=1;i<=n;i++){
		for(int j=1;j<=m;j++){
			cin>>x;
			if(x=='W') a[i][j]=1;
		}
	}
	for(int i=1;i<=n;i++){
		for(int j=1;j<=m;j++){
			if(a[i][j]==1&&v[i][j]==0){
				dfs(i,j);//开始递归
				ans++;//每递归完成一次表明有一个连通块
			}
		}
	}
	cout<<ans<<endl;
}
```

<br>

## 8.高精度
#### 高精度加减法：模拟竖式
```c++
lc=(la>lb)?la:lb;
for(i=0;i<=lc;i++){
	c[i]=a[i]+b[i]+n;
	n=c[i]/10;
	c[i]%=10;
}
```
#### 高精度乘除：分步取模
>大数相乘或者相除记得有个公式：(a * b) % p = (a % p * b % p) % p
```c++
//例：判断是否能被能被603整除
    for(int i=0;i<n;i++){
        mod=(mod*10+(s[i]-'0'))%603;
    }  
    if(mod==0){
        cout<<"YES"<<endl;
    }else{
        cout<<"NO"<<endl;
    }
```

<br>

## 9.十进制数转二进制数
```c++
int f(int x){
    if(x>1) return f(x/2)*10+x%2;
    else return x%2;
}
```

<br>

## 10.求对数
>cmath库中有专门的函数可以求对数
```c++
log() //以e为底的对数
log10() //以10为底的对数
```
>但是要表示如以3为底的对数呢？答案是换底公式！
```c++
double log3(double x){
	return log(x)/log(3);
}
```

<br>

## 11.并查集
>使用并查集之前，记得初始化祖宗节点数组：
```c++
int fa[N];//父节点数组，记录每个点的父节点
 
//父节点的初始化，初始状态全部为其本身（尚未合并）
void begin(int n){
	for(int i=1;i<=n;i++){
		fa[i]=i;
	}
}
```
#### Find
```c++
int find(int i){
	if(i!=fa[i]) fa[i]=find(fa[i]);//路径压缩
    return fa[i];
}
```
#### Union
```c++
void Union(int a,int b){
	fa[find(a)]=find(b);
}
```


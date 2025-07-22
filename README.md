# 01背包





## 二维数组

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010; // N: 最大物品个数和背包容量

int n, m; // n: 物品个数， m: 背包容量
int v[N], w[N]; // v[i]: 第 i 个物品的体积， w[i]: 第 i 个物品的价值
int f[N][N]; // f[i][j]: 前 i 个物品放入容量为 j 的背包的最大价值

int main(){
    cin >> n >> m;

    for(int i=1; i<=n; i++) cin >> v[i] >> w[i]; // i: 物品编号，从 1 开始

    for(int i=1; i<=n; i++){ // 遍历物品
        for(int j=0; j<=m; j++){ // j: 背包容量
            
            // 如果不放第 i 个物品，最大价值为 f[i-1][j]
            // 初始化：如果不放第 i 个物品，最大价值为 f[i-1][j]
            f[i][j]=f[i-1][j];
            
            // 状态转移方程：f[i][j] = max(f[i-1][j], f[i-1][j-v[i]] + w[i])
            // 如果放第 i 个物品，最大价值为 f[i-1][j-v[i]] + w[i]（前提是 j >= v[i]）
            // 注意：这里的 j-v[i] 需要保证 j >= v[i]，否则不能放第 i 个物品
            if(j>=v[i]) f[i][j]=max(f[i][j], f[i-1][j-v[i]]+w[i]);
        }
    }

    cout << f[n][m] << endl;

    return 0;
}

```



## 优化：一维数组

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010; 

int n, m; 
int v[N], w[N]; 
int f[N]; 

int main(){
    cin >> n >> m;

    for(int i=1; i<=n; i++) cin >> v[i] >> w[i]; 

    for(int i=1; i<=n; i++)
        for(int j=m; j>=v[i]; j--)
            f[j]=max(f[j], f[j-v[i]]+w[i]);
        
    cout << f[m] << endl;

    return 0;
}

```



# 完全背包



## 朴素：二维数组 三重循环

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N];
int f[N][N];

int main(){
    cin >> n >> m;

    for(int i=1; i<=n; i++) cin >> v[i] >> w[i];

    for(int i=1; i<=n; i++)
        for(int j=0; j<=m; j++)
            for(int k=0; k * v[i] <= j; k++)
                f[i][j] = max(f[i][j], f[i-1][j - k * v[i]] + w[i] * k);

    cout << f[n][m] << endl;
    
    return 0;
}
```



## 优化：二维数组 两重循环 --- 和01背包一样

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N];
int f[N][N];

int main(){
    cin >> n >> m;

    for(int i=1; i<=n; i++) cin >> v[i] >> w[i];

    for(int i=1; i<=n; i++)
        for(int j=0; j<=m; j++){
            f[i][j] = f[i-1][j];
            if(j>=v[i]) f[i][j] = max(f[i][j], f[i][j-v[i]] + w[i]);
        }

    cout << f[n][m] << endl;
    
    return 0;
}
```



## 终极：一维数组

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N];
int f[N];

int main(){
    cin >> n >> m;

    for(int i=1; i<=n; i++) cin >> v[i] >> w[i];

    for(int i=1; i<=n; i++)
        for(int j=v[i]; j<=m; j++){ // 这里跟01不同
            f[j] = max(f[j], f[j-v[i]] + w[i]);
        }

    cout << f[m] << endl;
    
    return 0;
}
```





# 多重背包



## 二维数组 三重循环

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 110;

int n, m;
int v[N], w[N], s[N];
int f[N][N];

int main(){
    cin >> n >> m;

    for(int i=1; i<=n; i++) cin >> v[i] >> w[i] >> s[i];

    for(int i=1; i<=n; i++)
        for(int j=0; j<=m; j++)
            for(int k=0; k <= s[i] && k * v[i] <= j; k++)
                f[i][j] = max(f[i][j], f[i - 1][j - k * v[i]] + k * w[i]);
        
    cout << f[n][m] << endl;
    
    return 0;
}
```



## 二进制优化

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 25000, M = 1010;

int n, m;
int v[N], w[N];
int f[N];

int main(){
    cin >> n >> m;

    int cnt=0; //本物品的编号

    for(int i=1; i<=n; i++){
        int a, b, s; // a体积，b价值，s个数
        cin >> a >> b >> s;
        int k=1; // k = 1, 2, 4, 8, 16, 32,..个物品打包在一起
        while(k<=s){
            cnt++; //cnt = 1, 2, 3,..
            v[cnt] = a * k; //打包在一起的物品的体积
            w[cnt] = b * k; //打包在一起的物品的价值
            s -= k;
            k *= 2;
        }
	   // s还剩一些需要补上
        if(s > 0) {
            cnt++;
            v[cnt] = a * s;
            w[cnt] = b * s;
        }
    }

    n = cnt;

    // 与01背包一样	
    for(int i=1; i<=n; i++)
        for(int j=m; j>=v[i]; j--)
            f[j] = max(f[j], f[j-v[i]] + w[i]);
        
    cout << f[m] << endl;
    
    return 0;
}

```



# 分组背包

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 110;

int n, m;
int v[N][N], w[N][N], s[N];
int f[N];

int main(){
    cin >> n >> m;

    for(int i=1; i<=n; i++){
        cin >> s[i];
        for(int j=1; j<=s[i]; j++)
            cin >> v[i][j] >>w[i][j];
    }

    for(int i=1; i<=n; i++)
        for(int j=m; j>=0; j--) // 从大到小枚举所有体积
            for(int k=0; k<s[i]; k++) // 枚举所有选择
                if(v[i][k]<=j) f[j] = max(f[j], f[j-v[i][k]] + w[i][k]);

    cout << f[m] << endl;

    return 0;
}

```


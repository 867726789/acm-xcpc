# RMQ

```cpp
#include <iostream>
#include <cstdio>
#include <cmath>

using namespace std;
const int N = 1e3+5;

int a[N]; 
int dp[N][20];
int Log[N]

void Log_init(int n)
{
    Log[0]=-1;
	for(int i=1;i<=n;i++)Log[i]=Log[i>>1]+1;
}

void ST_init(int n) {
    for(int i = 0; i < n; i++) {
        dp[i][0] = a[i];
    }
    int nlen = Log[n];    //计算区间可倍增区间的最大次方
    for(int j = 1; j <= nlen; j++) {    
        for(int i = 0; i < n; i++) {
            dp[i][j] = min(dp[i][j - 1], dp[i + (1 << (j - 1))][j - 1]); //将当前区间分成两个子区间求其最小值
        }
    }
}

int ST_query(int l, int r) {
    int k = Log[r-l+1];    //找到当前区间最大的倍增长度
    return min(dp[l][k], dp[r - (1 << k) + 1][k]);    
}

int main() {
    int n, m;
    cin >> n >> m;
    for(int i = 0; i < n; i++) {
        scanf("%d", &a[i]);
    }
    Log_init(n);
    ST_init(n);
    while(m--) {
        int l, r;
        cin >> l >> r;
        cout << ST_query(l, r) << endl;
    }
    return 0;
}
```


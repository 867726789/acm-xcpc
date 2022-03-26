# Tarjan

## LCA

```cpp
int fa[N];//并查集
void tarjan(int x)
{
    v[x] = 1;
    for(int i = head[x]; ~i ; i = nxt[i])
    {
        int y = to[i];
        if(v[y])
            continue;
        d[y] = d[x] + w[i];
        tarjan(y);
        fa[y] = x;
    }
    for(int i = 0 ; i < query[x].size() ; i ++)
    {
        int y = query[x][i].pos, id = query[x][i].id;
        if(v[y] == 2)
        {
            int lca = get(y);
            ans[id] = min(ans[id] , d[x] + d[y] - 2 * d[lca]);
        }
    }
    v[x] = 2;
}
```

## 无向图

### 割边

```cpp
void tarjan(int x,int in_edge)
{
    dfn[x] = low[x] = ++num;
    for(int i = head[x] ; i ; i = nxt[i])
    {
        int y = to[i];
        if(!dfn[y])
        {
            tarjan(y,i);
            low[x] = min(low[x],low[y]);
            if(low[y] > dfn[x])
                bridge[i] = bridge[i ^ 1] = true;
        }
        else if(i != (in_edge^1))
            low[x] = min(low[x],dfn[y]);
    }
}

int main()
{
    tot = 1;
    for(int i = 1 ; i <= n ;i ++)
        if(!dfn[i]) tarjan(i,0);
    for(int i = 2 ; i < tot ; i += 2)
        if(bridge[i])
            printf("%d %d",to[i],to[i^1]);
}
```

### 割点

```cpp

void tarjan(int x)
{
    dfn[x] = low[x] = ++num;
    int flag = 0;
    for(int i = head[x] ; i ; i = nxt[i])
    {
        int y = to[i];
        if(!dfn[y])
        {
            tarjan(y);
            low[x] = min(low[x],low[y]);
            if(low[y] >= dfn[x])
            {
                flag ++;
                if(x != root || flag > 1)
                    cut[x] = true;
            }
        }
        else
            low[x] = min(low[x],dfn[y]);
    }
}


int main()
{
    tot = 1;
    for(int i = 1 ; i <= n ;i ++)
        if(!dfn[i]) root = i , tarjan(i);
    for(int i = 1 ; i <= n ; i ++)
        if(cut[i])
            printf("%d ",i);
}
```

### 边双连通分量

```cpp
求:
int c[N],dcc;
void dfs(int x)
{
    c[x] = dcc;
    for(int i = head[x] ; i ; i = nxt[i])
    {
        int y =to[i];
        if(c[y] || bridge[i])continue;
        dfs(y);
    }
}
int main()
{
    for(int i = 1 ; i <= n ; i ++) 
        if(!c[i])
        {
            ++dcc;
            dfs(i);
        }
}

缩点:
int hc[N],tc[N<<1],nc[N<<1],tc;
void add_c(int x,int y)
{
    tc[++tc] = y , nc[tc] = hc[x] , hc[x] = tc;
}

int main()
{
    tc = 1;
    for(int i = 2 ; i <= tot ; i ++)
    {
        int x = to[i^1] , y = to[i];
        if(c[x] == c[y])continue;
        add_c(c[x],c[y]);
    }
}
```

### 点双连通分量

```cpp
求：
void tarjan(int x)
{
    dfn[x] = low[x] = ++num;
    stack[++top] = x;
    if(x == root && head[x] == 0)//孤立点
    {
        dcc[++cnt].push_back(x);
        return;
    }
    int flag = 0;
    for(int i = head[x] ; i ; i = nxt[i])
    {
        int y = to[i];
        if(!dfn[y])
        {
            tarjan(y);
            low[x] = min(low[x],low[y]);
            if(low[y] >= dfn[x])
            {
                flag ++;
                if(x != root || flag > 1)
                    cut[x] = true;
                cnt++;
                int tmp;
                do{
                    tmp = stack[top--];
                    dcc[cnt].push_back(tmp);
                }while(tmp != y);
                dcc[cnt].push_back(x);
            }
        }
        else
            low[x] = min(low[x],dfn[y]);
    }
}

缩点：
int main()
{
    num = cnt;
    for(int  i = 1 ; i <= n ; i++)
        if(cut[i])
            new_id[i] = ++num;
    tc = 1;
    for(int i = 1 ; i <= cnt ; i ++)
    {
        for(int j = 0 ; j < dcc[i].size() ; j ++)
        {
            int x = dcc[i][j];
            if(cut[x])
            {
                add_c(i,new_id[x]);
                add_c(new_id[x],i);
            }
        }
        else
            c[x] = i ;
    }
}


```

## 广义圆方树

仅存在圆方连边的点双缩点图，仅由一条边相连的点也看做一个点双,中间插入一个方点。

```cpp
vector<int> T[N<<1];
int cnt =  n;
void tarjan(int x)
{
    dfn[x] = low[x] = ++num;
    stack[++top] = x;
    for(int i = head[x] ; i ; i = nxt[i])
    {
        int y = to[i];
        if(!dfn[y])
        {
            tarjan(y);
            low[x] = min(low[x],low[y]);
            if(low[y] == dfn[x]) //以u为根的点双
            {
                cnt++;
                int tmp;
                do{
                    tmp = stack[top--];
                    T[cnt].push_back(tmp);
                    T[tmp].push_back(cnt);
                }while(tmp != y);
                dcc[cnt].push_back(x);
            }
            T[cnt].push_back(x);
            T[x].push_back(cnt);
        }
        else
            low[x] = min(low[x],dfn[y]);
    }
}
int main() {
  // 处理非连通图
  for (int u = 1; u <= N; ++u)
    if (!dfn[u]) Tarjan(u), --tp;
  // 注意到退出 Tarjan 时栈中还有一个元素即根，将其退栈
  return 0;
}
```



### 欧拉路问题

#### 欧拉图

无向图连通，且每个点的度数为偶数。（存在欧拉回路）

```cpp
int head[N],to[M],nxt[M],tot;
int stack[M],ans[M];//模拟系统栈，答案栈
bool vis[M];
int n,m,top,t;

void euler()
{
    stack[++top] = 1;
    while(top > 0)
    {
        int x = stack[top] , i =head[x];
        while(i && vis[i]) i = nxt[i]; // 寻找未访问的边
        if(i)//沿着这条边模拟递归过程，标记该边，并更新表头
        {
            stack[++top] = to[i];
            vis[i] = vis[i^1] = true;
            head[x] = nxt[i];
        }
        else //x相连边访问完毕，模拟会输过程，记录在答案栈中
        {
            top --;
            ans[++t] = x;
        }
    }
}
int main()
{
    euler();
    for(int i = t ; i ;  i--)
        printf("%d\n",ans[i]);
}
```

#### 欧拉路存在性判定

无向图连通，恰好有两个点度数为奇数，其余为偶数。

## 有向图

### 强连通分量

```cpp
int stack[N],ins[N],c[N];
vector<int> scc[N];
void tarjan(int x)
{
    dfn[x] = low[x] = ++num;
    stack[++top] = x , ins[x] = 1;
    for(int i = head[x] ; i ; i = nxt[i])
    {
        int y = to[i];
        if(!dfn[y])
        {
            tarjan(y);
            low[x] = min(low[x],low[y]);
        }
        else if(ins[y])
            low[x] = min(low[x],dfn[y]);
    }
    if(dfn[x] == low[x])
    {
        cnt++;
        int tmp;
        do{
            tmp = stack[top--] , ins[tmp] = 0;
            c[tmp] = cnt , scc[cnt].push_back(tmp);
        }while(tmp != x);
    }   
}

缩点:
for(int x = 1 ; x <= n ; x++)
{
    for(int i = head[x] ; i ; i = nxt[i])
    {
        int y = to[i];
        if(c[x] == c[y]) continue;
        add_c(c[x],c[y]);
    }
}
```

### 有向无环图的必经边和必经点

求S到每个点的路径条数CntS,每个点到T的路径条数CntT。
如果CntS[u]\*CntT[v] = CntS[T],则(u,v)为S到T 的必经边。
如果CntS[x]\*CntT[x] = CntS[T],则(u,v)为S到T 的必经点。
由于路径条数较多，可以采取hash方式存储路径数。

### 2-SAT问题

根据蕴含式建边，判断一个点的两面是否在同一强连通分量，判断是否存在矛盾式。

获得方案：

```cpp
//拓扑排序得到
void topsort()
{
    //选择初始化为-1
    memset(val,-1,sizeof(val));
    //缩点建反图
    for(int i = 1 ; i <= tot ; i ++)
    {
        int x = from[i] , y = to[i];
        if(c[x] != c[y])
            add2(c[y],c[x]),deg[c[x]]++;
    }
    while(q.size())q.pop();
    //入度为0的点（原图出度为0，不影响其他点选择）
    for(int i = 1 ; i <= cnt ; i ++)
        if(!deg[i])q.push(i);
    while(q.size())	
    {
        int k = q.front();
        q.pop();
        if(val[k] == -1)
            val[k] = 0 , val[opp[k]] = 1;
        for(int i = head2[k] ; i ; i = nxt2[i])
        {
            int y = to2[i];
            if(--deg[y] == 0)
                q.push(y);
        }
    }
}

//根据tarjan的缩点特性，SCC序已经满足反图拓扑序
for (int i = 1; i <= 2 * n; i++)
    	val[i] = c[i] > c[opp[i]];
```


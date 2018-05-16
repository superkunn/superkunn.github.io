---
layout: default
title: "最短路"
tags: graph
---
* 最短路的核心是松弛操作，即当前的最短距离大于先通过另一个点再到达此处的距离则进行松弛。
* 以[poj1511](http://poj.org/problem?id=15)以1为起点正反边求两次最短路即可。

## Bellman-Ford(TLE)

朴素的Bellman-Ford算法过不了，因为有大量重复计算，所以要用队列进行优化。
```c++
#include <cstdio>
#include <iostream>
#include <queue>
#include <cstring>
using namespace std;

typedef long long ll;

const int M=1000000+5;
const int inf=1e9+5;
int a,b;
int dis[M];
int ans[M];


void init(){
    memset(dis,-1,sizeof(dis));
    dis[1]=0;
}



int x[M];
int y[M];
int z[M];

int main()
{
    int n;
    scanf("%d",&n);
    while(n--){
        scanf("%d%d",&a,&b);
        init();
        for(int i=1;i<=b;i++){
            scanf("%d%d%d",&x[i],&y[i],&z[i]);
        }
        
        while(true){
            bool update=false;
            for(int i=1;i<=b;i++){
                if(dis[x[i]]!=-1&&(dis[y[i]]==-1||dis[y[i]]>dis[x[i]]+z[i])){
                    dis[y[i]]=dis[x[i]]+z[i];
                    update=true;
                }
            }
            if(!update){
                break;
            }
        }
        for(int i=2;i<=a;i++){
            ans[i]=dis[i];
        }

        init();
        while(true){
            bool update=false;
            for(int i=1;i<=b;i++){
                if(dis[y[i]]!=-1&&(dis[x[i]]==-1||dis[x[i]]>dis[y[i]]+z[i])){
                    dis[x[i]]=dis[y[i]]+z[i];
                    update=true;
                }
            }
            if(!update){
                break;
            }
        }
        
        long long res=0;
        for(int i=2;i<=a;i++){
            res+=ans[i]+dis[i];
        }
        printf("%I64d\n",res);

    }


    return 0;
}
```
## spfa(AC)

* 注意不要用vector

```c++
#include <cstdio>
#include <iostream>
#include <queue>
#include <cstring>
using namespace std;

typedef long long ll;

const int M=1000000+5;
const int inf=1e9+5;
int a,b;
int dis[M];
int ans[M];
bool book[M];
int tot;
int head[M];
struct node{
    int to;
    int v;
    int next;
}edge[M];

void init(){
    memset(dis,-1,sizeof(dis));
    dis[1]=0;
    memset(head,0,sizeof(head));
    tot=1;
    memset(book,0,sizeof(book));
}

void add_edge(int from,int to,int v){
    edge[tot].to=to;
    edge[tot].v=v;
    edge[tot].next=head[from];
    head[from]=tot++;
}

int x[M];
int y[M];
int z[M];

int main()
{
    int n;
    scanf("%d",&n);
    while(n--){
        scanf("%d%d",&a,&b);
        init();
        for(int i=1;i<=b;i++){
            scanf("%d%d%d",&x[i],&y[i],&z[i]);
            add_edge(x[i],y[i],z[i]);
        }
        queue<int> q;
        q.push(1);
        book[1]=1;
        while(!q.empty()){
            int t=q.front();
            q.pop();
            book[t]=0;
            for(int i=head[t];i!=0;i=edge[i].next){
                node &e=edge[i];
                if(dis[t]!=-1&&(dis[e.to]==-1||dis[e.to]>dis[t]+e.v)){
                    dis[e.to]=dis[t]+e.v;
                    if(!book[e.to]){
                        book[e.to]=1;
                        q.push(e.to);
                    }
                }
            }
        }
        for(int i=2;i<=a;i++){
            ans[i]=dis[i];
        }

        init();
        for(int i=1;i<=b;i++){
            add_edge(y[i],x[i],z[i]);
        }
        q.push(1);
        book[1]=1;
        while(!q.empty()){
            int t=q.front();
            q.pop();
            book[t]=0;
            for(int i=head[t];i!=0;i=edge[i].next){
                node &e=edge[i];
                if(dis[t]!=-1&&(dis[e.to]==-1||dis[e.to]>dis[t]+e.v)){
                    dis[e.to]=dis[t]+e.v;
                    if(!book[e.to]){
                        book[e.to]=1;
                        q.push(e.to);
                    }
                }
            }
        }

        long long res=0;
        for(int i=2;i<=a;i++){
            res+=ans[i]+dis[i];
        }
        printf("%I64d\n",res);

    }


    return 0;
}

```

## Dijkstra+堆优化

```
#include <iostream>  
#include <cstring>  
#include <cstdio>  
#include <queue>  
#include <cmath>  
#include <vector>  
#include <algorithm>  
#include <math.h>  
using namespace std;  
  
typedef long long ll;  
typedef pair<ll,int> P;  
  
const int MAX=1000000+5;  
const ll INF=1000000000+9;  
  
int n,q;  
int head[MAX];  
int aa[MAX];  
int bb[MAX];  
int cc[MAX];  
struct node{  
   int to;  
   int v;  
   int next;  
};  
node edge[MAX];  
  
ll d[MAX];  
  
int tot;  
  
void addedge(int a,int b,int c){  
    edge[tot].to=b;  
    edge[tot].v=c;  
    edge[tot].next=head[a];  
    head[a]=tot++;  
    return;  
}  
  
void dij(){  
    priority_queue<P, vector<P>, greater<P> > pque;  
    fill(d+1,d+1+n,INF);  
    d[1]=0;  
    pque.push(P(0,1));  
    while(!pque.empty()){  
        P p=pque.top();  
        pque.pop();  
        int vv=p.second;  
        if(d[vv]<p.first)continue;  
        for(int i=head[vv];i!=0;i=edge[i].next){  
            if(d[edge[i].to]>d[vv]+edge[i].v){  
                d[edge[i].to]=d[vv]+edge[i].v;  
                pque.push(P(d[edge[i].to],edge[i].to));  
            }  
        }  
    }  
    return;  
}  
  
  
void solve(){  
    int kase;  
    scanf("%d",&kase);  
    while(kase--){  
  
        ll ans=0;  
        scanf("%d %d",&n,&q);  
        tot=1;  
        memset(head,0,sizeof(head));  
        for(int i=1;i<=q;i++){  
            scanf("%d %d %d",&aa[i],&bb[i],&cc[i]);  
            addedge(aa[i],bb[i],cc[i]);  
        }  
        dij();  
        for(int i=2;i<=n;i++){  
            ans+=d[i];  
        }  
  
        tot=1;  
        memset(head,0,sizeof(head));  
        for(int i=1;i<=q;i++){  
            addedge(bb[i],aa[i],cc[i]);  
        }  
        dij();  
  
        for(int i=2;i<=n;i++){  
            ans+=d[i];  
        }  
  
        printf("%lld\n",ans);  
  
    }  
  
    return;  
}  
  
int main()  
{  
    solve();  
    return 0;  
}  
```

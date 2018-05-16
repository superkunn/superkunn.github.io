---
layout: default
title: "最短路"
tags: graph
---

## Bellman-Ford 

```
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

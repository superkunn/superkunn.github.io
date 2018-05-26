---
layout: default
title: "非递归线段树"
tags: datastruct
---

## 点更新+区间查询

```
#include <cstdio>
#include <algorithm>
using namespace std;
typedef long long ll;
int M;
int tree[100000];

void up(int i){tree[i]=tree[i<<1]+tree[i<<1|1];}

void build(int n){
    for(M=1;M<n;M<<=1);
    for(int i=M+1;i<=M+n;i++)scanf("%d",&tree[i]);
    for(int i=M-1;i;i--)up(i);
}

void update(int pos,int v){
    pos+=M;
    tree[pos]=v;
    for(pos>>=1;pos;pos>>=1)up(pos);
}

int sum(int l,int r){
    int ans=0;
    for(l=l+M-1,r=r+M+1;l^r^1;l>>=1,r>>=1){
        if(~l&1)ans+=tree[l^1];
        if(r&1)ans+=tree[r^1];
    }
    return ans;
}

int main(){
    int n;
    scanf("%d",&n);
    build(n);
    return 0;
}

```

## 区间查询+区间RMQ

```
#include <bits/stdc++.h>
using namespace std;
#define up(i, lower, upper) for(int i = lower; i < upper; i++)
#define down(i, lower, upper) for(int i = upper-1; i >= lower; i--)
typedef long long ll;

int m;
long long tree[10000];
void build(int len) {
    m = 1;
    memset(tree, 0, sizeof tree);
    while(m-1 <= len) m*=2;
    up(i, 1, len+1) cin>>tree[i+m];
    down(i, 1, m) tree[i] = min(tree[(i<<1)], tree[i<<1|1]),tree[i<<1]-=tree[i],tree[i<<1|1]-=tree[i];
}

void update(int l, int r, int val) {
    ll tmp;
    for(l += m-1, r += m+1; l^r^1; l>>=1, r>>=1) {
        if(~l&1) tree[l^1]+=val;
        if(r&1) tree[r^1]+=val;
        tmp = min(tree[l], tree[l^1]), tree[l] -= tmp, tree[l^1] -= tmp, tree[l>>1] += tmp;
        tmp = min(tree[r], tree[r^1]), tree[r] -= tmp, tree[r^1] -= tmp, tree[r>>1] += tmp;
    }
    for (;l!=1;l>>=1)
        tmp = min(tree[l],tree[l^1]), tree[l] -= tmp, tree[l^1] -= tmp, tree[l>>1] += tmp;
}

ll query(int l, int r) {
    ll lAns = 0, rAns = 0;
    l+=m, r+=m;
    if(l != r) {
        for(; l^r^1; l>>=1, r>>=1) {
            lAns += tree[l], rAns += tree[r];
            if(~l&1) lAns = min(lAns, tree[l^1]);
            if(r&1) rAns = min(rAns, tree[r^1]);
        }
    }
    ll ans = min(lAns+tree[l], rAns+tree[r]);
    while(l > 1) ans += tree[l>>=1];
    return ans;
}


int main(){
    build(3);
    update(1,3,2);
    for(int i=0;i<=m+3;i++){
        cout<<i<<"   "<<tree[i]<<endl;
    }
    cout<<query(1,3)<<endl;
    return 0;
}

```

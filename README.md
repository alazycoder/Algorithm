# KD-Tree

* 用优先队列实现了找k近邻的点。

* todo：直接找最近邻的一个点。

```c++
// kd-tree
const int max_dim=10;
const int max_points=50005;

int dim;
int current_idx=0;

struct Point{
    int x[max_dim];
    bool operator < (const Point &u) const{
        return x[current_idx] < u.x[current_idx];
    }
}a[max_points];

typedef pair<double, Point> pdp;
priority_queue<pdp> que;

struct Tree{
    Point p[max_points<<1];
    int son[max_points<<2]; // 记录每个节点的子孙数量
    
    void init(){
        memset(son, -1, sizeof(son));
    }
    
    int pow(int x){
        return x*x;
    }
    
    void build(int l, int r, int u=1, int dep=0){
        if(l>r)
            return;
        son[u] = r-l;
        current_idx = dep % dim;
        int mid = (l+r) >> 1;
        nth_element(a+l, a+mid, a+r+1); //排完序后, a[mid]是中位数
        p[u] = a[mid];
        build(l, mid-1, u<<1, dep+1);
        build(mid+1, r, u<<1|1, dep+1);
    }
    
    void query_k(Point b, int k, int u=1, int dep=0){
        if(son[u] == -1)
            return;
        int in=u<<1, not_in = u<<1|1, idx=dep%dim;
        if(b.x[idx] > p[u].x[idx])
            swap(in, not_in);
        if(son[in]!=-1)
            query_k(b, k, in, dep+1);
        // 判断u节点是否应该入队列
        pdp nd(0, p[u]);
        for(int i=0; i<dim; i++){
            nd.first += pow(p[u].x[i] - b.x[i]);
        }
        if(que.size()<k || nd.first < que.top().first){
            que.push(nd);
            while(que.size() > k)
                que.pop();
        }
        // 判断是否去另一棵子树搜索
        if((que.size() < k || que.top().first >= pow(b.x[idx]-p[u].x[idx])) && son[not_in] !=-1)
            query_k(b, k, not_in, dep+1);
    }
}tree;
```




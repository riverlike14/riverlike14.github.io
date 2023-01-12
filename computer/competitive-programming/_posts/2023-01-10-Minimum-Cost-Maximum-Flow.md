---
title: "[Competitive Programming] Minimum Cost Maximum Flow"
---

```c++
#include <iostream>
#include <algorithm>
#include <vector>
#include <queue>
using namespace std;

const int INF = 1e9;
const int N = 1000;

class NetworkFlow {
private:
    node source;
    node sink;

    int capacity[N][N];
    int flow[N][N];

    bool visited[N];
    bool inqueue[N];
    node previous[N];
    vector<node> children[N];
    int cost[N][N];
    int min_cost[N];

public:
    void update_capacity(node a, node b, int current, int expense) {
        capacity[a][b] = current;
        children[a].push_back(b);
        children[b].push_back(a);
        cost[a][b] = expense;
        cost[b][a] = -expense;
    }

    void update_source(node a) {
        source = a;
    }

    void update_sink(node b) {
        sink = b;
    }

    void spfa(node inode) {
        fill(visited, visited + N, false);
        fill(min_cost, min_cost + N, INF);
        fill(inqueue, inqueue + N, false);

        min_cost[inode] = 0;

        queue<node> q;
        q.push(inode);
        inqueue[inode] = true;

        while (!q.empty()) {
            inode = q.front();
            q.pop();

            visited[inode] = true;
            inqueue[inode] = false;

            for (auto cnode: children[inode]) {
                if (capacity[inode][cnode] <= flow[inode][cnode])
                    continue;
                if (min_cost[inode] + cost[inode][cnode] < min_cost[cnode]) {
                    min_cost[cnode] = min_cost[inode] + cost[inode][cnode];
                    previous[cnode] = inode;
                    if (!inqueue[cnode]) {
                        q.push(cnode);
                        inqueue[cnode] = true;
                    }
                }
            }
        }
    }

    void get_mcmf() {
        node inode, pnode;

        int total_flow = 0;
        int total_cost = 0;
        while (true) {
            spfa(source);

            if (!visited[sink])
                break;

            int additional_flow = INF;
            int additional_cost = 0;
            for (inode = sink; inode != source; inode = pnode) {
                pnode = previous[inode];
                additional_flow = min(additional_flow, capacity[pnode][inode] - flow[pnode][inode]);
            }
            for (inode = sink; inode != source; inode = pnode) {
                pnode = previous[inode];
                flow[pnode][inode] += additional_flow;
                flow[inode][pnode] -= additional_flow;
                additional_cost += additional_flow * cost[pnode][inode];
            }

            total_flow += additional_flow;
            total_cost += additional_cost;
        }

        cout << total_flow << endl;
        cout << total_cost << endl;
    }
};
```

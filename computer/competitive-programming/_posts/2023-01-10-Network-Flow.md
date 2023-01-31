---
title: "[Competitive Programming] Network Flow"
---

```c++
#include <iostream>
#include <cstring>
#include <vector>
#include <queue>
using namespace std;

const int INF = 1e9;
const int LEN = 5000;

class NetworkFlow {
private:
    node source;
    node sink;

    bool visited[LEN];
    node previous[LEN];
    int flow[LEN][LEN];
    int capacity[LEN][LEN];
    vector<node> children[LEN];

public:
    void update_capacity(node a, node b, int num) {
        capacity[a][b] = num;
        children[a].push_back(b);
        children[b].push_back(a);
    }

    void update_source(node a) {
        source = a;
    }

    void update_sink(node b) {
        sink = b;
    }

    void dfs(node inode) {
        if (visited[inode])
            return;
        visited[inode] = true;
        if (inode == sink)
            return;

        for (auto cnode: children[inode]) {
            if (!visited[cnode] && capacity[inode][cnode] > flow[inode][cnode]) {
                previous[cnode] = inode;
                dfs(cnode);
            }
        }
    }

    void bfs(node inode) {
        queue<node> q;
        q.push(source);

        while (!q.empty()) {
            inode = q.front();
            q.pop();

            if (visited[inode])
                continue;
            visited[inode] = true;
            if (inode == sink)
                return;

            for (auto cnode: children[inode]) {
                if (!visited[cnode] && capacity[inode][cnode] > flow[inode][cnode]) {
                    previous[cnode] = inode;
                    q.push(cnode);
                }
            }
        }
    }

    int get_maximum_flow(int method) {
        node inode, pnode;

        int total_flow = 0;
        while (true) {
            memset(visited, false, sizeof(visited));

            if (method == 0)
                dfs(source);
            else if (method == 1)
                bfs(source);

            if (!visited[sink])
                break;

            int additional_flow = INF;
            for (inode = sink; inode != source; inode = pnode) {
                pnode = previous[inode];
                additional_flow = min(additional_flow, capacity[pnode][inode] - flow[pnode][inode]);
            }
            for (inode = sink; inode != source; inode = pnode) {
                pnode = previous[inode];
                flow[pnode][inode] += additional_flow;
                flow[inode][pnode] -= additional_flow;
            }

            total_flow += additional_flow;
        }

        return total_flow;
    }
};
```

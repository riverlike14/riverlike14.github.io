---
title: "[Competitive Programming] Bipartite Matching"
---

```c++
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

typedef int node;

const int LEN = 500;

class BipartiteMatch {
private:
    bool insource[LEN];
    vector<node> source;
    vector<node> children[LEN];
    bool visited[LEN];
    int match[LEN];

public:
    void update_match(node a, node b) {
        children[a].push_back(b);
        if (!insource[a]) {
            insource[a] = true;
            source.push_back(a);
        }
    }

    bool dfs(node inode) {
        if (visited[inode])
            return false;
        visited[inode] = true;

        for (auto cnode: children[inode]) {
            if (match[cnode] == -1 || dfs(match[cnode])) {
                match[cnode] = inode;
                return true;
            }
        }
        return false;
    }

    int max_match() {
        fill(match, match + LEN, -1);

        int ans = 0;
        for (auto inode: source) {
            fill(visited, visited + LEN, false);
            ans += dfs(inode);
        }

        return ans;
    }
};
```

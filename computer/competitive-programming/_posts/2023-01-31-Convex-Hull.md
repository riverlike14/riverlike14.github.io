---
title: "[Competitive Programming] Convex Hull"
---

```c++
#include <iostream>
#include <vector>
#include <algorithm>
#define x first
#define y second
using namespace std;

typedef long long sll;
typedef pair<sll, sll> point;

const sll inf = 1e18;

point operator-(point a, point b) {
    return { a.x - b.x, a.y - b.y };
}

sll operator*(point a, point b) {
    return a.x * b.y - a.y * b.x;
}

sll abs(point p) {
    return p.x * p.x + p.y * p.y;
}

int ccw(point p1, point p2, point p3) {
    sll value = (p2 - p1) * (p3 - p2);
    if (value > 0)
        return 1;
    else if (value < 0)
        return -1;
    else
        return 0;
}

class ConvexHull {
private:
    int len = 0;
    point origin = { inf, inf };
    vector<point> arr;
    vector<point> convex;
public:
    void update(point p) {
        arr.push_back(p);
        origin = min(origin, p);
    }

    void findConvexHull() {
        sort(arr.begin(), arr.end(), [&] (point a, point b) {
            if ((a - origin) * (b - origin) == 0)
                return abs(a - origin) > abs(b - origin);
            return ccw(origin, a, b) > 0;
        });

        for (point p: arr) {
            while (len >= 2) {
                if (ccw(convex[len - 2], convex[len - 1], p) > 0)
                    break;
                convex.pop_back();
                len--;
            }
            convex.push_back(p);
            len++;
        }
    }

    sll getDiameter2() {
        convex.insert(convex.end(), convex.begin(), convex.end());

        sll dist = -1;
        for (int idx1 = 0, idx2 = 1; idx1 < len; idx1++) {
            point p1 = convex[idx1], p2 = convex[idx1 + 1];
            point q1 = convex[idx2], q2 = convex[idx2 + 1];

            while ((p2 - p1) * (q2 - q1) > 0) {
                idx2++;
                q1 = convex[idx2];
                q2 = convex[idx2 + 1];
            }

            dist = max(dist, abs(p1 - q1));
        }
        convex.resize(len);

        return dist;
    }

    bool isPointInside(point p) {
        for (int i = 0; i < len; i++) {
            if (ccw(convex[i], convex[(i + 1) % len], p) < 0)
                return false;
        }
        return true;
    }
};
```

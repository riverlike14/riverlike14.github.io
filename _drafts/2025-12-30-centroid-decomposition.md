---
title: "[Draft] Centroid Decomposition"
date: 2025-12-30 00:08:00+09:00
categories: [Computer, Algorithm]
tags: [centroid decomposition, tree] # TAG names should always be lowercase
---

# Description

# Code

```c++
const int MAX_LEN = 100001;

bool removed[MAX_LEN];
vector<int> adj[MAX_LEN];
int subtree_size[MAX_LEN];
int parent_centroid[MAX_LEN];

void set_subtree_size(int current_node, int parent_node) {
	subtree_size[current_node] = 1;
	for (int child_node : adj[current_node]) {
		if (removed[child_node] || child_node == parent_node) {
			continue;
		}
		set_subtree_size(child_node, current_node);
		subtree_size[current_node] += subtree_size[child_node];
	}
}

int get_centroid(int current_node, int parent_node, int total_size) {
	for (int child_node : adj[current_node]) {
		if (removed[child_node] || child_node == parent_node) {
			continue;
		}
		if (subtree_size[child_node] > total_size / 2) {
			return get_centroid(child_node, current_node, total_size);
		}
	}
	return current_node;
}

void build_centroid_tree(int current_node, int parent_node) {
	// Run build_centroid_tree(root, 0);
	set_subtree_size(current_node, 0);
	int centroid = get_centroid(current_node, 0, subtree_size[current_node]);

	removed[centroid] = true;
	parent_centroid[centroid] = parent_node;

	for (int child_node : adj[centroid]) {
		if (removed[child_node]) {
			continue;
		}
		build_centroid_tree(child_node, centroid);
	}
}
```

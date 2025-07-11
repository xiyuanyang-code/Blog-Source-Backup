---
title: DataStructure Disjoint Set
date: 2025-05-08 14:51:45
index_img: /img/cover/disjoint.jpg
excerpt: This article introduces the basic implementation and applications of the Union-Find data structure, including LCA (Lowest Common Ancestor) problems and maze generation.
math: true
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Disjoint Set
  - LCA
  - Data Structure
  - Finished
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# 不相交集 (并查集)

## Definition

不相交集是从**等价关系 & 等价类**出发的，即对非空集合$S$做出分割：

## Operations

- `find()`：找出特定元素属于哪一个等价类。
- `union()`：添加关系$(a,b)$，如果find失败的话。

如果使用线性表：

- 使用顺序存储，即线性表的第$i$个元素储存所属的等价类名。
	- 查找$O(1)$，更新为$O(n)$

如果使用树：

- `union`为$O(1)$
- `find`为$O(\log n)$（保证平衡）

## Implementations

每一个等价类表示为一颗树，使用**双亲表示法**来表示，即每一个节点只需要储存**指向父亲的指针**。我们可以使用**数组**来表示不相交集的森林。同样，`union`操作的时候，非常类似于堆的`merge`的操作，我们需要把两棵树合并在一起。

合并的操作是$O(1)$很快就可以完成，但是这会带来一个问题，**在极端情况下**，树会变得很不平衡，此时查找的时间复杂度就会退化到$O(N)$。因此，我们需要对`union`操作进行优化，本质上还是在尽可能保证树的平衡。

优化的方式有：

- 按照规模合并树
- 按照高度合并树

### 按照规模合并

- 规模小的树作为规模大的树的子树。
- 一般情况下，这不会增加树的高度。
- **我们需要记录每棵树的规模**。（储存额外的信息）
	- 一开始我们的根节点的parent值都是-1，我们可以利用这个-1来储存树的规模。（**这简直是信息压缩的神**）
	- 假设树的规模（节点的个数）为$n \ (n \ge 1)$，则根节点的parent数组中储存$-n$。

### 按照高度合并

考虑一些长的很奇形怪状的树，按规模合并有时候也会带来不平衡的情况，因此我们可以**按高度合并**，即高度小的树作为高度大的子树。parent存储的值同样也变为$-h$，对于根节点而言。

### 继续优化

我们在实现`union`的优化之后，可以保证树的平衡性。在并查集中，我们不关心**从根节点向下找的过程**，我们只希望找祖先的过程可以尽可能的快，因此**最优的并查集结构是只要两层的树：根节点和所有的子节点全部都是叶子结点**。我们可以在`find()`的过程中做路径压缩。

![image](https://s1.imagehub.cc/images/2025/05/08/6ae0a8e2c2b178620d1df14432356d3e.png)

## Code

```cpp
#include <cstddef>
#include <iostream>

class disjointSet {
private:
    size_t size;
    int *parent;

public:
    /**
     * @brief Construct a new disjoint Set object
     * 
     * @param s 
     */
    disjointSet(size_t s) : size(s) {
        parent = new int[size];

        // initiallize
        for (int i = 0; i < size; i++) {
            parent[i] = -1;
        }
    }

    /**
     * @brief Destroy the disjoint Set object
     * 
     */
    ~disjointSet() {
        delete parent;
    }

    /**
     * @brief Union two subtree, merged by scale
     * 
     * @param root1 
     * @param root2 
     */
    void Union(int root1, int root2) {
        if (root1 == root2) {
            return;
        }

        // !attention, these are all negative
        if (parent[root1] > parent[root2]) {
            parent[root2] += parent[root1];
            parent[root1] = root2;
        } else {
            parent[root1] += parent[root2];
            parent[root2] = root1;
        }
    }

    /**
     * @brief find the set which x lies in, using recursion
     * 
     * @param x 
     * @return int 
     */
    int Find(int x) {
        if (parent[x] < 0) {
            // x is the root node
            return x;
        }

        // optimize
        return (parent[x] = Find(parent[x]));
    }
};

int main() {
    // Create a disjoint set with 10 elements (0 to 9)
    disjointSet ds(10);

    // Perform some union operations
    std::cout << "Performing union operations..." << std::endl;
    ds.Union(0, 1);
    ds.Union(2, 3);
    ds.Union(4, 5);
    ds.Union(6, 7);
    ds.Union(8, 9);
    ds.Union(1, 3);// Union of sets containing 0, 1 and 2, 3
    ds.Union(5, 7);// Union of sets containing 4, 5 and 6, 7

    // Check the root of each element
    std::cout << "\nFinding roots of elements..." << std::endl;
    for (int i = 0; i < 10; i++) {
        std::cout << "Element " << i << " belongs to set with root: " << ds.Find(i) << std::endl;
    }

    // Check if two elements are in the same set
    std::cout << "\nChecking if elements are in the same set..." << std::endl;
    std::cout << "Are 0 and 3 in the same set? " << (ds.Find(0) == ds.Find(3) ? "Yes" : "No") << std::endl;
    std::cout << "Are 4 and 7 in the same set? " << (ds.Find(4) == ds.Find(7) ? "Yes" : "No") << std::endl;
    std::cout << "Are 8 and 9 in the same set? " << (ds.Find(8) == ds.Find(9) ? "Yes" : "No") << std::endl;
    std::cout << "Are 0 and 9 in the same set? " << (ds.Find(0) == ds.Find(9) ? "Yes" : "No") << std::endl;

    return 0;
}

```

## Applications

### Puzzle generation

我们可以把**迷宫的生成**抽象成并查集的问题。

对于集合$S$，入口位置$x$，出口位置$y$，如果$[x]_R = [y]_R$，则认为这个集合是联通的。

因此，对于迷宫的生成，算法如下：

- 每一次随机找到一个位置，并选择一个和他相邻的位置。
- **保证这两个位置不在同一个等价类中**，此时打通这堵墙，相当于做`Union()`操作。
- **外层循环终止条件**：出口和入口属于同一个等价类。

#### Code

```cpp
#include <cstddef>
#include <iostream>
#include <random>
#include <vector>

class disjointSet {
private:
    size_t size;
    int *parent;

public:
    /**
     * @brief Construct a new disjoint Set object
     * 
     * @param s 
     */
    disjointSet(size_t s) : size(s) {
        parent = new int[size];

        // initiallize
        for (int i = 0; i < size; i++) {
            parent[i] = -1;
        }
    }

    /**
     * @brief Destroy the disjoint Set object
     * 
     */
    ~disjointSet() {
        delete parent;
    }

    /**
     * @brief Union two subtree, merged by scale
     * 
     * @param root1 
     * @param root2 
     */
    void Union(int root1, int root2) {
        if (root1 == root2) {
            return;
        }

        // !attention, these are all negative
        if (parent[root1] > parent[root2]) {
            parent[root2] += parent[root1];
            parent[root1] = root2;
        } else {
            parent[root1] += parent[root2];
            parent[root2] = root1;
        }
    }

    /**
     * @brief find the set which x lies in, using recursion
     * 
     * @param x 
     * @return int 
     */
    int Find(int x) {
        if (parent[x] < 0) {
            // x is the root node
            return x;
        }

        // optimize
        return (parent[x] = Find(parent[x]));
    }
};


int generate_integer(int size) {
    // Create a random number generator
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_int_distribution<> dis(0, size * size - 1);

    return dis(gen);
}

void createPuzzle(int size) {
    int chosen_position, near_position;
    disjointSet ds(size * size);


    while (ds.Find(0) != ds.Find(size * size - 1)) {
        // 0 represents the starting position, while size^2-1 represents the ending positon, we need to ensure it is \to the same set
        while (true) {
            // chose the chosen_position randomly
            chosen_position = generate_integer(size);
            int find_index = ds.Find(chosen_position);

            // check up
            near_position = chosen_position - size;
            if (near_position >= 0 && ds.Find(near_position) != find_index) {
                break;
            }

            // check left
            near_position = chosen_position - 1;
            if (chosen_position % size != 0 && ds.Find(near_position) != find_index) {
                break;
            }

            // check down
            near_position = chosen_position + size;
            if (near_position < size * size && ds.Find(near_position) != find_index) {
                break;
            }

            // check right
            near_position = chosen_position + 1;
            if (near_position % size != 0 && ds.Find(near_position) != find_index) {
                break;
            }
        }

        // we have chosen the two positions, then we need to erase the wall between them
        ds.Union(ds.Find(chosen_position), ds.Find(near_position));

        // debugging info
        std::cout << "<" << chosen_position << "," << near_position << ">, ";
    }
}

void createPuzzle_with_visualization(int size){
    int chosen_position, near_position;
    disjointSet ds(size * size);

    // Initialize the maze grid with walls
    std::vector<std::vector<char>> maze(size * 2 + 1, std::vector<char>(size * 2 + 1, '#'));

    // Create the cells in the maze
    for (int i = 1; i < size * 2; i += 2) {
        for (int j = 1; j < size * 2; j += 2) {
            maze[i][j] = ' ';
        }
    }


    while (ds.Find(0) != ds.Find(size * size - 1)) {
        // 0 represents the starting position, while size^2-1 represents the ending position
        while (true) {
            // Choose the chosen_position randomly
            chosen_position = generate_integer(size);
            int find_index = ds.Find(chosen_position);

            // Check up
            near_position = chosen_position - size;
            if (near_position >= 0 && ds.Find(near_position) != find_index) {
                // Remove the wall between chosen_position and near_position
                maze[2 * (chosen_position / size) - 1][2 * (chosen_position % size)] = ' ';
                break;
            }

            // Check left
            near_position = chosen_position - 1;
            if (chosen_position % size != 0 && ds.Find(near_position) != find_index) {
                // Remove the wall between chosen_position and near_position
                maze[2 * (chosen_position / size)][2 * (chosen_position % size) - 1] = ' ';
                break;
            }

            // Check down
            near_position = chosen_position + size;
            if (near_position < size * size && ds.Find(near_position) != find_index) {
                // Remove the wall between chosen_position and near_position
                maze[2 * (chosen_position / size) + 1][2 * (chosen_position % size)] = ' ';
                break;
            }

            // Check right
            near_position = chosen_position + 1;
            if (near_position % size != 0 && ds.Find(near_position) != find_index) {
                // Remove the wall between chosen_position and near_position
                maze[2 * (chosen_position / size)][2 * (chosen_position % size) + 1] = ' ';
                break;
            }
        }

        // Union the two positions
        ds.Union(ds.Find(chosen_position), ds.Find(near_position));

        // Debugging info
        std::cout << "<" << chosen_position << "," << near_position << "> \n";

        // Print the maze after each wall removal
        for (const auto& row : maze) {
            for (char cell : row) {
                std::cout << cell;
            }
            std::cout << std::endl;
        }
        std::cout << std::endl;
    }
}

int main() {
    int size = 10;
    createPuzzle_with_visualization(size);
    return 0;
}

```

### lca problem

See [this](https://xiyuanyang-code.github.io/posts/DataStructure-LCA/) for more information!

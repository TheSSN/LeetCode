# Index

1.  [Merge k Sorted Lists](#merge-k-sorted-lists)
2.  [Add Two Numbers](#add-two-numbers)
3.  [House Robber](#house-robber)
4.  [Keys and Rooms](#keys-and-rooms)
5.  [Network Delay Time](#network-delay-time)
6.  [Robot Bounded In Circle](#robot-bounded-in-circle)
6.  [Clone Graph](#clone-graph)

---

### [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)<a name="merge-k-sorted-lists"></a>
```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */

ListNode *merge2Lists(ListNode *l1, ListNode *l2) {
    ListNode *result = new ListNode();
    ListNode *head = result;
    
    while (l1 != nullptr && l2 != nullptr) {
        if (l1->val <= l2->val) {
            head->next = new ListNode(l1->val);
            head = head->next;
            l1 = l1->next;
        } else {
            head->next = new ListNode(l2->val);
            head = head->next;
            l2 = l2->next;
        }
    }
    
    for (; l1 != nullptr; l1 = l1->next) {
        head->next = new ListNode(l1->val);
        head = head->next;
    }
    
    for (; l2 != nullptr; l2 = l2->next) {
        head->next = new ListNode(l2->val);
        head = head->next;
    }
    
    return result->next;
}

ListNode *mergeKLists(vector<ListNode*> &lists) {
    if (lists.size() == 0) {
        return nullptr;
    }
    
    if (lists.size() == 1) {
        return lists[0];
    }
    
    vector<ListNode*> newLists;
    int index = 0;
    
    // Merge every pair of lists
    while (index + 1 < lists.size()) {
        newLists.push_back(merge2Lists(lists[index], lists[index+1]));
        index += 2;
    }
    
    // In case there's an odd number of lists...
    if (index < lists.size()) {
        newLists.push_back(lists[index]);
    }
    
    return mergeKLists(newLists);
}
```

### [Add Two Numbers](https://leetcode.com/problems/add-two-numbers/)<a name="add-two-numbers"></a>
```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */

ListNode* addTwoNumbers(ListNode *l1, ListNode *l2) {
    ListNode *result = new ListNode();
    ListNode *resultTail = result;
    int carry = 0;

    // Add each digit while keeping track of carry
    while (l1 || l2 || carry != 0) {
        int digit1 = l1 ? l1->val : 0;
        int digit2 = l2 ? l2->val : 0;
        int sum = digit1 + digit2 + carry;

        carry = sum / 10;

        resultTail->next = new ListNode(sum % 10);
        resultTail = resultTail->next;

        l1 = l1 ? l1->next : nullptr;
        l2 = l2 ? l2->next : nullptr;
    }

    ListNode *finalResult = result->next;
    delete result;
    return finalResult;
}
```

### [House Robber](https://leetcode.com/problems/house-robber/)<a name="house-robber"></a>
```C++
int rob(vector<int> &nums) {
    // Solutions for the leftmost 0..n houses where we rob the rightmost house
    vector<int> solns;
    
    solns.push_back(0);        // Buffer value
    solns.push_back(0);        // Solution for 0 houses
    solns.push_back(nums[0]);  // Solution for 1 house
    
    for (int i = 1; i < nums.size(); ++i) {
        int soln = nums[i] + solns[solns.size()-2];  // Take rightmost and skip 1
        soln = max(soln, nums[i] + solns[solns.size()-3]);  // Take rightmost and skip 2
        solns.push_back(soln);
    }
    
    // Final solution either takes the rightmost or the 2nd rightmost house
    return max(solns[solns.size()-1], solns[solns.size()-2]);
}
```

### [Keys and Rooms](https://leetcode.com/problems/keys-and-rooms/)<a name="keys-and-rooms"></a>
```C++
void dfs(vector<vector<int>> &rooms, vector<bool> &visited, int i) {
    visited[i] = true;

    // Traverse over room keys and visit new rooms
    for (int j = 0; j < rooms[i].size(); ++j) {
        if (!visited[rooms[i][j]]) {
            dfs(rooms, visited, rooms[i][j]);
        }
    }
}

bool canVisitAllRooms(vector<vector<int>> &rooms) {
    int n = rooms.size();
    vector<bool> visited(n, false);
    int count = 0;

    for (int i = 0; i < n; ++i) {
        if (!visited[i]) {
            dfs(rooms, visited, i);
            ++count;  // Count the number of graph components
        }
    }

    return count == 1;
}
```

### [Network Delay Time](https://leetcode.com/problems/network-delay-time/)<a name="network-delay-time"></a>
```C++
int MAX_TIME = 1000000;  // Value > max possible time

struct Node {
    int v;  // Target node
    int w;  // Travel time (i.e. edge weight)
    Node *next;
};

int networkDelayTime(vector<vector<int>> &times, int n, int k) {
    k = k-1;  // For nodes labelled 0..n-1

    // Convert edge list to adjacency list
    vector<Node*> adjList(n, nullptr);
    for (int i = 0; i < times.size(); ++i) {
        int v1 = times[i][0]-1;
        int v2 = times[i][1]-1;
        int weight = times[i][2];
        
        adjList[v1] = new Node{v2, weight, adjList[v1]};
    }
    
    // Make max heap <-distance, node> (-distance instead of min heap)
    priority_queue<pair<int, int>> maxHeap;
    for (int i = 0; i < n; ++i) {
        maxHeap.push(make_pair(i == k? 0 : -MAX_TIME, i));
    }
    
    vector<bool> visited(n, false);
    vector<double> dists(n, MAX_TIME);
    dists[k] = 0;
    int processed = 0;
    
    // Run Dijkstra's Algorithm
    while (processed < n) {
        pair<int, int> nextNode;
        while (true) {
            nextNode = maxHeap.top();
            maxHeap.pop();
            if (!visited[nextNode.second]) {
                visited[nextNode.second] = true;
                break;
            }
        }
        
        int y = nextNode.second;
        int weight = -nextNode.first;
        
        // This would only happen if the node is not connected to root
        if (weight == MAX_TIME) {
            break;
        }
        
        for (Node *edge = adjList[y]; edge; edge = edge->next) {
            if (weight + edge->w < dists[edge->v]) {
                dists[edge->v] = weight + edge->w;
                maxHeap.push(make_pair(-dists[edge->v], edge->v));
            }
        }
        ++processed;
    }
    
    int maxTime = 0;
    for (int i = 0; i < n; ++i) {
        if (dists[i] > maxTime) {
            maxTime = dists[i];
        }
    }
    
    return maxTime == MAX_TIME? -1 : maxTime;
}
```

### [Robot Bounded In Circle](https://leetcode.com/problems/robot-bounded-in-circle/)<a name="robot-bounded-in-circle"></a>
```C++
bool isRobotBounded(string instructions) {
    int direction = 0;  // 0: North, 1: East, 2: South, 3: West
    int x = 0;
    int y = 0;

    // Go through instruction set 4 times
    for (int i = 0; i < 4; ++i) {
        for (int j = 0; j < instructions.size(); ++j) {
            if (instructions[j] == 'G') {
                if (direction == 0) {
                    ++y;
                } else if (direction == 1) {
                    ++x;
                } else if (direction == 2) {
                    --y;
                } else if (direction == 3) {
                    --x;
                }
            } else if (instructions[j] == 'L') {
                direction = (direction + 3) % 4;
            } else if (instructions[j] == 'R') {
                direction = (direction + 1) % 4;
            }
        }
        // Check if back to initial position (i.e. cycle => movement is bounded)
        if (direction == 0 && x == 0 && y == 0) {
            return true;
        }
    }
    return false;
}
```

### [Clone Graph](https://leetcode.com/problems/clone-graph/)<a name="clone-graph"></a>
```C++
Node *cloneNode(Node *node, unordered_map<int, Node*> &visitedNodes) {
    // Check if we've already cloned this node
    if (visitedNodes.count(node->val)) {
        return visitedNodes[node->val];
    }

    // Create new node and store a stub in case of cycles
    Node *newNode = new Node(node->val);
    visitedNodes[node->val] = newNode;

    // Recursively clone each neighbour
    vector<Node*> neighbors;
    for (int i = 0; i < node->neighbors.size(); ++i) {
        neighbors.push_back(cloneNode(node->neighbors[i], visitedNodes));
    }
    newNode->neighbors = move(neighbors);

    return newNode;
}

Node *cloneGraph(Node *node) {
    if (!node) return nullptr;
    unordered_map<int, Node*> visitedNodes;

    return cloneNode(node, visitedNodes);
}
```

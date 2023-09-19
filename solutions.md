# Index

1.  [Merge k Sorted Lists](#merge-k-sorted-lists)

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
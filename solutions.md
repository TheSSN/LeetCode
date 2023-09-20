# Index

1.  [Merge k Sorted Lists](#merge-k-sorted-lists)
2.  [Add Two Numbers](#add-two-numbers)

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

ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
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
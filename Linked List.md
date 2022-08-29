# [2. 两数相加](https://leetcode.cn/problems/add-two-numbers/)

![image-20220822032955916](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822032955916.png)

**循环执行的条件是只要链表不为空或有进位就执行，每次统计节点（节点不为空）加进位的和，更新进位**

```c++
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* dummy = new ListNode();
        ListNode* p = dummy;
        int carry = 0;
        while (l1 || l2 || carry != 0) {
            int val = carry;
            if (l1 != nullptr) {
                val += l1->val;
                l1 = l1->next;
            }
            if (l2 != nullptr) {
                val += l2->val;
                l2 = l2->next;
            }
            carry = val / 10;
            val = val % 10;
            p->next = new ListNode(val);
            p = p->next;
        }
        return dummy->next;
    }
};
```

**另一种写法，单独判断最后的carry**

```c++
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* pre = new ListNode();
        ListNode* cur = pre;
        int carry = 0;
        while (l1 != nullptr || l2 != nullptr) {
            int x = l1 == nullptr ? 0 : l1->val;
            int y = l2 == nullptr ? 0 : l2->val;
            int val = x + y + carry;
            carry = val / 10;
            val = val % 10;
            cur->next = new ListNode(val);
            cur = cur->next;
            if (l1) l1 = l1->next;
            if (l2) l2 = l2->next;        
        }
        if (carry > 0) {
            cur->next = new ListNode(carry);
        }
        return pre->next;
    }
};
```

# [146. LRU 缓存](https://leetcode.cn/problems/lru-cache/)

![image-20220822032921757](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822032921757.png)

```C++
class LRUCache {
public:
    class ListNode{
    public:
        int val, key;
        ListNode* pre, *next;
        ListNode() : val(-1), key(-1), pre(nullptr), next(nullptr) {}
        ListNode(int x, int y) : key(x), val(y), pre(nullptr), next(nullptr) {}
    }*head, *tail;
    int cap;
    int count;
    unordered_map<int, ListNode*> mp;
    LRUCache(int capacity) {
        count = 0;
        cap = capacity;
        head = new ListNode();
        tail = new ListNode();
        head->next = tail;
        tail->pre = head;
    }
    
    int get(int key) {
        if (mp.count(key) > 0) {
            remove(mp[key]);
            setHead(mp[key]);
            return mp[key]->val;
        }
        return -1;
    }
    
    void put(int key, int value) {
        if (mp.count(key) > 0) {
            mp[key]->val = value;
            remove(mp[key]);
            setHead(mp[key]);
        } else {
            mp[key] = new ListNode(key, value);
            count++;
            setHead(mp[key]);
        }
        if (count > cap) {
            mp.erase(tail->pre->key);
            ListNode* tmp = tail->pre;
            remove(tmp);
            delete(tmp);
            count--;
        }
    }
    void setHead(ListNode* node) {
        node->pre = head;
        node->next = head->next;
        head->next->pre = node;
        head->next = node;
    }
    void remove(ListNode* node) {
        node->pre->next = node->next;
        node->next->pre = node->pre;
    }
};
```

# [206. 反转链表](https://leetcode.cn/problems/reverse-linked-list/)
**递归（好理解版本）**

```c++
class Solution {
public:
    ListNode* reverse(ListNode* pre, ListNode* head) {
        if (head == nullptr) return pre;  //递归出口不要忘记
        ListNode* tmp = head->next;
        head->next = pre;
        pre = head;
        return reverse(pre, tmp);
    }
    ListNode* reverseList(ListNode* head) {
        return reverse(nullptr, head);
    }
};
```
**递归（不好理解版本）**

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return head;
        ListNode* ret = reverseList(head->next);
        head->next->next = head;
        head->next = nullptr;  //别忘了给当前最后节点next置空
        return ret;   //每次返回的都是最后一个节点
    }
};
```
**迭代**

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr) return head;
        ListNode* pre = nullptr;
        while (head != nullptr) {
            ListNode* tmp = head->next;
            head->next = pre;
            pre = head;
            head = tmp;
        }
        return pre;
    }
};
```

# [92. 反转链表 II](https://leetcode.cn/problems/reverse-linked-list-ii/)

![image-20220822033121111](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822033121111.png)

## 递归法

```C++
class Solution {
public:
    ListNode* succ = nullptr;
    ListNode* reverseN(ListNode* head, int n) {
        if (n == 1) {
            succ = head->next;
            return head;
        }
        ListNode* last = reverseN(head->next, n - 1);
        head->next->next = head;
        head->next = succ;
        return last;
    }
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        if (left == 1) return reverseN(head, right);
        head->next = reverseBetween(head->next, left - 1, right - 1);
        return head;
    }
};
```

## 头插法

![image.png](https://pic.leetcode-cn.com/1615105296-bmiPxl-image.png)

```C++
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        ListNode* dummyNode = new ListNode(-1);
        dummyNode->next = head;
        ListNode* pre = dummyNode;
        for (int i = 0; i < left - 1; i++) {
            pre = pre->next;
        }
        ListNode* curr = pre->next;
        ListNode* next = curr->next;
        for (int i = 0; i < right - left; i++) {
            curr->next = next->next;
            next->next = pre->next;
            pre->next = next;
            next = curr->next;
        }
        return dummyNode->next;
    }
};
```

## 分割+翻转+拼接

![image.png](https://pic.leetcode-cn.com/1615105168-ZQRZew-image.png)

```C++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr) return head;
        ListNode* pre = nullptr;
        while (head != nullptr) {
            ListNode* tmp = head->next;
            head->next = pre;
            pre = head;
            head = tmp;
        }
        return pre;
    }
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        ListNode* dummyNode = new ListNode(-1);
        dummyNode->next = head;
        ListNode* pre = dummyNode;
        for (int i = 0; i < left - 1; i++) {
            pre = pre->next;
        }
        ListNode* leftNode = pre->next;
        ListNode* rightNode = pre;
        for (int i = 0; i < right - left + 1; i++) {
            rightNode = rightNode->next;
        }
        ListNode* curr = rightNode->next;
        //切割链表
        pre->next = nullptr;
        rightNode->next = nullptr;
        //反转链表
        reverseList(leftNode);
        //拼接链表
        pre->next = rightNode;
        leftNode->next = curr;
        return dummyNode->next;

    }
};
```

# [25. K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/)

![image-20220822033157479](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822033157479.png)

**利用翻转链表前N个节点做**，统计好需要翻转几部分，迭代的去翻转即可

需要注意的点，head节点反翻转后就会到翻转部分的末尾，head的下一个节点即下一次翻转的开始

```c++
class Solution {
public:
    ListNode* curr = nullptr;
    ListNode* reverseN(ListNode* head, int N) {
        if (N == 1) {
            curr = head->next;
            return head;
        }
        ListNode* ret = reverseN(head->next, N - 1);
        head->next->next = head;
        head->next = curr;
        return ret;
    } 
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* dummyNode = new ListNode();
        dummyNode->next = head;
        int count = 0;
        ListNode* cur = dummyNode;
        while (cur->next != nullptr) {
            cur = cur->next;
            ++count;
        }
        int a = count / k;
        ListNode* pre = dummyNode;
        while (a--) {
            pre->next = reverseN(head, k);
            pre = head;
            head = head->next;           
        }
        return dummyNode->next;
    }
};
```

**利用翻转链表做**，分割大小为k的链表，翻转后再拼接，迭代执行

```C++
class Solution {
public:
    ListNode* reverse(ListNode* head) {
        if (head == nullptr || head->next == nullptr) {
            return head;
        }
        ListNode* ret = reverse(head->next);
        head->next->next = head;
        head->next = nullptr;
        return ret;
    } 
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* dummyhead = new ListNode();
        dummyhead->next = head;
        ListNode* pre, *start, *end, *next;
        pre = dummyhead;
        end = dummyhead;
        while (end->next != nullptr) {
            for (int i = 0; i < k && end != nullptr; i++) {
                end = end->next;
            }
            if (end == nullptr) break;
            start = pre->next;
            next = end->next;
            end->next = nullptr;
            pre->next = reverse(start);
            start->next = next;
            pre = start;
            end = pre;
        }
        return dummyhead->next;
    }
};
```

# [21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)

##  迭代

```C++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode* dummynode = new ListNode();
        ListNode* cur = dummynode;
        while (l1 != NULL && l2 != NULL) {
            if (l1->val <= l2->val) {
                cur->next = l1;
                l1 = l1->next;
            } else {
                cur->next = l2;
                l2 = l2->next;
            }
            cur = cur->next;
        }
        cur->next = !l1 ? l2 : l1;
        return dummynode->next;
    }
};
```

##  递归


```C++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if (!l1) return l2;
        if (!l2) return l1;
        if (l1->val < l2->val) {
            l1->next = mergeTwoLists(l1->next, l2);
            return l1;
        } else {
            l2->next = mergeTwoLists(l1, l2->next);
            return l2;
        }
    }
};
```

# [23. 合并K个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/)

## 分治合并


```c++
class Solution {
public:
    ListNode* merge2Lists(ListNode* head1, ListNode* head2) {
        if (head1 == nullptr) return head2;
        if (head2 == nullptr) return head1;
        if (head1->val < head2->val) {
            head1->next = merge2Lists(head1->next, head2);
            return head1;
        } else {
            head2->next = merge2Lists(head1, head2->next);
            return head2;
        }
    }
    ListNode* merge(vector<ListNode*>& lists, int left, int right) {
        if (left == right) return lists[left];
        if (left > right)  return nullptr;
        int mid = (left + right) / 2;
        return merge2Lists(merge(lists, left, mid), merge(lists, mid + 1, right));
    }
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        return merge(lists, 0, lists.size() - 1);
    }
};
```

## 优先队列

**关于优先队列自定义比较器：**


如果直接将结构体放入priority_queue中，则需在结构体中重载<号（或是在结构体外重载<号），优先队列默认使用less<>

如果放入的不是某个结构体，则需定义结构体cmp并在其内重载小括号，并将cmp写到优先队列的第三个参数

**关于sort自定义比较器：**


如果使用结构体，需在结构体内重载小括号（返回值为bool），并将匿名对象（类名()）或对象实例放到sort第三个参数

如果使用函数，需自定义一个返回类型为bool值的函数，并将函数名放到sort第三个参数

**好理解版本**


```C++
class Solution {
public:
    struct cmp{
        bool operator () (ListNode* a, ListNode* b) {
            return a->val > b->val;
        }
    };
    priority_queue<ListNode*, vector<ListNode*>, cmp> pq;
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        for (auto p : lists) {
            if (p) pq.push(p);
        }
        ListNode head, *cur = &head;
        while (!pq.empty()) {
            ListNode* f = pq.top(); pq.pop();
            cur->next = f;
            cur = cur->next;
            if (f->next) pq.push(f->next); 
        }
        return head.next;
    }
};
```

**不好理解版本**


```C++
class Solution {
public:
    struct Status {
        int val;
        ListNode *ptr;
        bool operator < (const Status &rhs) const {
            return val > rhs.val;
        }
    };

    priority_queue <Status> q;

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        for (auto node: lists) {
            if (node) q.push({node->val, node});
        }
        ListNode head, *tail = &head;
        while (!q.empty()) {
            auto f = q.top(); q.pop();
            tail->next = f.ptr; 
            tail = tail->next;
            if (f.ptr->next) q.push({f.ptr->next->val, f.ptr->next});
        }
        return head.next;
    }
};
```

# [剑指 Offer 22. 链表中倒数第k个节点](https://leetcode.cn/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

![image-20220822033239137](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822033239137.png)

## 哈希表

```C++

class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        unordered_map<int, ListNode*> map;
        int i = 0;
        while (head != NULL) {
            map[++i] = head;
            head = head->next;
        }
        return map[i - k + 1];
    }
};
```

## 快慢指针

```C++
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        ListNode* fast = head;
        ListNode* slow = head;
        while (k--) fast = fast->next;
        while (fast != NULL) {
            fast = fast->next;
            slow = slow->next;
        }
        return slow;
    }
};
```

# [143. 重排链表](https://leetcode.cn/problems/reorder-list/)

![image-20220822033317636](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822033317636.png)

## **寻找链表中点 + 链表逆序 + 合并链表**

```c++
class Solution {
public:
    ListNode* reverse(ListNode* head) {
        if (head == nullptr || head->next == nullptr) {
            return head;
        }
        ListNode* ret = reverse(head->next);
        head->next->next = head;
        head->next = nullptr;
        return ret;
    }
    void reorderList(ListNode* head) {
        if (head == nullptr) return;
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast->next != nullptr && fast->next->next != nullptr) {
            fast = fast->next->next;
            slow = slow->next;
        }
        ListNode* midHead = slow->next;
        slow->next = nullptr;
        midHead = reverse(midHead);
        ListNode* p1 = head;
        ListNode* p2 = midHead;
        while (p2 != nullptr) {
            ListNode* tmp1 = p1->next;
            ListNode* tmp2 = p2->next;
            p1->next = p2;
            p2->next = tmp1;
            p1 = tmp1;
            p2 = tmp2;         
        }
        return;
    }
};
```

## **线性表**

```c++
class Solution {
public:
    void reorderList(ListNode *head) {
        if (head == nullptr) {
            return;
        }
        vector<ListNode *> vec;
        ListNode *node = head;
        while (node != nullptr) {
            vec.emplace_back(node);
            node = node->next;
        }
        int i = 0, j = vec.size() - 1;
        while (i < j) {
            vec[i]->next = vec[j];
            i++;
            if (i == j) {
                break;
            }
            vec[j]->next = vec[i];
            j--;
        }
        vec[i]->next = nullptr;
    }
};
```

# [707. 设计链表](https://leetcode.cn/problems/design-linked-list/)

![image-20220822033359743](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822033359743.png)

```c++
class MyLinkedList {
public:
    struct LinkedNode {
        int val;
        LinkedNode* next;
        LinkedNode(int x) : val(x), next(nullptr) {}
        };

    MyLinkedList() {
        dummyhead = new LinkedNode(0);
        size = 0;
    }
    
    int get(int index) {
        if (index > (size - 1) || index < 0) {
            return -1;
        }
        LinkedNode* cur = dummyhead->next;
        while (index--) {
            cur = cur->next;
        }
        return cur->val;
    }
    
    void addAtHead(int val) {
        LinkedNode* newNode = new LinkedNode(val);
        newNode->next = dummyhead->next;
        dummyhead->next = newNode;
        size++;
    }
    
    void addAtTail(int val) {
        LinkedNode* newNode = new LinkedNode(val);
        LinkedNode* cur = dummyhead;
        while (cur->next != nullptr) {
            cur = cur->next;
        }
        cur->next = newNode;
        size++;
    }
    
    void addAtIndex(int index, int val) {
        if (index > size) {
            return;
        }
        LinkedNode* newNode = new LinkedNode(val);
        LinkedNode* cur = dummyhead;
        while (index--) {
            cur = cur->next;
        }
        newNode->next = cur->next;
        cur->next = newNode;
        size++;
    }
    
    void deleteAtIndex(int index) {
        if (index >= size || index < 0) {
            return;
        }
        LinkedNode* cur = dummyhead;
        while(index--) {
            cur = cur->next;
        }
        LinkedNode* tmp = cur->next;
        cur->next = cur->next->next;
        delete tmp;
        size--;
    }
private:
    int size;
    LinkedNode* dummyhead;
};
```

# [203. 移除链表元素](https://leetcode.cn/problems/remove-linked-list-elements/)

![image-20220822142603326](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822142603326.png)

## 递归

```C++
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        if (head == nullptr) return head;
        if (head->val == val) {
            return removeElements(head->next, val);
        } else {
            head->next = removeElements(head->next, val);
            return head;
        }
    }
};
```

## 迭代

```c++
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        if (head == nullptr) return head;
        ListNode* dummy = new ListNode();
        dummy->next = head;
        ListNode* cur = dummy;
        while (cur->next != nullptr) {
            if (cur->next->val == val) {
                cur->next = cur->next->next;
            } else {
                cur = cur->next;
            }
        }
        return dummy->next;
    }
};
```

# [83. 删除排序链表中的重复元素](https://leetcode.cn/problems/remove-duplicates-from-sorted-list/)

![image-20220822041852380](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822041852380.png)

## 双指针

```C++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head) return head;
        ListNode* slow = head;
        ListNode* fast = head->next;
        while (fast != nullptr) {
            if (fast->val != slow->val) {
                slow->next = fast;
                slow = slow->next;
                fast = fast->next;
            } else {
                fast = fast->next;
            }
        }
        slow->next = nullptr;
        return head;
    }
};
```

## 迭代

```C++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head)	return head;
        ListNode* cur = head;
        while (cur->next) {
            if (cur->val == cur->next->val) {
                cur->next = cur->next->next;
            } else {
                cur = cur->next;
            }
        }
        return head;
    }
};

```

## 递归（没必要）

```C++
class Solution {
public:
    ListNode* dfs(ListNode* head, int x) {       
        while (head && head->val == x) {
            head = head->next;
        }
        if (!head) return head;
        head->next = dfs(head->next, head->val);
        return head;
    }
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head) return head;
        head->next = dfs(head->next, head->val);
        return head;
    }
};
```



# [82. 删除排序链表中的重复元素 II](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/)

![image-20220822033428568](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822033428568.png)

## 迭代

```C++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (head == nullptr) return head;
        ListNode* dummy = new ListNode(101);
        dummy->next = head;
        ListNode* cur = dummy;
        ListNode* pre = dummy;
        while (cur != nullptr && cur->next != nullptr) {
            if (cur->val != cur->next->val) {
                pre = cur;
                cur = cur->next;
            } else {
                int x = cur->val;
                while (cur != nullptr && cur->val == x) {
                    cur = cur->next;
                }
                pre->next = cur;
            }
        }
        return dummy->next;
    }
};
```

## 递归

```C++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head || !head->next) {
            return head;
        }
        if (head->val != head->next->val) {
            head->next = deleteDuplicates(head->next);
        } else {
            ListNode* move = head->next;
            while (move != nullptr && head->val == move->val) {
                move = move->next;
            }
            return deleteDuplicates(move);
        }
        return head;
    }
};
```

## 利用计数，两次遍历

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        unordered_map<int, int> m;
        ListNode dummy(0);
        ListNode* dummy_move = &dummy;
        ListNode* move = head;
        while (move) {
            m[move->val]++;
            move = move->next;
        }
        move = head;
        while (move) {
            if (m[move->val] == 1) {
                dummy_move->next = move;
                dummy_move = dummy_move->next;
            }
            move = move->next;
        }
        dummy_move->next = nullptr;
        return dummy.next;
    }
};
```

# [19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

![image-20220822035752749](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822035752749.png)

## 双指针

```C++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummy = new ListNode();
        dummy->next = head;
        ListNode* p1 = dummy;
        ListNode* p2 = dummy;
        for (int i = 0; i < n; ++i) {
            p1 = p1->next;
        }
        while (p1->next != nullptr) {
            p1 = p1->next;
            p2 = p2->next;
        }
        ListNode* tmp = p2->next;
        p2->next = p2->next->next;
        delete(tmp);
        return dummy->next;
    }
};
```

## 递归

```C++
class Solution {
public:
    int dfs(ListNode* head, int n) {
        if (head == nullptr) return 0;
        int cnt = dfs(head->next, n);
        if (cnt == n) {
            head->next = head->next->next;
        }
        return cnt + 1;
    }
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        int cnt = dfs(head, n);
        if (cnt == n) {
            return head->next;
        }
        return head; 
    }
};
```

# [24. 两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)

![image-20220822034518796](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822034518796.png)

## 迭代

```C++
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (head == NULL || head->next == NULL) return head;
        ListNode* dummyHead = new ListNode();
        dummyHead->next = head;
        ListNode* cur = dummyHead;
        while (cur->next != NULL && cur->next->next != NULL) {
            ListNode* tmp1 = cur->next;
            ListNode* tmp2 = cur->next->next;
            tmp1->next = tmp2->next;
            cur->next = tmp2;
            tmp2->next = tmp1;
            cur = tmp1;
        }
        return dummyHead->next;
    }
};
```

## 递归

```C++
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (head == NULL || head->next == NULL) return head;
        ListNode* tmp = head->next;
        head->next = swapPairs(head->next->next);
        tmp->next = head;
        return tmp;
    }
};
```

# [141. 环形链表](https://leetcode.cn/problems/linked-list-cycle/)

![image-20220822035005899](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822035005899.png)

## 快慢指针

```C++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode* fast = head, *slow = head;
        do{
            if (fast == nullptr || fast->next == nullptr) {
                return false;
            }
            fast = fast->next->next;
            slow = slow->next;
        } while (fast != slow);
        return true;
    }
};
```

```c++
class Solution {
public:
    bool hasCycle(ListNode* head) {
        if (head == nullptr || head->next == nullptr) {
            return false;
        }
        ListNode* slow = head;
        ListNode* fast = head->next;
        while (slow != fast) {
            if (fast == nullptr || fast->next == nullptr) {
                return false;
            }
            slow = slow->next;
            fast = fast->next->next;
        }
        return true;
    }
};
```

## 哈希表

```C++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        unordered_set<ListNode*> seen;
        while (head != nullptr) {
            if (seen.count(head)) {
                return true;
            }
            seen.insert(head);
            head = head->next;
        }
        return false;
    }
};
```

# [142. 环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)

## 双指针

```C++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {   
        ListNode* fast = head;
        ListNode* slow = head;
        do {
            if (fast == NULL || fast->next == NULL) return NULL;
            fast = fast->next->next;
            slow = slow->next;
        } while (fast != slow);
        fast = head;
        while (fast != slow) {
            fast = fast->next;
            slow = slow->next;
        }
        return fast;
    }
};
```

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode fast = head, slow = head;
        while (true) {
            if (fast == null || fast.next == null) return null;
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) break;
        }
        fast = head;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }
        return fast;
    }
}
```

# [234. 回文链表](https://leetcode.cn/problems/palindrome-linked-list/)

![image-20220822035246145](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822035246145.png)

## 快慢指针+反转链表

```C++
class Solution {
public:
    ListNode* reverse(ListNode* head) {
        ListNode* cur = head, *pre = NULL;
        while (cur != NULL) {
            ListNode* tmp = cur->next;
            cur->next = pre;
            pre = cur;
            cur = tmp;
        }
        return pre;
    }

    bool isPalindrome(ListNode* head) {
        ListNode *fast = head, *slow = head;
        while (fast != NULL && fast->next != NULL) {
            fast = fast->next->next;
            slow = slow->next;
        }
        if (fast != NULL) slow = slow->next;
        ListNode* right =  reverse(slow);
        ListNode* left = head;
        while (right != NULL) {
            if (left->val != right->val) return false;
            left = left->next;
            right = right->next;
        }
        return true;
    }
};
```

## 使用辅助数组

```C++
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        vector<int> v;
        while (head != NULL) {
            v.push_back(head->val);
            head = head->next;
        }
        int l = 0, r = v.size() - 1;
        while (l < r) {
            if (v[l] == v[r]) {
                l++, r--;
                continue;
            }
            return false;
        }
        return true;
    }
};
```

# [剑指 Offer 36. 二叉搜索树与双向链表](https://leetcode.cn/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

![image-20220822140949407](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822140949407.png)

![image-20220822141019780](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822141019780.png)

![image-20220822141040527](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822141040527.png)

## 宏观递归

```C++
class Solution {
public:
    Node* treeToDoublyList(Node* root) {
        if (!root) return root;
        Node* leftHead = treeToDoublyList(root->left);
        Node* rightHead = treeToDoublyList(root->right);
        Node* leftTail, *rightTail;
        if (leftHead) {
            leftTail = leftHead->left;
            leftTail->right = root;
            root->left = leftTail;
        } else {
            leftTail = leftHead = root;
        }
        if (rightHead) {
            rightTail = rightHead->left;
            root->right = rightHead;
            rightHead->left = root;
        } else {
            rightTail = rightHead = root;
        }
        leftHead->left = rightTail;
        rightTail->right = leftHead;
        return leftHead;       
    }
};
```

## 中序遍历

```C++
class Solution {
public:
    Node* treeToDoublyList(Node* root) {
        if (!root) return NULL;
        dfs(root);
        head->left = pre;
        pre->right = head;
        return head;      
    }
private: 
    Node* pre, *head;
    void dfs(Node* node) {
        if (!node) return;
        dfs(node->left);
        if (pre != NULL) pre->right = node;
        else head = node;
        node->left = pre;
        pre = node;
        dfs(node->right);
    }
};
```

# [148. 排序链表](https://leetcode.cn/problems/sort-list/)

![image-20220822141820536](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822141820536.png)

## 哈希表

```c++
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        if (head == nullptr) return head;
        multiset<int> set;
        ListNode* cur = head;
        while (cur != nullptr) {
            set.insert(cur->val);
            cur = cur->next;
        }
        cur = head;
        for (auto& i : set) {
            cur->val = i;
            cur = cur->next;
        }
        
        return head;
    }
};
```

```C++
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        if (head == nullptr) return head;
        multimap<int, ListNode*> map;
        ListNode* cur = head;
        while (cur != nullptr) {
            ListNode* tmp = cur;
            cur = cur->next;
            tmp->next = nullptr;
            map.insert({tmp->val, tmp});
            
        }
        ListNode* Head = new ListNode();
        cur = Head;
        for (auto pair : map) {
            cur->next = pair.second;
            cur = cur->next;
        }
        
        return Head->next;
    }
};
```

## 归并排序

```C++
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return head;
        ListNode* slow = head;
        ListNode* fast = head->next;
        while (fast != nullptr && fast->next != nullptr) {
            fast = fast->next->next;
            slow = slow->next;
        }
        ListNode* tmp = slow->next;
        slow->next = nullptr;
        ListNode* left = sortList(head);
        ListNode* right = sortList(tmp);
        ListNode* res = new ListNode();
        ListNode* cur = res;
        while (right != nullptr && left != nullptr) {
            if (left->val < right->val) {
                cur->next = left;
                left = left->next;
            } else {
                cur->next = right;
                right = right->next;
            }
            cur = cur->next;
        }
        cur->next = left == nullptr ? right : left;
        return res->next;
    }
};
```

# [160. 相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/)

![image-20220822144837779](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822144837779.png)

## 双指针

```C++
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* curA = headA;
        ListNode* curB = headB;
        while (curA != curB) {
            curA = curA == NULL ? headB : curA->next;
            curB = curB == NULL ? headA : curB->next; 
        }
        return curA;
    }
};
```

# [138. 复制带随机指针的链表](https://leetcode.cn/problems/copy-list-with-random-pointer/)

![image-20220822145258552](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822145258552.png)

![image-20220822145315419](C:\Users\85731\AppData\Roaming\Typora\typora-user-images\image-20220822145315419.png)

## 拆分链表

```C++
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if (!head) return NULL;
        //交叉连接到一起
       for (Node* node = head; node != NULL; node = node->next->next) {
           Node* nodeNew = new Node(node->val);
           nodeNew->next = node->next;
           node->next = nodeNew;
       }
        //构造random
        for (Node* node = head; node != NULL; node = node->next->next) {
            Node* nodeNew = node->next;
            nodeNew->random = (node->random == NULL) ? NULL : node->random->next;
        }
        //拆分链表，构建next
        Node* headNew = head->next;
        for (Node* node = head; node != NULL; node = node->next) {
            Node* nodeNew = node->next;
            node->next = nodeNew->next;
            nodeNew->next = (node->next == NULL) ? NULL : node->next->next;
        }
        return headNew;
    }
};
```

## 哈希表

```C++
class Solution {
public:
    unordered_map<Node*, Node*> map;
    Node* copyRandomList(Node* head) {
        if (!head) return NULL;
        if (!map.count(head)) {
            map[head] = new Node(head->val);
            map[head]->next = copyRandomList(head->next);
            map[head]->random = copyRandomList(head->random);
        } 
        return map[head];       
    }
};
```

```C++
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if (!head) return NULL;
        unordered_map<Node*, Node*> map;
        Node* cur = head;
        while (cur != NULL) {
            map[cur] = new Node(cur->val);
            cur = cur->next;
        }
        cur = head;
        while (cur != NULL) {
            map[cur]->next = map[cur->next];
            map[cur]->random = map[cur->random];
            cur = cur->next;
        }
        return map[head];
    }
};
```


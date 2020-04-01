---
layout:     post
title:      [basic]Data Structure
subtitle:   Array & LinkedList
date:       2020-03-31
author:     lmcdhr
header-img: img/LeetCode_Sharing.png
catalog: true
tags:
    - Leetcode
    - Data Structure
    - 九章基础班
---

## Data Structure

```
outline
1. array和linkedlist的介绍以及对比
2. 基本题型以及操作
3. 栗子
```

## 1. 介绍及对比
### 1. array
#### 1. 存储方式：

   内存中连续存储
#### 2. 特点：

   查找快(O(1)),插入删除慢(O(n))；因为查找只需要下标查找，而插入时，插入删除点之后的数据全部需要移位

#### 3. deque：

   阉割版的linkedlist，可以从头尾进行操作，但是存储方式仍是连续的，本质上仍是array，可以理解为强化查找，弱化插入删除的链表
### 2. linkedlist
#### 1. 存储方式：

   内存中分散存储，存储内容包括value和下一个node的地址
#### 2. java中的存储方式：

   java中一切变量全部以object方式存储，虽然一个linkedlist的node，本身数据占四个字节（假设为int型），next的地址占4个字节，但是node这个object本身也占四个字节，这四个字节存储了node本身这个object的地址，因此shallow copy时，即使直接将一个node赋值给另外一个node，只改变了这个被赋值的node object的存储地址，并不改变链表本身的顺序结构以及数据；若要通过node之间的赋值改变一个node的内容，需要deep copy（简单理解为：java中，一个node的value以及next，与node变量本身的地址分开存储，value与next存于heap之中，而node变量存于stack，node变量所存储的地址指向value所在的地址）

   请看下面这个例子，第二次输出的时候，对node1只进行了shallow copy，所以其实改变的只是将node1本身所在的地址改变为了node2，内存中value为1的数据结点仍然存在，head所指向的node仍然是值为1的数据结点，所以输出结果跟第一次一样，仍然是1->2->3->null;

   ![](https://s2.ax1x.com/2020/02/22/3KyXdI.png)
#### 3. 如何改变链表的值？

   1. 改变链表的值：直接复制node.value即可
   2. 改变结构：链表的结构由每一个node单元的next决定，因为next里面存储了其下一个node结点的地址，于是只有改变node.next才可以改变链表结构，单纯的给node赋值，只会在stack里生成一个临时的变量，并且该变量会在函数结束后被销毁

## 2. 基本题型以及操作
### 1. linkedlist
1. 题型：翻转，快慢指针找中点以及环，排序，归并
2. dummy node的使用：一般用于链表改变结构时，设dummy.next为head，如果需要返回head，最后返回dummy.next即可
### 2. array
1. 题型：排序，subarray
2. prefix sum的使用：

     需要求subarray的sum时，使用brute force时间复杂度是n²，于是我们使用一种prefix sum的思路，使得时间复杂度降为n，prefix具体实现：

     1. 求任何一个subarray的sum，即从index为i的点一直加和到j的点，我们可以从推导得知：sum[i,j]= sum[0,j]-sum[0,i-1],即从0至j的和，减去从0至i-1的和
     2. 我们遍历array一次，即可分次获得到所有点的sum，记录这些sum，便可以获得所有可能子数组的sum
## 3. 栗子
### 1. 链表的翻转 
#### leetcode 206. Reverse Linked List
1. 题目：给予一个链表，整个翻转，返回新的head
2. 思路： 经典的链表三步翻转：pre记录左侧点，cur记录其next，temp记录cur的next，翻转cur与pre的关系，pre更新为cur，cur更新为temp，pre从null开始，直至cur为null跳出
3. 代码：
```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head == null) return head;
        ListNode pre = null;
        ListNode cur = head;
        while(cur != null){
            ListNode temp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = temp;
        }
        return pre;
    }
}
```
#### leetcode 92. Reverse Linked List II
1. 题目：给予一个链表head，以及int m&n（m<n），要求翻转第m个node到第n个node之间的链表（包含m及n），返回head
2. 思路：

     我们从head开始，先找到第m个点，随后开始翻转m到n之间的链表，但是需要注意，我们要额外记录第m个点之前的那个点，因为翻转结束后，我们需要将这个点的next置为第n个点（第n个点后面的点因为已经被cur记录，因此不需要额外记录）

     除此之外，本题我们需要使用dummy node，因为如果从head便开始翻转，我们无法记录head之前的那个null，而且使用dummy，最后直接返回dummy.next既是new head，非常方便
3. 代码
```java
class Solution {
    public ListNode reverseBetween(ListNode head, int m, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        int count = 1;
        ListNode cur = head.next;
        ListNode pre = head;
        ListNode first = dummy;
        while(count < n){
            if(count >= m){
                ListNode temp = cur.next;
                cur.next = pre;
                pre = cur;
                cur = temp;
                count++;
            }
            else{
                first = head;
                head = head.next;
                pre = head;
                cur = pre.next;
                count++;
            }
        }
        head.next = cur;
        first.next = pre;
        return dummy.next;
    }
}
```
#### leetcode25. Reverse Nodes in k-Group
https://leetcode.com/problems/reverse-nodes-in-k-group/

类似题型：leetcode 24. Swap Nodes in Pairs
1. 题目：给予一个链表，以及一个数字k，要求链表内从head开始，每k个点为一组进行翻转，如果最后不够k个点，则保留剩下的点不翻转
2. 思路：

      本题思路较为清楚，写一个while循环，每k个点进行内部翻转即可，不够k个点就跳出，注意每组点首尾两端的处理即可
3. 代码：
```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode dummy = new ListNode(0, null);
        dummy.next = head;
        head = dummy;
        while(head != null){
            head = reverseK(head,k);
        }
        return dummy.next;
    }
    
    private ListNode reverseK(ListNode head, int k){
        int count = 0;
        ListNode check = head;
        while(check != null && check.next != null&& count<k){
            check = check.next;
            count++;
        }
        if(k!=count){
            return null;
        }
        count = 1;
        ListNode pre = head.next;
        ListNode start = pre;
        ListNode cur = pre.next;
        while(count < k){
            ListNode temp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = temp;
            count++;
        }
        start.next = cur;
        head.next = pre;
        return start;
    }
}
```
### 2. 求中点
#### leetcode143. Reorder List
https://leetcode.com/problems/reorder-list/
1. 题目： 给予一个链表head，要求重新整理链表，从L: L0→L1→…→Ln-1→Ln变为L0→Ln→L1→Ln-1→L2→Ln-2→…
2. 思路：

      1. 从两头开始变换，最终会在链表的中间结束，所以我们先找到链表的中点，作为结束循环的标志
      2. 我们从尾部，无法向前取用，因此我们需要将mid至tail的部分全部取反，以满足从尾往中间取用的要求
      3. 之后的事情就是从头开始，使用双指针，依次取用头尾元素，直至tail指针到达mid
3. 链表求中间点并获得tail的问题：

      链表有很多需要分别从头尾进行操作的题，而从尾部往头部无法实现，因为我们只有next指针没有pre，所以求中点配合取反的操作在这些题中较为常见；其中求中点的步骤：
      1. 建立两个指针，一快一慢，慢指针一次走一个next，快指针两个next，快指针为null或者next为null 或者next.next为null时，慢指针所在位置即为中点（如果链表有偶数个元素，则所在位置在中线的前一个元素）
      2. 如果链表有奇数个点，那么快指针此时也指向最后一个元素，如果有偶数个，那么fast.next是最后一个点
3. 代码：
```java
class Solution {
    public void reorderList(ListNode head) {
        if(head == null) return;
        ListNode dummy = new ListNode(0,null);
        dummy.next = head;
        
        ListNode slow = head;
        ListNode fast = head;
        
        //find middle and tail
        while(fast != null && fast.next!=null && fast.next.next!=null){
            slow = slow.next;
            fast = fast.next.next;
        }
        if(fast.next != null){
            fast = fast.next;
        }
        
        //reverse from middle to tail
        ListNode pre = slow;
        ListNode cur = pre.next;
        while(cur!=null){
            ListNode temp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = temp;
        }
        slow.next = null;
        
        //get new list
        ListNode start = dummy;
        ListNode p1 = head;
        ListNode p2 = fast;
        while(p2!= slow){
            start.next = p1;
            start = p1;
            p1 = p1.next;
            start.next = p2;
            start = p2;
            p2 = p2.next;
        }
        if(p1 != p2){
            start.next = null;
        }
    } 
}
```
### 3. 链表deep copy 
#### leetcode138. Copy List with Random Pointer
https://leetcode.com/problems/copy-list-with-random-pointer/
1. 题目：给予一个linkedlist，每个node的信息包含val,next以及一个随机的指针random，要求deep copy这个链表
2. 思路：

      本题最简单的思路就是以前的copy graph的思路，建立hash map存储相关对应点的信息，然鹅这样需要一个额外的O(n)空间来存储map，我们能否在const空间内完成呢？
      
      这里引出一个复制链表的O（1）空间办法：
      1. 在每个点的后面，增加一个新的点，例如：1->2->3 变成1->1'->2->2'->3->3'
      2. 每隔一个点进行操作，复制相关信息

      做题的时候，在复制random的时候，如果random指向null，需要额外进行复制，用普通情况下的赋值语句会造成null pointer，具体请看程序代码
3. 代码：
```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/
class Solution {
    public Node copyRandomList(Node head) {
        //case edge
        if(head == null){
            return null;
        }
        
        //copy node val and next
        Node cop = head;
        while(cop != null){
            Node new_node = new Node(cop.val);
            Node temp = cop.next;
            cop.next = new_node;
            new_node.next = temp;
            cop = temp;
        }
        
        //copy random;
        cop = head;
        while(cop != null){
            if(cop.random != null){
                cop.next.random = cop.random.next;
            }
            else{
                cop.next.random = null;
            }
            cop = cop.next.next;
        }
        
        //reform
        Node new_head = head.next;
        while(head != null && head.next.next != null){
            Node temp = head.next.next;
            head.next.next = head.next.next.next;
            head.next = temp;
            head = head.next;
        }
        head.next = null;
        return new_head;
    }
}
```
### 4. 链表排序 
#### leetcode 148. Sort List
https://leetcode.com/problems/sort-list/
1. 题目：给予一个链表head，要求sort这个链表，要求时间复杂度nlogn，空间const
2. 思路：

      排序算法nlogn一共有以下几种，我们分类讨论其优劣性及可行性：

      1. merge sort：时间复杂度nlogn，空间上因为链表的merge不再需要额外的空间，因此可行
      2. quick sort：时间复杂度nlogn~n²（一般认为是nlogn），空间上不需要额外空间，可行，链表中可以将switch的操作简化为从dummy开始重新排列

      整体上思路与数列的排序无异，但是要注意，在找完终点之后，要将中点与后面断开，否则无法形成两个子链表，因此我们也需要先处理右半部分，否则断开中点之后，我们无法再找到右半部分的起始点

3. 代码：merge sort版本
```java
class Solution {
    public ListNode sortList(ListNode head) {
        
        if(head == null || head.next == null){
            return head;
        }
        return mergeSort(head);
    }
    
    private ListNode mergeSort(ListNode head){
        if(head.next == null) 
            return head;
        ListNode mid = findMiddle(head);
        ListNode right = mergeSort(mid.next);
        mid.next = null;
        ListNode left = mergeSort(head);
        return merge(left, right);
    }
    
    private ListNode merge(ListNode left,  ListNode right){
        ListNode dummy = new ListNode(0);
        ListNode temp = dummy;
        while(left!=null && right!=null){
            if(left.val > right.val){
                temp.next = right;
                right = right.next;
            }
            else{
                temp.next = left;
                left = left.next;
            }
            temp = temp.next;
        }
        if(left != null){
            temp.next = left;
        }
        if(right != null){
            temp.next = right;
        }
        return dummy.next;
    }
    
    private ListNode findMiddle(ListNode head){
        ListNode slow = head;
        ListNode fast = head;
        while(fast!=null && fast.next!=null && fast.next.next!=null){
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
}
```
### 5. with trees 
#### leetcode108. Convert Sorted Array to Binary Search Tree
1. 题目：给予一个排好序的array，要求将其转换为一棵BST
2. 思路：

     将BST正序遍历，即可得到一个升序数组，同样的，我们按照根->左->右的顺序进行逆遍历即可，而BST根节点大于左小于右，我们在一段升序数组中，取其中点即可做到这一点
     
     在实际做题中，遇到了一个问题，即递归的退出条件是left==right 还是left>right？按照本题的逻辑，==即可满足条件，没有必要再往下多走一步判断>，然而在本题树的操作中，他会将你的数据结构转化为一个数组来判断对错（类似于之前做过的serilazation of tree），即一个点的子节点必须同时写全，没有的话必须填入null，如果用==，则不会填入null，即当满足left==right的时候，便直接新建结点接入后return了，因此总是被判为错误答案；所以二者虽然都可以保证完整正确的树，但是在本题中一定要记得使用第二个条件
    
3. 代码：
```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        if(nums == null){
            return null;
        }
        return helper(nums, 0, nums.length - 1);
    }
    
    private TreeNode helper(int[] nums, int left, int right){
        if(left > right){
            return null;
        }
        
        int mid = (left + right) / 2;
        TreeNode root = new TreeNode(nums[mid]);
        
        root.left = helper(nums, left, mid-1);
        root.right = helper(nums, mid+1, right);
        
        return root;
    }
}
```
### 6. quick select
#### 4. Median of Two Sorted Arrays
https://leetcode.com/problems/median-of-two-sorted-arrays/
1. 题目：给予两个排好序的array，求他们两个array所组成的大array的中位数
2. 思路：

      本题如果用普通的思路来想，不难想到一个O(n)的解法：从两array的头开始依次打擂台，直至从小到大找到第(m.length+n.length)/2个数（根据总长度奇偶性不同公式不同），即为中位数，但是本题有一种更优解法，即logn的解法，我们知道logn是在O(1)的时间内将O(n)转化为O(n/2)，即使用二分的思想一次解决一半的问题，那么本题中引出一种quick select的算法，专门解决选择第N个数的问题
      
      大致思路为：我们找两个数组总体的中位数，即是在两个升序数组中，从小到大找到第(m.length+n.length)/2个数（接下来我们将这个数记为第k个数），那我们可以每次取当前数列的中点位置，即每个队列取k/2个数，接下来对比这两个数的大小，小的一方说明从起始到这个数位置，都在中位数的左侧，到此我们便完成了第一次二分，找到了前k/2个数（不一定是从小到大前k/2个数，但是一定包含在前k个数中）我们将这个数列的下一次起始位置换为k/2,并将要找的数的数量减去已经找到的k/2，开始下次循环

      大致思路并不难，即两个数列交替寻找所要找的数的数量一半个数，但是两个数列的长度可能有很多可能，我们根据不同情况讨论一下递归的退出条件以及处理等

      1. 递归的退出：
           1. 正常退出，每次取一半，最后一定剩一个，因此k==1时，取两个指针指向的数较小的那个，即为中位数
           2. 某个数列到头了，即指针>=array.length，这是我们只要在另一个数列中找到start index+k-1个数即可
      2. 递归的拆解：
        
           由上述思路，分别取两个数列中对应start index的数，对比大小，小的一方更新对应的start index并更新k
      3. 特殊情况的处理：
            
           前文我们说过，如果一个数组很长，一个数组很短，甚至不足我们取出第k/2个数，怎么处理？我们知道我们每处理一次，k会变为k-k/2（注意：由于java除法的问题，不可以直接使用k/2），由数学推导可知，一个树一直减去他的二分之一，最后一定会出现剩1的情况，也就是说无论短的数组有多短，最后一定会处理得到，因此我们只需要在该数组长度不够k/2时，处理另一个数组即可，在我的解法中，我设置了两个int变量，设为Integer.Maxvalue，如果长度不够k/2则不更新，因此在接下来的判断中，如果没有更新，则可以达成处理另外一个数组的需求

3. 代码：
```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int m = nums1.length;
        int n = nums2.length;
        if((m+n) % 2 == 0){
            return (double)((double)findMedian(nums1,nums2, 0, 0, (m+n)/2) + (double)findMedian(nums1,nums2, 0, 0, (m+n)/2+1))/2;
        }
        else{
            return (double)findMedian(nums1, nums2, 0, 0, (m+n)/2 + 1);
        }
    }
    
    private int findMedian(int[] nums1, int[] nums2,  int p1, int p2, int k){
        if(p1 >= nums1.length){
            return nums2[p2 + k - 1];
        }
        if(p2 >= nums2.length){
            return nums1[p1 + k - 1];
        }
        if(k == 1){
            return Math.min(nums1[p1], nums2[p2]);
        }
        
        int A = Integer.MAX_VALUE;
        int B = Integer.MAX_VALUE;
        
        if(p1 + k/2 - 1 < nums1.length){
            A = nums1[p1 + k/2 - 1];
        }
        if(p2 + k/2 - 1 < nums2.length){
            B = nums2[p2 + k/2 - 1];
        }
        
        if(A<B){
            return findMedian(nums1, nums2, p1+k/2, p2, k - k/2); 
        }
        else{
            return findMedian(nums1, nums2, p1, p2+k/2, k - k/2);
        }
    }
}
```
### 7. prefix sum
#### leetcode 53. Maximum Subarray
https://leetcode.com/problems/maximum-subarray/
1. 给予一个array，求一个子数组，使其和在所有子数组中最大，并return这个和
2. 思路：

     子数组求和，一看便知是prefix sum的问题，我们知道一个子数组的和sum[i,j]=sum[0,j]-sum[0,i],因此我们只需要使得sum[0,j]尽量大，sum[0,i]尽量小即可；因此我们写一个for循环，从头至尾，每次加和的结果便是sum[0,j]，根据大小关系更新得出的最大结果max，以及sum[0,i]的min，看起来简单，我们如何初始化并更新这几个值呢？
     
      1. 初始化：
           1. sum：从头开始，每次更新，毋庸置疑为0
           2. min：min值是sum[0,i]所得的最小值，一开始的时候相当于i=0，即一个数都不取，根据这一特性，我们初始化为0
           3. max：max是我们最终要求的的值，每次循环对比更新，而数组中的数有正有负，我们应该初始化为Integer.Min_Value使其第一次循环便能正确更新
      2. 更新：
           1. sum: 每次循环将num[i]加进去即可

           进行下一步之前，出现了一个问题，先更新max还是min？

           为了解决这个问题，我们可以从初始化中得到答案，我们将max初始化为minvalue，意为循环执行之前还没有得出max，而min置为0，意为我们已经默认了当前的min是一个树都不取，即为0，因此我们可知第一次循环的时候，min值已经设定好，可以直接利用，而max却没有，需要每次循环中得到，
           2. max：对于当前位置，有两个选择：当前的max或者更新了sum之后的sum-min，选择大的更新 
           3. min：由上面的分析我们可知，min其实是为了下次循环更新的，因此我们有两个选择：当前的min或者新生成的sum，选择小的更新

3. 代码：
```java
class Solution {
    public int maxSubArray(int[] nums) {
        int sum = 0;
        int min = 0;
        int max = Integer.MIN_VALUE;
        for(int i=0; i<nums.length; i++){
            sum += nums[i];
            max = Math.max(max, sum-min);
            min = Math.min(min, sum);
        }
        return max;
    }
}
```
#### leetcode 560. Subarray Sum Equals K
https://leetcode.com/problems/subarray-sum-equals-k/
1. 题目：给予一个数组，给予一个整数k，求出数组中所有加和=k的连续子数组的数量
2. 思路：

      子数组加和问题，继续使用prefix sum，但是本题要我们求出所有子数组的数量，于是我们不仅需要记录prefix sum，还要记录其数量，比如我们求和为9，我们当前到达的位置的sum是14，我们只要查找出sum是5的prefix sum有多少个，就可以求出以当前位置为结尾的和为9的子数组有多少个了，因此我们需要借助hash map来完成这一纪录，大概的思路为：
      1. 生成一个hash map，key值为对应的sum，value为该sum出现的次数
      2. 从头开始循环，每次循环更新sum
      3. 查找map中是否存在相应的k-sum，如果存在，结果中加上该k-sum出现的次数（key为k-sum的value）
      4. 在map中更新该sum的次数
3. 代码：
```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        if(nums == null || nums.length == 0)
            return 0;
        Map<Integer, Integer> sum = new HashMap<Integer, Integer>();
        sum.put(0,1);
        int count = 0;
        int temp = 0;
        for(int i=0; i<nums.length; i++){
            temp += nums[i];
            if(sum.containsKey(temp - k)){
                count += sum.get(temp-k);
            }
            sum.put(temp,sum.getOrDefault(temp,0)+1);
        }
        return count;
    }
}
```
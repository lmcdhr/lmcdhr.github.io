---
layout:     post
title:      九章算法基础班：Data Structure II
subtitle:   Hash & Heap
date:       2020-04-04
author:     lmcdhr
header-img: img/LeetCode_Sharing.png
catalog: true
tags:
    - Leetcode
    - Data Structure
    - 九章基础班
---

## Data Structure II Hash & Heap

```
outline
1. hash的实现原理及性质：hash function， collision， rehashing
2. heap的原理及性质：priorityqueue，compare function
3. 一些力扣栗子
```
## 1. Hash
#### 1. hash table, hash map与hash set
1. Hash Set: 实际是set，只存储key，只能查询是否存在某个数据
2. Hash table 与 Hash Map: 二者都存储key value pair，但是hash table是多线程安全，因此使用多线程访问的时候需要使用hash table



#### 2. Hash的实现原理：Hash Function
1. hash function的作用：将一个key转换为一个对应且无规律的0~capacity-1的数字
2. hash function的算法栗子：

![](https://s1.ax1x.com/2020/03/22/84YTmD.png)

3. 注意：因为hash function的算法复杂度是O(size of key)，而且hash table的插入，删除，查找操作必须要经过hash function，**因此不可以简单地认为hash的时间复杂度是O(1)**,这个需要由key的复杂程度确定，尤其是使用string作为key的时候
4. magic number 31：我们使用hash function时，经常每一位*31，这个31由经验而来，效率最好（太大影响速度，太小冲突太多）



#### 3. 冲突collision问题的解决办法
1. closed hashing：
     1. 冲突时，依次查找下一个位置，直至找到一个空的位置插入
     2. 缺点：当删除一个元素时，该位置会被标记为deleted，在查找时，遇到deleted会向下进行，但是这样会导致如果deleted过多，查找时可能只存在几个有效数据，但是有几千个deleted，而且每个deleted都要判断一次，造成步骤过多，浪费时间
     3. 因为上述缺点，因此closed hashing未被大量使用
2. open hashing：
     1. 冲突时，共用一个位置，但是由链表实现内部数据的连接，key所在的位置实际是一个dummy node，插入数据时，从头插入（越新的数据越靠近头）
     2. 缺点：由于链表的特性，过多数据积累与一个位置时，访问速度受限



#### 4. rehashing
1. 定义：当hash的size不够，无法满足数据的capacity时，进行的操作，将hash进行扩充
2. 实现：将size扩充一倍，类似于vector
3. 如何定义hash满了？（何时扩充？）：hash的饱和度：实际存储元素个数/总大小，当饱和度>= 0.1（经验值）,说明需要rehashing
4. 缺点： 需要for循环所有已有的位置，因此应该尽量避免rehash，所以我们使用hash的时候，应该根据我们的需求事先定义一个size，从而避免rehashing

## 2. Heap
#### 1. Heap的实现原理
1. Heap的本质:一个二叉树，且根据堆的不同（最大堆，最小堆），父节点比两个子节点都大/小
2. Heap的常用操作以及时间复杂度：
    1. 增add：向二叉树中增加一个值，需要重新heapify，时间复杂度logn
    2. 删remove：向二叉树中删除一个值，也需要heapify，时间复杂度logn
    3. 推出poll：推出顶部元素（最大值或最小值），实际为直接推出root，然后根据左右结点关系重新更换root，时间复杂度O(1)



#### 2. Heap的API：PriorityQueue

1. 定义：Queue<?> queue = new Priority<?>();
2. comparator函数的使用（重载）
     1. PriorityQueue自动生成一个最小堆，如果需要一个最大堆，或者根据数据类型的不同替换大小对比关系，则需要重新定义comparator函数，这里给出一个栗子：

```java
class Solution {
    
    // 定义一个新的comparator：pointComparator，输入是int[]类型，返回数组中前两个元素平方和较大的一个
    private Comparator<int[]> pointComparator = new Comparator<int[]>(){
        public int compare(int[] left, int[]right){
            return (right[0]*right[0]+right[1]*right[1])-(left[0]*left[0]+left[1]*left[1]);
        }
    };
    
    // 使用新的comparator定义一个priority queue，会按照之前规定的大小顺序来生成堆，例如：[1,3]会小于[2,2]
    public void test(int K) {
        Queue<int[]> queue = new PriorityQueue<int[]>(K,pointComparator);
    }
```

## 3. 栗子
### 1. Hash设计类
#### 亚麻必考题 leetcode 146. LRU Cache
1. 题目：设计一个指定size的LRU cache（least recent use cache），即size满了之后，删除上次使用离现在最久的元素，设计两个操作，一个put（put一对值，key和value），一个get（通过key获取value），要求两者的时间复杂度是O(1)

2. 思路：

      LRU并不难设计，本题一考点在于如何设计两个操作使得其操作时间为const，这道题我当初思考的时候，分为两部分：
      1. put元素，即往存储过去元素的数据结构中，加一个新的元素的时间复杂度为const，而且因为size的限制会存在删除操作，也可以说这个数据结构的增和删必须是O(1)，那么可以排除array以及int[]，选择linkedlist；
      2. get元素，即查找一个元素，时间复杂度为const，那么只有hash，且hash的key值不能为string，需要是int；

      由此，本题的思路明了，即用hash map作为主存储，每一个hash的key是输入中的key，value是一个linkedlist的node，该链表应该设计为双向链表，因为我们删除操作需要一个点的之前一个点与后面的点连接，使用双向链表比较方便，当然单向也可以，只是操作略微不同，每个node中，我们存储key，value值，前一个点pre和后一个点node；当查找时，我们只需要找到map中的key对应的node，并获得node的value即可

      get的操作比较简单，但是put的操作较为繁琐，几种情况及对应做法如下：

      1. 已经有了一个包含该key的点：根据题意更新value，并将这个点从当前位置挪到末尾
      2. 输入的是新value，且size还没有达到上限：加入新的点即可，随后在双向链表末尾加入该点
      3. 输入是新value，且size已经达到上限：将第一个点删除（这里可以使用设置dummy node来简化操作），将新的点添加于末尾

      这里还有一种特殊情况，即例如size limit为1，那么只会存在一个node，我们在上述第三种情况下，将当前点删除的时候，会有一个语句：node.next.pre = node.pre，但是node.next=null，没有pre，会造成null pointer exception，因此做这一步之前我们需要先判断一下node.next是否为空，是的话就不用再进行这一操作
      
3. 引申

      java中有一个数据结构LinkedHashMap,既是hash+双向链表的组合，本题中我们实现的便是这一数据结构，以后可以直接使用，方便写代码
      
4. 代码：



```java
public class LRUCache {
    
    public class ListNode{
    public int key;
    public int val;
    public ListNode next;
    public ListNode pre;
    public ListNode(int key, int val){
        this.key = key;
        this.val = val;
        this.next = null;
        this.pre = null;
    }  
}

    private Map<Integer, ListNode> map;
    private ListNode dummy;
    private ListNode tail;
    private int size;
    private int capacity;
    
    public LRUCache(int capacity) {
        this.map = new HashMap<Integer, ListNode>();
        this.dummy = new ListNode(-1,-1);
        this.tail = dummy;
        this.size = 0;
        this.capacity = capacity;
    }

    public int get(int key) {
        if(!map.containsKey(key)){
            return -1;
        }
        else{
            ListNode temp = map.get(key);
            moveToTail(temp);
            return temp.val;
        }
    }
    

    public void put(int key, int value) {
        if(map.containsKey(key)){
            ListNode temp = map.get(key);
            temp.val = value;
            moveToTail(temp);
        }
        else if(size < capacity && !map.containsKey(key)){
            ListNode node = new ListNode(key,value);
            tail.next = node;
            node.pre = tail;
            tail = node;
            map.put(key, node);
            size++;
        }
        else{
            map.remove(dummy.next.key);
            dummy.next = dummy.next.next;
            if(dummy.next != null)
                dummy.next.pre = dummy;
            
            ListNode node = new ListNode(key, value);
            tail.next = node;
            node.pre = tail;
            tail = node;
            map.put(key, node);
        }
    }
    
    private void moveToTail(ListNode node){
        if(node == tail || node.next == null){
            return;
        }
        
        node.pre.next = node.next;
        node.next.pre = node.pre;
        
        node.pre = tail;
        tail.next = node;
        tail = node;
    }
}
```

### 2. heap的维持
#### leetcode 264. Ugly Number II
1. 题目: 求第n个ugly number，ugly number是一类数字，他们的质因数只可能是2，3，5

2. 思路：

      ugly number不难求，只需从1开始，依序乘2，3，5即可得到他的下一个ugly number，由此往复，即可得到无数个ugly number。但是我们不能保证得到的ugly number是从小到大的，比如我们从1开始，分别获得了2，3，5，随后2分别乘，得到了4，6，10，3再乘的时候，得到6，9，15，但是6和9就比先得到的10小，而10因为2比3优先的关系，先得出了。因此我们引入堆来解决这个问题，每次循环做乘法的时候，现将得到的数置入堆，然而再pop出最小的元素，循环到第n次的时候，就可获得第n个ugly number了

      本题中，因为数字增长很快，因此我选用了long来进行操作，这是为了防止溢出，虽然程序的输出是int，但是我们之前提到了，堆中的数字可能会比当前输出的数字大，因此有可能超出了int的范围，所以使用long是严谨且必须的

3. 代码：



```java
class Solution {
    public int nthUglyNumber(int n) {
        Set<Long> ans = new HashSet<Long>();
        Queue<Long> queue = new PriorityQueue<Long>();
        Long[] primes = new Long[3];
        primes[0] = Long.valueOf(2);
        primes[1] = Long.valueOf(3);
        primes[2] = Long.valueOf(5);
        
        ans.add(Long.valueOf(1));
        queue.add(Long.valueOf(1));
        for(int i=0; i<3; i++){
            ans.add(primes[i]);
            queue.add(primes[i]);
        }
              
        Long base = Long.valueOf(1);
        for(int i=0; i<n; i++){
            base = queue.poll();
            for(int j=0; j<3; j++){
                Long temp = base * primes[j];
                if(ans.contains(temp)){
                    continue;
                }
                queue.add(temp);
                ans.add(temp);
            }
        }
        
        return base.intValue();
    }
}
```



#### 维持size = k的堆 leetcode 215. Kth Largest Element in an Array

https://leetcode.com/problems/kth-largest-element-in-an-array/
1. 题目：给予一个数组（无序），要求求出数组内第k大的数

2. 类似题型：high five（亚麻高频题），973. K Closest Points to Origin

3. 思路：

      本题是较为经典的维持一个size为k的堆，具体做法为：循环遍历，像堆中增加新的元素，当size<k时，直接添加，如果size超过k，则对比最小（或最大）元素和当前要加入的元素，poll两者中更小（更大）的那个，即可维持堆的size保持不变

      leetcode讨论区中，有的解法时在size超过k时，先将元素压入堆，再poll，这样虽然代码简单，但是却多了一步不必要的add，即如果要加入的元素比堆顶元素小，则该元素不应该被置入堆中，而add操作的时间复杂度logn，获取堆顶元素的时间复杂度是1，这样如果我们无脑add，就会在这种不必要的情况下增加时间消耗，会显得我们代码优化不好，因此不要这么写



4. 代码：



```java

class Solution {
    public int findKthLargest(int[] nums, int k) {
        Queue<Integer> num = new PriorityQueue<Integer>();
        int res = 0;
        for(int i: nums){
            res = add(i, num, res, k);
        }
        return res;
    }
    
    private int add(int i, Queue<Integer> num, int res, int k){
        if(num.size() < k){
            num.add(i);
        }
        else if(i>num.peek()){
            num.poll();
            num.add(i);
        }
        return num.peek();
    }  
}

```





### 3. 堆与其他算法的对比



#### 时间空间的取舍 leetcode 23. Merge k Sorted Lists
1. 题目：给予k个sorted linkedlist，要求将其整合为一个sorted linkedlist

2. 思路：

      本题有多种思路，可以简化为两种（我们假设链表长度为n）：
      1. 使用堆，将每个list的头置入堆，每次选出最小的那个，并在最小node对应的list中找到下一个点置入堆（这里我选择堆里面存node而不是value，override compare函数即可，如果存值得话，不方便找下一个点），建立dummy node来新建链表，直至堆poll到空，return dummy.next；这种做法时间上nlogk，空间上k
      2. 使用分治，每两个链表合并一次，之后再将合并后的链表两两合并，直至只剩一个链表。这种做法时间上nk，空间上1

      一般类似题型都可以使用这两种做法，使用堆时间上比较好，但是需要额外的空间，根据要求使用不同做法即可

3. 代码：



```java
class Solution {
    
    private Comparator<ListNode> listComparator = new Comparator<ListNode>(){
        public int compare(ListNode left, ListNode right){
            return left.val - right.val;
        }
    };
    
    public ListNode mergeKLists(ListNode[] lists) {
        if(lists == null || lists.length == 0){
            return null;
        }
        Queue<ListNode> queue = new PriorityQueue<ListNode>(lists.length, listComparator);
        for(ListNode list: lists){
            if(list != null){
                queue.add(list);
            }
        }
        
        ListNode dummy = new ListNode(0);
        ListNode tail = dummy;
        
        while(!queue.isEmpty()){
            ListNode temp = queue.poll();
            if(temp.next != null){
                queue.add(temp.next);
            }
            tail.next = temp;
            tail = temp;
        }
        
        return dummy.next;
    }
        
}
```

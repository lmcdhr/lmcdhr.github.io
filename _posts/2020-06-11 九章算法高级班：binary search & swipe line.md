---
layout:     post
title:      九章算法高级班：binary search && swipe line 
subtitle:   binary search & swipe line
date:       2020-06-11
author:     lmcdhr
header-img: img/LeetCode_Sharing.png
catalog: true
tags:
    - Leetcode
    - binary search
    - swipe line
    - 九章高级班
---

## binary search and swipe line

## 1. 二分法的高级解题

#### 1. 值域二分

1. 总体思想：

    之前的二分法都是基于数据的范围搜索一个答案，而本次的题型中，我们将对答案可能的值域进行二分，本质与之前的二分搜索并无区别，但是题目更为具体，也更为隐晦

2. 思考路径：

    这类问题较为难以想到是二分，二分往往作为一个优化的途径，因此面试可能需要面试官cue一下，这种优化一般是将时间复杂度从n²降为nlogn（更准确的说法是降为nlogk,k为答案的值域范围）；这类问题的大概解题思路为：确定答案的可能范围，进行二分，一般判断条件是一个独立的函数，用来判断答案在当前mid值上是否可行，再根据独立函数返回的值进行不同操作

## 2. 扫描线

#### 1. 解决的问题

   解决一类在起始和终止条件内发生某件事类的问题，例如一点开始会议，四点结束会议，多个会议多个时间求空余时间等等；这类问题一般会有多个同类型事件，每个事件有一个起始点和一个终止点，这是我们使用扫描线解决

#### 2. 解题思路

1. 事件的分解：将事件按照起始和终止分解，大致为：[事件，起始，终止]->[事件，起始]+[事件，终止]，分解后按照时间点的先后顺序来处理
2. 处理的顺序：一般按照事件发生的时间，事件本身的一些性质（重要程度等等）来进行处理，我们需要根据不同的要求来重载compare函数，随后根据使用的数据结构进行排序

## 3. Deque

#### 1. 底层实现

deque是queue的子接口，也是collections当中的一类线性数据结构

#### 2. 一些函数

1. pollLast()/pollFirst():推出deque尾/头部的第一个元素
2. addLast()/addFirst():在deque的尾/头部加入一个元素
3. peekLast()/peekFirst():返回deque尾/头部的第一个元素
4. 其他queue中可以使用的函数，deque都可以使用（毕竟是子类）

## 4. 栗子

### 1. 答案值域二分

#### lintcode 183. wood cut

https://www.lintcode.com/problem/wood-cut/description

1. 题目：

    给予一个数组，代表一堆木头中每块木头的长度，再给出一个整数k，现按照某种长度切分每块木头，要求能切出k块该长度的木头，并求出这个长度的最大值

2. 思路：

     这一题最直接的思路是暴力解法，时间复杂度n²，此时要求我们进行优化，时间复杂度nlogn，需要一个时间复杂度logn的算法，由此想到二分；那么如何进行二分？二分的精髓在于每次循环，将搜索的范围降低一半，本题中，我们可以搜索的只有切块的长度，那么我们可以继续思考得到搜索的范围：切木头的时候，可能切出的最短长度是1，最长长度是所有木头长度的最大值（当然如果只有一个最长木头，此时k应该为1），而答案一定落在这个区间之内，那么我们只需要每次对这个值域进行二分，然后通过另外一个函数判断当前长度是否可以切出k块，就可以逐步逼近答案了

     对于这个判断的独立函数，也很简单，我们已经通过二分得出了每次需要判断的长度，只要将每块木头除以这个长度，正数部分加和就是可以切出的块数了。由此我们得出解题办法，时间复杂度是nlog(max-1),n是数组的长度（有多少块木头），max是数组中的最大值（最长的木头）

3. 代码：

```java
public class Solution {
    /**
     * @param L: Given n pieces of wood with length L[i]
     * @param k: An integer
     * @return: The maximum length of the small pieces
     */
    public int woodCut(int[] L, int k) {
        // write your code here
        int max = 0;
        for(int l: L){
            max = Math.max(l,max);
        }
        if(max == 0){
            return 0;
        }
        
        int left = 1;
        int right = max;
        int res = 0;
        while(left <= right){
            int mid = left + (right-left)/2;
            int temp = getPiece(L,mid);
            if(temp>=k){
                res = mid;
                left = mid+1;
            }else{
                right = mid-1;
            }
        }
        
        return res;
    }
    
    private int getPiece(int[] L, int length){
        int count = 0;
        for(int l: L){
            count += l/length;
        }
        return count;
    }
}
```

#### 类似题型：lintcode 437. copy books

https://www.lintcode.com/problem/copy-books/note/220584

1. 题目：

    给予一个数组，代表抄写每本书所需要的时间，现在分配k个人抄书，每个人可以抄写数组内连续相邻的若干本书，要求求出抄完这些书所需的最短时间

2. 思路：

    这道题跟上道题思路类似，区别在于一个求最大，一个求最少；而答案的搜索范围是：最短时间是书本中时间的最大值，即即使每个人抄一本书，最短也得花费所需时间最长的书的时间来完成；而最长时间是所有书本时间的加和，即一个人抄完所有书；我们基于这些即可完成代码，时间复杂度nlog(sum-max)

    不过本题的判断函数有一个需要注意的点，那就是因为题目给出条件说每个人抄写数组内连续的几本书，所以我们使用贪心算法即可求出是否规定的时间限度内可以完成任务，如果条件改为可以不连续，那么该题的解法就要改变了

3. 代码：

```java
public class Solution {
    /**
     * @param pages: an array of integers
     * @param k: An integer
     * @return: an integer
     */
    public int copyBooks(int[] pages, int k) {
        // write your code here
        int sum = 0;
        int max = 0;
        for(int page: pages){
            sum += page;
            max = Math.max(max, page);
        }
        
        int left = max;
        int right = sum;
        while(left+1 < right){
            int mid = left+(right-left)/2;
            if(canFinish(pages, mid,k)){
                right = mid;
            }else{
                left = mid;
            }
        }
        
        int res = canFinish(pages, left, k)?left:right;
        return res;
    }
    
    private boolean canFinish(int[] pages, int limit, int k){
        int count = 0;
        int time = 0;
        for(int page: pages){
            if(time+page>limit){
                time = 0;
                count += 1;
            }
            time += page;
        }
        return count+1<=k;
    }
}
```

#### 小数精度二分 leetcode 644. Maximum Average Subarray II

https://leetcode.com/problems/maximum-average-subarray-ii/

1. 题目：

    给予一个数组以及一个长度k，找出一个长度大于等于k的连续子数组，并且这个子数组中元素的均值为所有可能的子数组中最大，并返回这个最大的均值

2. 思路：

    求子数组可以使用双指针，本题使用双指针，需要对子数组长度从k到数组.length开始遍历，每次遍历所有可能的连续子数组，更新最大均值，时间复杂度n²，这里我们使用二分法进行优化

    首先寻找范围，一个子数组最大的均值范围，肯定在数组的最大值和最小值之间；随后我们再确定判断函数：对于每个mid，我们需要得出是否存在一个长度大于等于k的连续子数组，使其均值大于等于mid，而这个判断函数可以利用我们之前写过的subarray sum一题，那道题便是利用prefix sum求出一个最大的连续子数组；本题中我们不需要真正求出这个子数组，而是判断是否可行，我们只需要得出一个prefix sum，使其大于mid（average）*length即可，即代表确实有一个数组可以获得该均值。我们可以继续进行一点小优化，即prefix sum每步求sum的时候，我们不是直接将数组的元素加和，而是先将数组元素减去average（mid），再进行操作，这样我们只要求出一个prefix sum>0即可说明满足条件了，这样我们可以摆脱k的限制，否则每次还要求出子数组的长度来进行判断

    本题中的判断函数其实可以单独作为一个小题，求判断是否存在一个均值大于等于avg且长度大于等于k的连续子数组，之前我们做subarray sum的时候只对大小进行了限制而没有对长度进行限制，而如果要对长度进行限制，只需要先预前k-1个元素，随后在这k-1个元素的基础上进行处理即可，而且min也不再单纯是Math.min(min,sum[0 ~ i])， 而是因为长度的限制，变为了Math.min(min,sum[0 ~ i-k])以保证获取的prefix sum长度至少为k

    本题因为是double类型，因此我们二分的while函数需要更改为right-left>一个精度要求，这里是六位小数，这是double或float类二分的常见操作，面试里这个精度需要根据面试官要求进行更改

3. 代码：

```java
class Solution {
    public double findMaxAverage(int[] nums, int k) {
        if(nums == null || nums.length == 0 || nums.length < k){
            return 0;
        }
        
        double arrayMax = Integer.MIN_VALUE;
        double arrayMin = Integer.MAX_VALUE;
        for(int num: nums){
            arrayMax = Math.max(num, arrayMax);
            arrayMin = Math.min(num, arrayMin);
        }
        
        while(arrayMax - arrayMin > 1e-6){
            double mid = (arrayMax+arrayMin)*0.5;
            if(hasAverage(nums, k ,mid)){
                arrayMin = mid;
            }else{
                arrayMax = mid;
            }
        }
        return arrayMin;
    }
    
    private boolean hasAverage(int[] nums, int k, double target){
        double sum = 0;
        double prefix = 0;
        double min = 0;
        for(int i=0; i<k; i++){
            sum += nums[i] - target;
        }
        if(sum >= 0)
            return true;
        for(int i=k; i<nums.length; i++){
            sum += nums[i] - target;
            prefix += nums[i-k] - target;
            min = Math.min(min,prefix);
            if(sum - min >= 0)
                return true;
        }
        return false;
    }
}
```

### 2. 扫描线

https://leetcode.com/problems/meeting-scheduler/

#### 1. 老亚麻扫描线了 leetcode 1229. Meeting Scheduler

1. 题目：

    给予两个人一天内空闲时间的开始和结束，现在要在两个人的共同空闲时间举行一场长度为k的会议，求出最早的一个时间区间，使得会议可以进行

2. 思路：

    一看题目就知道老亚麻，老扫描线了，简单介绍扫描线问题的解题思路：

    1. 一般我们会定义一个新的slot类，存储事件，包括事件发生的时间（注意，我们已经将一个事件拆解成了一个起始事件和一个终止时间，这里记录的时间只是二者之一，并不知道是开始还是结束）；一个boolean或其他类型变量，记录是开始还是结束；以及其他信息，例如下一题中我们需要记录楼的高度等等。 更重要的，我们需要定义compare函数，用来定义处理事件的先后顺序，这里我们需要根据题目来思考，是先处理开始，还是先处理结束，同一时间的事件如何处理等等，下一题我们便可以看到一个比较复杂的compare函数思考过程
    2. 随后我们遍历给的slot，将时间分解，置入队列，随后使用我们定义好的compare函数进行排序
    3. 我们再逐一取出小slot，根据题目要求进行处理

    回到本题，根据这三点，我们开始解题：
    1. 我们先定义一个slot类，需要存储的是空闲时间的开始或结束时间，以及一个boolean变量来区别是开始还是结束，定义compare函数，根据事件发生时间的先后顺序排序
    2. 开始拆解，如果是开始时间，boolean类型为true，如果是结束时间，为false，随后加入array中冲寻排序
    3. 逐一取出事件，定义一个整型count来记录我们满足空闲时间的人数，一个start和end来记录时间段：如果取出的时间是开始时间，那么count+1，更新start；如果是结束时间，count-1，更新end；如果count==2， 那么说明两个人在目前的时间段内都有空闲，我们再判断目前的end-start是否大于要求的k，即可判断出当前时间段是否满足开会的时间长度要求，如果满足，记录结果即可

    其实对于上述第三点，本题还有需要思考的地方，即如何更新start，end以及如何判断是共同空闲时间：因为我们要求出一段共同的空闲时间，那么必须保证两个人都在这段时间之前就start，而且这段空闲时间会随着一个end事件的到来而结束，因此我们必须每次遇到start事件更新start，来保证获得最新的start；比如我们有四个输入，[20，true]，[40,true],[50,false],[70,false],k=20；我们可以看出真正二者都空闲的时间是40-50这段时间，也就是说我们需要一直更新最新的start，等到count==2时说明共同空闲时间开始，遇到一个end事件的时候说明共同空闲时间结束；这个例子中遇到50，false这一事件时，因为之前的两个true，count==2，说明共同空闲时间在start=40开始，但是start=40,end=50,无法满足k的20，因此还需要继续向下遍历判断

3. 代码：

```java
class Slot{
    
    int start;
    boolean isStart;
    
    public Slot(int start, boolean isStart){
        this.start = start;
        this.isStart = isStart;
    }
    
    public static Comparator<Slot> slotComparator = new Comparator<Slot>(){
        public int compare(Slot slot1, Slot slot2){
            return slot1.start - slot2.start;
        }
    };
}

class Solution {
    public List<Integer> minAvailableDuration(int[][] slots1, int[][] slots2, int duration) {
        
        List<Integer> res = new ArrayList<Integer>();
        if(slots1 == null || slots2 == null || slots1.length == 0 || slots2.length == 0)
            return res;
        
        List<Slot> slots = new ArrayList<Slot>();
        for(int[] slot: slots1){
            slots.add(new Slot(slot[0],true));
            slots.add(new Slot(slot[1],false));
        }
        for(int[] slot: slots2){
            slots.add(new Slot(slot[0],true));
            slots.add(new Slot(slot[1],false));
        }
        Collections.sort(slots, Slot.slotComparator);
        
        int start = -1;
        int end = -1;
        int count = 0;
        
        for(Slot slot: slots){
            if(slot.isStart == true){
                start = slot.start;
                count += 1;
            }else{
                end = slot.start;
                if(count > 1 && end-start >= duration){
                    res.add(start);
                    res.add(start + duration);
                    return res;
                }
                count -= 1;
            }
        }
        
        return res;
    }
}
```

#### 2. 老倒霉蛋了 leetcode 218. The Skyline Problem

https://leetcode.com/problems/the-skyline-problem/

咖儿说面试面到这道题的人都是老倒霉蛋了，那么就祝大家都面到这道题（除了我）#doge

1. 题目

    给予一堆长度为3的数组，3个元素分别代表：一个楼的高度，楼的起始坐标，终止坐标，且每个大楼都视为矩形，现在要求绘制出所有大楼的轮廓图，返回格式是一堆长度为2的数组，两个元素代表：一个高度，这个高度开始的坐标

2. 思路：

    这道题也是明显的扫描线思路，我们还是吧一个楼的slot分成两部分：一个楼的开始以及结束，需要存储的元素：楼的高度，事件的时间，是起始还是终止

    这道题不同之前，还需要处理楼的高度，我们如果想绘制一群楼的outline，那么该轮廓肯定是每个坐标上可获得的楼的高度的最大值，我们可以维护一个最大堆来满足这个需求，我们先在堆中置入一个0，代表没有楼的时候轮廓的高度为0，每当我们获得一个新的事件，如果是楼的结束，那我们在堆中remove掉这个高度，如果是楼的开始，那么我们在堆中add这个高度；每次遇到一个新的事件，我们先根据开始还是结束处理高度，随后查看是否需要更新答案：这个查看是通过比对堆的新peek与操作之前的堆的peek是否相同来完成的，如果peek值相同，说明要么新加入的高度并没有比之前的最高高度更高，要么说明删除的元素不是目前最高高度的楼的高度，也就是说目前的最高高度可以继续保持，不用更新我们的答案；如果peek发生了变化，那么就说明我们的outline发生了变化，要么是高楼被删了，要么加入了一个更高的楼，我们都需要将新的peek，配合事件发生的时间，更新入我们的答案中

    现在我们处理完了slot类和对数据的后续处理，也就是之前三点思路的1和3，那么compare函数如何写呢？我们有三个需要排序的，楼的高度，楼的起始还是结束，以及事件的时间；时间肯定根据先后，作为最先考量，高度方面也肯定是先处理比较高的；那么代表起始结束的boolean变量自己，以及高度和boolean变量之间如何排序呢？我们不妨假设一个时间点上有4个事件，两个开始，两个结束，新加入的楼有一个是比较更高的，即目前的peek为10，时间点为20，四个事件为[10,20,false],[7,20,false],[9,20,true],[8,20,true]，栈中元素有：10，7，0；在这一时间点结束后，我们不难发现应该更新答案为[9,20],代表从20这个时间点开始，最高高度变为9；如果我们先处理楼的结束，那么根据高低原则，我们会先处理[10，false]，将10remove之后peek发生改变，变为7，我们新加入了答案[7,20]，但是这是不对的，于是我们大胆推测应该先处理楼的开始，因为先处理结束，有可能使得最高的楼被remove，从而有可能让答案错误的更新；那么我们是先处理高度还是boolean变量呢？我们再做一次实验，如果先处理高度，那么被处理的还是[10,20,false]，还是10被remove，堆中peek更新为7，又出现了错误答案，因此我们可以继续推测应该先处理boolean变量而非高度

    由此，我们的compare顺序定了下来，即：时间（先后）>事件的起始结束boolean变量（且先处理起始时间）>楼的高度（且先处理高的）；leetcode答案里有一个将height的值根据是起始还是结束分别变为正数或者负数，代码也比较简洁，但是我认为这个写法其实掩盖了上述关于boolean变量和height处理顺序的思考过程，并不是很好

3. 代码：

```java
class Edge{
    
    int start;
    boolean isStart;
    int height;
    
    public Edge(int start, boolean isStart, int height){
        this.start = start;
        this.isStart = isStart;
        this.height = height;        
    }
    
    public static Comparator<Edge> edgeComparator = new Comparator<Edge>(){
        public int compare(Edge edge1, Edge edge2){
            if(edge1.start != edge2.start)
                return edge1.start - edge2.start;
            else if(edge1.isStart != edge2.isStart)
                return Boolean.compare(edge2.isStart, edge1.isStart);
            else if(edge1.isStart == true && edge1.isStart == edge2.isStart)
                return edge2.height - edge1.height;
            else
                return edge1.height - edge2.height;
        }
    };
}

class Solution {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        
        List<List<Integer>> res = new ArrayList<>();
        if(buildings == null || buildings.length == 0){
            return res;
        }
        
        List<Edge> edges = new ArrayList<Edge>();
        for(int[] building: buildings){
            edges.add(new Edge(building[0], true, building[2]));
            edges.add(new Edge(building[1], false, building[2]));
        }
        Collections.sort(edges, Edge.edgeComparator);
        
        Queue<Integer> queue = new PriorityQueue<Integer>(Collections.reverseOrder());
        queue.add(0);
        int pre = 0;
        for(Edge edge: edges){
            int height = edge.height;
            if(edge.isStart == true){
                queue.add(edge.height);
            }else{
                queue.remove(edge.height);
            }
            
            int cur = queue.peek();
            if(cur != pre){
                res.add(new ArrayList<Integer>(Arrays.asList(edge.start, cur)));
                pre = cur;
            }
        }
        
        return res;
    }
}
```

### 3. Deque

#### Deque唯一真主 leetcode 239. Sliding Window Maximum

九章的老师说这是关于deque的唯一一道面试中需要掌握的题

https://leetcode.com/problems/sliding-window-maximum/

1. 题目：

    给予一个数组，一个sliding window长度为k，从数组起始滑到终点，要求返回一个数组，数组内是每次sliding window中的最大值

2. 思路：

    看到这道题第一反应是维持一个size为k的heap，每次新加入一个元素，remove一个旧的，但是因为remove的时间复杂度是n，所以整体的时间复杂度是n²，这里我们使用deque进行优化，deque说白了就是两边都能进出的queue

    这道题目我们使用deque来筛选存储每次遍历到的元素，使得deque的size不大于k，那么如何筛选呢？我们分两步讨论，一个是新加一个元素，一个是删除一个元素；我们要求的是每个sliding window里面的最大值，那么如果我们新加入的元素比他左侧（数组中前面）的元素更小，那么他还有可能在sliding window滑动之后成为新的最大值，我们应当保留，但是如果新加入的元素更大，那么说明他之前的元素都没有意义，因为他们不会成为新的最大值了，我们直接删除即可；删除操作我们只需要注意存在两个相同元素的情况，我们可以使用removeFirstOccurence()这一函数，只删除第一个发现的元素就可以满足需求了

    由此我们可以总结出思路：先将前k个元素置入deque，新的元素在队尾，旧的元素在头部，如果新加入的元素更大，那么删除deque中排在新元素左侧的所以比他小的元素，如果新加入的元素更小，那么排入队尾保留，删除的时候留意有重复元素的情况；这样我们就可以保证每次最大的元素都在deque的头部，无意中实现了一种头尾都可以操作的heap

    本题的时间复杂度看似是n²，因为每次加入一个元素都需要遍历一遍删除一些不必要的，但是实际看来每个元素只被遍历过两次，即加入的时候和被删除的时候，因此其实这是一个O(n)的算法

3. 代码：

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        List<Integer> res = new ArrayList<Integer>();
        if(nums == null || nums.length < k)
            return new int[0];
        
        Deque<Integer> deque = new ArrayDeque<Integer>();
        
        for(int i=0; i<k; i++){
            dequePush(deque, nums[i]);  
        }
        res.add(deque.peekFirst());
        for(int i=k; i<nums.length; i++){
            int pre = nums[i-k];
            dequeDelete(deque, pre);
            dequePush(deque,nums[i]);
            res.add(deque.peekFirst());
        }
        
        int[] ans = new int[res.size()];
        for(int i=0; i<res.size(); i++){
            ans[i] = res.get(i);
        }
        return ans;
    }
    
    private void dequePush(Deque<Integer> deque, int num){
        while(!deque.isEmpty() && deque.peekLast() < num)
            deque.pollLast();
        deque.addLast(num);
    }
    
    private void dequeDelete(Deque<Integer> deque, int pre){
        if(deque.peekFirst() == pre)
            deque.removeFirstOccurrence(pre);            
    }
}
```

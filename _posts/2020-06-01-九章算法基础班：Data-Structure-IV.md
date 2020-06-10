---
layout:     post
title:      九章算法高级班：Data Structure IV
subtitle:   Stack & Heap
date:       2020-06-01
author:     lmcdhr
header-img: img/LeetCode_Sharing.png
catalog: true
tags:
    - Leetcode
    - Data Structure
    - 九章高级班
---

## Data Structure IV stack & heap

## 1. Heap的一点拓展

之前的基础班中已经介绍了heap的使用，这里做一点简单的拓展

#### 1. Heap的优化数据结构: HashHeap与TreeSet 

1. java的heap有自带的remove函数，该函数遍历所有数据并删除相关数据，因此时间复杂度O(n),如果需要频繁的删除操作，我们可以使用hashheap和treeset来代替priority queue，使得remove操作时间复杂度变为logn

2. 注意：treeset使用红黑树实现，本质是实现了一个有序的set（而hashset使用hash实现），所以内部无法保存重复数据，而且重复数据需要使用comparator函数来定义；而且虽然remove函数是logn，但是其他操作例如add等操作时间复杂度也变为了logn

## 2. stack

#### 1. 底层实现：
java中，stack继承于vector，而vector继承于抽象类list，因此stack在底层实际是用list来实现的

#### 2. 基本操作以及时间复杂度：
1. push：向栈顶增加一个元素，O(1)
2. pop: 推出栈顶元素,O(1)
3. peek: 查看栈顶元素,O(1)

#### 3. 单调栈
1. 引出：在实际应用中，我们需要求一种问题：找到在一组数据中，某个数据的左边和右边，第一个比他大/小的元素，这里我们使用单调栈进行维护
2. 大致思路：找比他小的元素，维护一个递增栈，找更大的元素反之（具体实现请看下方的单调栈例题）

#### 4. stack与递归
1. 很多递归都可以用stack来优化，比如dfs遍历树的时候，非recursion的写法就是使用stack，可以避免stack overflow的问题
2. 但是两者的思路是不同的，dfs是自上往下，stack是自下向上，具体例子可以看最后一道例题

## 3. 栗子

### 1. heap

#### 1. leetcode 42. Trapping Rain Water
https://leetcode.com/problems/trapping-rain-water/

1. 题目：著名的下雨盛水问题，请大家进网页自行查看
2. 思路：

    本题有三种思路，双指针，stack，dp；讲解三种思路之前，我们应该先意识到：这种灌水问题，可以灌水的高度取决于两侧边中较短的那个边，于是我们可以大概获取一个直观思路：从第一个点开始，找到比这个点更高的一条边，中间就是可以灌水的区域，这个问题也就转换成了如何找水坑的问题。

    1. stack解法：（请配合代码来看）总体的思路就是从左至右找水坑，换言之就是找到一个比栈顶元素大的点，找到一个水坑后，水坑的储水能力（高度）是两侧边较短的高度减去水坑的最低点，宽度是当前坐标与我们在stack中存储的下标差值，我们便换得了一个坑的总储水量，遍历完整个数组即可得到答案，本题中我们在栈中存储数据的下标而非本身，可以方便计算宽度；

    但是这种思路理解起来有一个误区，那就是水坑并非是从左至后进行计算的，而是发现一个水坑之后，对其进行拆解，从小打大，由内而外的计算，这里我给出一个栗子，比如有一个水坑的高度是：[2,1,0,1,3]，我们一步步推导来看一下解题过程：
    1. 遍历过程经历元素2->1->0，一直是递减的，于是这三个元素所在的下标被置入栈，现在的栈为[0,1,2]（再次注意存储的是下标），随后走到1，发现了一个比栈顶元素0大的，将0推出，作为坑底，而坑的两个边界便是现在的栈顶元素1（下标1）和我们当前遍历到的位置上的1（下标3），这个水坑的高为1，宽度为1，于是我们得到了1，0，1这个小水坑的储水量1
    2. 随后我们再进行比对，0因为已经被推出，这次遍历到的位置上的1和栈顶元素1相同，并不存在坑，于是我们将这个1置入栈，继续往下寻找，此时栈中存储的下标变成了：[0,1,3]
    3. 继续遍历，我们发现了下标为4的位置上的3，比栈顶元素下标为3位置上的1大；于是我们再进行计算，这次我们其实进行的计算是一个高度为[2,1,1,1,3]的坑，高度为1，宽度为3，将两次得出的结果加和，由此，这个大坑的储水量计算完毕

    由此，我们可以发现，整个水坑的计算并非是从左至右加和的，而是从距离最近的两个边开始，换言之就是从最底部的水坑开始，一直往上一层层计算，颇有递归的味道，只不过如果使用递归的话，思路是从浅往深，而使用stack是从深往浅，也印证了我之前说的使用stack与递归思路上的差异

    2. 双指针解法：使用stack解法，我们是从左至右找第一个比他大的点，而如果我们将整个数组区域看成一个大坑，那么他内部的储水能力不会超过两侧边中较短的那个，于是我们可以从较短的那个边开始，一直遍历，寻找一个比他大的边；再次比较，再次从较小的边开始。这种思路其实是把整个大坑从左和右切分成了一个个小坑，我们举一个栗子：加入一个大坑的高度是:[2,0,0,6,0,0,0,3,0,4],那么从两头看，整个坑的储水高度不会高于2，我们从2开始，一直遍历到6，这期间共储水2*2=4，这时6左侧的点已经遍历完毕，整个坑就变成了[6,0,0,0,3,0,4]，我门再从较小的4开始，向左遍历即可，一直到左右指针相遇

    3. DP解法，这种解法比较难以想到，相当于有一个探照灯分别位于左侧和右侧，一个向右照，一个向左照，我们可以得知当遇到一个较高的点时，他另外一侧就不会再被灯照到了，就像摩天大楼阻挡了阳光一样，也说明了从这点开始往左或往右的最大值就是他了；我们分别从左和从右照一次，求出每个点探照的最大值，然后再讲两次的结果取min，就是可能获得的水坑内的水量，文字描述过于抽象，请看leetcode解析里的第二种解法

3. 代码：
```java
## stack解法
class Solution {
    public int trap(int[] height) {
        
        if(height == null || height.length == 0)
            return 0;
        
        Stack<Integer> stack = new Stack<Integer>();
        int i=0; 
        int n = height.length;
        int ans = 0;
        while(i<n){
            if(!stack.isEmpty() && height[stack.peek()] < height[i]){
                int top = stack.pop();
                if(stack.isEmpty())
                    continue;
                int h = Math.min(height[i],height[stack.peek()]) - height[top];
                int width = i - stack.peek() - 1;
                ans += h*width;
            }else{
                stack.push(i++);
            }
        }
        return ans;
    }
}
```

```java

## 双指针解法
class Solution {
    public int trap(int[] height) {
        
        int left = 0;
        int right = height.length - 1;
        int res = 0;
        if(height.length == 0 || left == right)
            return res;
        int leftheight = height[left];
        int rightheight = height[right];
        
        while(left < right){
            if(leftheight < rightheight){
                left++;
                if(leftheight > height[left]){
                    res += leftheight - height[left];
                }
                else{
                    leftheight = height[left];
                }
            }
            else{
                right--;
                if(rightheight > height[right]){
                    res += rightheight - height[right];
                }
                else{
                    rightheight = height[right];
                }
            }
        }
        return res;
    }
}
```

#### 2.42的follow up leetcode 407. Trapping Rain Water II

1. 题目：还是盛雨水题，但是这次数据从一维变成二维
2. 思路：

     有了42的基础，这道题便好理解多了，现在图形变为二维，那么决定这样一个水池储水能力的，是他最周围的一圈边，类似于木桶原理；有了这一认识，我们便可以得出大致思路：建立heap，从木桶四周中最短的边开始，利用方向矩阵向四周遍历，因为有四周的边兜底，因此内部比这条最短边更短的地方，都可以储水；当然，如果遍历到了一个比兜底边更长的边，我们也需要将兜底边更新为这个更长的边

     这道题中有一个容易弄错的点，我们使用一个数据结构存储一个点的坐标和高度，使用bfs遍历到四周符合条件的点时，这个点的高度不能直接存为该点的高度，而是遍历到该点的兜底边的长度；而兜底边的长度如何求呢？应该是当前的兜底边长度与遍历到的点的高度二者中较大的那个，因为随着bfs，我们遍历到一个高度很高的点，他周围的点就会由他进行兜底，即使这个点不够高，也有最周围的点为他兜底；因此如果出现了一个更长的兜底边，储水能力会改变，因此我们每次存储的是当前结点的高度与当前兜底边二者中的更大值，对兜底边进行实时的更新

3. 代码：

```java
class Cell{
    int i;
    int j;
    int height;
    
    public Cell(int i, int j, int height){
        this.i = i;
        this.j = j;
        this.height = height;
    }
}

class Solution {
    
    private final int[][] directions = ;
    
    private Comparator<Cell> cellComparator = new Comparator<Cell>(){
        public int compare(Cell cell1, Cell cell2){
            return cell1.height - cell2.height;
        }
    };
    
    public int trapRainWater(int[][] heightMap) {
        
        int res = 0;
        if(heightMap == null || heightMap[0].length == 0){
            return res;
        }
        
        int m = heightMap.length;
        int n = heightMap[0].length;
        Queue<Cell> todo = new PriorityQueue<Cell>(cellComparator);
        int[][] visited = new int[m][n];
        
        for(int i=0; i<m; i++){
            visited[i][0] = 1;
            visited[i][n-1] = 1;
            todo.add(new Cell(i,0,heightMap[i][0]));
            todo.add(new Cell(i,n-1,heightMap[i][n-1]));
        }
        for(int i=1; i<n; i++){
            visited[0][i] = 1;
            visited[m-1][i] = 1;
            todo.add(new Cell(0,i,heightMap[0][i]));
            todo.add(new Cell(m-1,i,heightMap[m-1][i]));
        }
        
        while(!todo.isEmpty()){
            Cell doing = todo.poll();
            for(int[] direction: directions){
                int new_i = doing.i+direction[0];
                int new_j = doing.j+direction[1];
                if(new_i<0 || new_j<0 || new_i>=m || new_j>=n || visited[new_i][new_j] == 1)
                    continue;
                todo.add(new Cell(new_i,new_j,Math.max(heightMap[new_i][new_j],doing.height)));
                visited[new_i][new_j] = 1;
                if(doing.height > heightMap[new_i][new_j])
                    res += doing.height-heightMap[new_i][new_j];
            }
        }
        
        return res;
    }
}
```

#### 3. leetcode 480. Sliding Window Median

https://leetcode.com/problems/sliding-window-median/

1. 题目：给予一个数组和一个sliding window的长度k，window从数组最左边一直slide到最右边，要求输出每次sliding window滑动过后，window内的中位数
2. 思路：

     要求动态中位数，自然的想到使用双heap，这在之前那节heap的笔记中有提到，剩下的思路就不难了，每次窗口滑动就是删除一个元素，再加入一个元素，随后每次输出中位数就好

     不过这道题做的时候有一个int溢出的问题出现在compare函数，我一开始将compare函数写成left-right，但是这样如果数据溢出无法解决，正确的做法是写成left.compareto(right)或者将compare函数省略为Collections.reverseOrder()

3. 代码：

```java
class Solution {
    
    Queue<Integer> small;
    Queue<Integer> big;
        
    public double[] medianSlidingWindow(int[] nums, int k) {
        if(nums == null || nums.length == 0)
            return new double[0];
        
        int m = nums.length;
        double[] res = new double[m - k + 1];
        small = new PriorityQueue<Integer>(Collections.reverseOrder());
        big = new PriorityQueue<Integer>();
        int left = 0;
        int right = k;
        assembleHeap(nums, k);
        res[0] = getMedian(k);
        
        for(right = k; right<m; right++,left++){
            if(!small.isEmpty() && nums[left] <= small.peek()){
                small.remove(nums[left]);
            }
            else{
                big.remove(nums[left]);
            }
            small.add(nums[right]);
            big.add(small.poll());
            keepBalanced();
            
            res[left+1] = getMedian(k);
        }
        
        return res;
        
    }
    
    private void assembleHeap(int[] nums, int k){
        int count = 0;
        for(int i=0; i<k; i++){
            if(big.isEmpty()){
                big.add(nums[i]);
                count++;
            }
            else if(small.isEmpty()){
                small.add(nums[i]);
                count++;
            }
            else{
                if(big.peek() < small.peek()){
                    big.add(small.poll());
                    small.add(big.poll());
                }
                if(count % 2 == 0){
                    small.add(nums[i]);
                    big.add(small.poll());
                    count++;
                }
                else{
                    big.add(nums[i]);
                    small.add(big.poll());
                    count++;
                }
            }
        }
    }
    
    private double getMedian(int k){
        if(k%2 == 0){
            return (double)((double)small.peek()+(double)big.peek())/2;
        }
        else{
            return (double)big.peek();
        }
    }
    
    private void keepBalanced(){
        while(big.size() - small.size() > 1){
            small.add(big.poll());
        }
    }
}
```

### 2. stack基础

#### 1. leetcode 155. Min Stack

https://leetcode.com/problems/min-stack/

1. 题目：实现一个min stack数据结构，要求可以push和pop数据，并能实现get min，即输出当前stack中的最小值
2. 思路：

     实现stack很简单，本题难点在于如何实时查找min，如果使用一个int来存储最小值，那么如果最小值被pop，无法做到回溯；为了解决这个问题，我们可以设置另外一个stack，用来存储最小值：即每次新输入一个数据，我们可以通过比对得出新的最小值，并将这个最小值置入最小值的栈中，这样每个输入数据和每次输入获得的最小值就一一对应了起来，每输入一个数据，就会新加一个最小值，每pop一个数据，这个数据对应的最小值也会被pop，这样就实现了最小值的回溯功能

3. 代码：

```java
class MinStack {

    Stack<Integer> stack;
    Stack<Integer> minstack;
    /** initialize your data structure here. */
    
    public MinStack() {
        stack = new Stack<Integer>();
        minstack = new Stack<Integer>();
    }
    
    public void push(int x) {
        int min = minstack.size()==0?Integer.MAX_VALUE:minstack.peek();
        if(x<min){
            minstack.push(x);
        }
        else{
            minstack.push(min);
        }
        stack.push(x);
    }
    
    public void pop() {
        stack.pop();
        minstack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return minstack.peek();
    }
}

```

#### 2. 栈的翻转 leetcode 232. Implement Queue using Stacks

https://leetcode.com/problems/implement-queue-using-stacks/

1. 题目：用多个stack来实现一个queue，要求实现push,peek,pop,isempty功能
2. 思路：

     queue与stack最大的不同在于一个是先进先出，一个是先进后出，出栈顺序不同，为了解决这个问题，引出栈的翻转做法：使用两个栈，当我们把数据存储到第一个栈中，一直pop的话，输出与原数据顺序相反，如果我们将数据pop进另一个栈，结束后另一个栈再进行pop，那么顺序就与原数据顺序相同了，有点双重否定表肯定的意思。

     而我们日常做题使用stack的时候，很多时候出栈的数据跟我们要的数据都是反的，我们只要借助另外一个栈来翻转即可

     这里还有一点小优化，我们不一定每次输入数据，都进行翻转，这样会使时间复杂度变为n，我们只需要在stack2为空的时候，将stack1中的数据全pop进stack2即可，这样每输出一次，stack2就pop一次，一直到stack2再为空，我们再将1中的数据补充进去即可，这样做可以使得时间复杂度近似为O(1)

3. 代码：

```java
class MyQueue {

    Stack<Integer> stack1;
    Stack<Integer> stack2;
    int peek;
    /** Initialize your data structure here. */
    public MyQueue() {
        stack1 = new Stack<Integer>();
        stack2 = new Stack<Integer>();
        peek = 0;
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        if(stack1.isEmpty()){
            peek = x;
        }
        stack1.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if(stack2.isEmpty()){
            while(!stack1.isEmpty()){
                stack2.push(stack1.pop());
            }
        }
        return stack2.pop();
    }
    
    /** Get the front element. */
    public int peek() {
        if(stack2.isEmpty()){
            return peek;
        }
        else{
            return stack2.peek();
        }
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return stack1.isEmpty() && stack2.isEmpty();
    }
}
```

### 3. 单调栈 Monotonous stack

#### 1. 单调栈的维护 leetcode 84. Largest Rectangle in Histogram

https://leetcode.com/problems/largest-rectangle-in-histogram/

1. 题目：给予一个数组，里面的数字可以理解为一个个宽度为1，高度不一且互相邻接的矩形，要求求得这一图形中一个最大的矩形面积
2. 思路：

     本题求矩形面积，有两种想法：一个是横向来看，横向用双指针遍历所有可能的底边，再看纵向上分别有哪些数值的高度可以满足条件，得出矩形，从而获得最大值，这种做法需要n²横向遍历，再n纵向遍历，一共有nums.length²*nums.max种考虑情况，是一种n³的解法；而如果我们纵向来看，我们的思路则变为了：对于每一块bar，他能够向左向右延伸多少，换言之，他的左侧和右侧有多少了连续的，而且比他高的bar，因为在这些比他高的bar使得他可以延伸出一个跟他一样高的矩形，我们只要求出该点左右有多少这样的bar，就可以求出以该点为高，宽度为符合条件的bar的数量的矩形了；如果想知道左右两侧有多少bar比他高，那么我们只需要知道左右两侧第一个比他矮的点就行了，这两个点之间的都是比他高的，为了满足这一要求，我们使用stack进行维护

     维护的大概思路为：数组从左至右遍历，每遍历到一个点，检查该点height与栈顶元素height的关系，如果该点的height<栈顶元素的height，说明已经为栈顶元素找到了右侧第一个小的元素，pop出栈顶元素，新的栈顶元素就是原来栈顶元素的左界，用左界右界算出宽度，高度就是栈顶元素的height，看大小情况更新max即可

3. 代码：

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        if(heights == null || heights.length == 0){
            return 0;
        }
        
        int ans = 0;
        Stack<Integer> stack = new Stack<Integer>();
        for(int i=0; i<=heights.length; i++){
            int height = i==heights.length? -1:heights[i];
            while(!stack.isEmpty() && heights[stack.peek()]>height){
                int start = stack.pop();
                int h = heights[start];
                int width = stack.isEmpty()? i:i-stack.peek()-1;
                ans = Math.max(width*h, ans);
            }
            stack.push(i);
        }
        
        return ans;
    }
}
```

#### 2. 84的follow up leetcode 85. Maximal Rectangle
https://leetcode.com/problems/maximal-rectangle/

1. 题目：也是求最大矩形，不过这次给出的是一个充满0和1的二维数组，1代表可用，0代表空，求一个由1组成的最大矩阵面积
2. 思路：

     看似猛如虎，实则0-5，我们已经在一维空间得知了解法，二维空间不过是一维空间的堆叠，我们将二维数组一层一层的切开，使用dp求出第n层为底时，向上看所能得到的height一维矩阵（1的数量），这样就将问题转化为了84题，轻松得出结果

3. 代码：

```java
class Solution {
    public int maximalRectangle(char[][] matrix) {
        if(matrix == null || matrix.length == 0)
            return 0;
        
        int m = matrix.length;
        int n = matrix[0].length;
        int max = 0;
        
        int[][] heights = getHeights(matrix);
        for(int[] height: heights){
            Stack<Integer> stack = new Stack<Integer>();
            for(int i=0; i<=height.length; i++){
                int h= i==height.length? -1:height[i];
                while(!stack.isEmpty() && height[stack.peek()] >= h){
                    int h2 = height[stack.pop()];
                    int width = stack.isEmpty()? i:i-stack.peek()-1;
                    max = Math.max(max, h2*width);
                }
                stack.push(i);
            }
        }
        return max;
    }
    
    private int[][] getHeights(char[][] matrix){
        int[][] heights = new int[matrix.length][matrix[0].length];
        for(int i=0; i<matrix.length; i++){
            for(int j=0; j<matrix[0].length; j++){  
                if(i == 0){
                    heights[0][j] = Character.getNumericValue(matrix[0][j]);
                }
                else if(matrix[i][j] == '0'){
                    heights[i][j] = 0;
                }
                else{
                    heights[i][j] = Character.getNumericValue(matrix[i][j]) + heights[i-1][j];
                }
            }   
        }
        return heights;
    }
}
```

### 4. 递归与stack

#### 1. ❌套娃 leetcode 394. Decode String
https://leetcode.com/problems/decode-string/

1. 题目：进行命令解码，栗子：s = "2[abc]3[cd]ef", return "abcabccdcdcdef". 命令中数字+[string]代表括号内的string字符串重复n次，可以套娃

2. 思路：

     本题并不难，如果使用递归，那就是每有一个'['，就将相关参数传入下一级递归即可，本题我们试着用stack优化递归stack overflow的问题；stack会将有效信息从头开始存储，并从末尾开始处理，也就是从最内层开始处理，而递归是直接将参数传给下一层函数，相当于从外往里处理；如果递归是遇到一个'['就开始下一层递归，那么stack就是一直将数据压入栈，直到遇到第一个']'开始处理参数，即已经遇到了最内层的数据，并从这层数据开始往外部展开，将新生成的子数据推入栈，一层层往外处理即可

3. 代码：

```java
class Solution {
    
    public String decodeString(String s) {
        
        if(s == null || s.length() == 0)
            return s;
        Stack<Object> stack = new Stack<Object>();
        Stack<Object> store = new Stack<Object>();
        int num = 0;
        String ans = "";
        
        for(char word: s.toCharArray()){
            if(Character.isDigit(word)){
                num = num*10 + word - '0';
            }
            else if(word == '['){
                stack.push(num);
                num = 0;
            }
            else if(word == ']'){
                String sub = "";
                Stack<String> reverse = new Stack<String>();
                while(!stack.isEmpty() && (stack.peek() instanceof String)){
                    reverse.push((String)stack.pop());
                }
                while(!reverse.isEmpty()){
                    sub += reverse.pop();
                }
                int time = (Integer)stack.pop();
                for(int i=0; i<time; i++){
                   stack.push(sub);
                }
            }
            else{
                stack.push(Character.toString(word));
            }
        }
        
        
        while(!stack.isEmpty()){
            store.push(stack.pop());
        }
        while(!store.isEmpty()){
            ans += store.pop();
        }
        
        return ans;
    }
}
```

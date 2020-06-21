---
layout:     post
title:      九章算法高级班： follow up questions
subtitle:   follow up questions
date:       2020-06-21
author:     lmcdhr
header-img: img/LeetCode_Sharing.png
catalog: true
disqus_username: lmcdhr
tags:
    - Leetcode
    - follow up
    - iterator
    - 九章高级班
---

## follow up questions

## 1. follow up 问题

#### follow up类型

1. 一维转二维：

   一般题目不会改变，数据的类型从一维数组变为二维矩阵，总体的思路不变，只是某些关键的推导过程会变得复杂

2. 题目条件加强：

   这类问题较为棘手，虽然有的时候题目要求只是增加了一个等号，思路却很可能有重大改变，因此需要重新思考思路

3. 马甲题：

   顾名思义，换个马甲，题目本质是一样的，多做题就能识别出来

## 2. iterator类问题

#### 1. 题目类型

这类问题一般是给予一组数据，让你自己写一个iterator遍历整个数据，每次输出数据之中的一个

#### 2. 解题思路

1. 一个重要前提：不要预处理数据！即不要先把数据全部处理完，比如将整个数据结构处理完放入数组中，之后每次输出一个这种操作，而是应该现场输出，现场处理

2. 一般都会包含两个函数：next和hasnext，next就是输出iterator的下一个元素，hasnext是判断是否还有下个元素，一般我们使用next函数的时候，需要先用hasnext确保仍有元素可以输出

3. 我们处理数据的内容一般放入hasnext之中，例如我们flatten list一题中，我们会在hasnext中每次处理完一个小的list，然后使得next函数得以输出，而且next函数一般只负责输出

4. 这类先处理后面数据的问题，我们会较为频繁的使用stack，以存储先出现的stack，有的时候我们也需要两个stack来翻转数据

## 2. 栗子：

### 1. 一维转二维问题

#### 一维prefix sum leetcode 560. Subarray Sum Equals K

1. 题目：

   给予一个数组，以及一个整型k，要求找出数组中连续子数组的个数，使得这些子数组的和为k

2. 思路：

   求连续子数组的和，一般会使用prefix sum，本题中我们需要求出连续子数组的数量，且和为k，和为k比较好弄，只需要某段子数组的prefix sum为k即可，即两个从头开始的子数组的sum差值为k；如果还需要保存数量，那么我们需要使用hash map来保存这些sum，并且同时保存他们出现的次数，本题中我们只要遍历数组求出sum，存入hash map中，再每次在map中寻找k-sum，更新出现次数就行了

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

#### 一维变种 leetcode 523. Continuous Subarray Sum

https://leetcode.com/problems/continuous-subarray-sum/

1. 题目：

   给予一个数组以及一个整型k，要求得出是否可能存在至少一个子数组，使得子数组的长度至少为2，且和是k的正整数倍

2. 思路

   可以看出这是上一道题的follow up， 有两个多出来的要求：一是子数组的长度至少为2，二是从和正好等于，变成了正整数倍，不过问题从求总数变成了求是否可能，实际上精简了一些。现在我们重新梳理思路，分别解决这两个问题：
   1. 长度至少为2：上一题中，需要求数量，因此hash map中key是sum值，value是出现次数，不过现在如果需要子数组的长度，而且不需要再存个数，那么我们应该改为存下标
   2. 正整数倍：遍历n倍显然不现实，我们改变一下思路，如果加和是正整数倍，那么他一定可以整除k，而且如果两个不同的sum除以k的余数加相同，那么他们两个相减，一定是6的正整数倍，例如k=5,一个子数组的和为8，一个为13，他们除以5的余数都是3，那么他们中间的prefix sum13 - 8 = 5， 是5的1倍，满足条件。因此我们本次不再单纯的保存数组的sum，而是保存sum%k,如果遍历中找到了另一个sum%k,那么验证一下长度满不满足，满足的话直接输出true即可

   我们不妨再进一步思考，如果要求长度怎么办？很简单，把保存的下标变成int[]，里面一个元素保存下标，一个保存出现次数就行了

3. 代码：

```java
class Solution {
    public boolean checkSubarraySum(int[] nums, int k) {
        if(nums == null || nums.length == 0)
            return k==0;
        
        int m = nums.length;
        int sum = 0;
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        map.put(0,-1);

        for(int i=0; i<m; i++){
            sum += nums[i];
            if(k != 0)
                sum = sum % k;
            if(map.containsKey(sum)){
                if(i - map.get(sum) > 1 ){
                    return true;
                }
            }else{
                map.put(sum, i);
            }
        }        
        
        return false;
    }
}
```

#### 一维转二维 倒霉蛋二号 leetcode 1074. Number of Submatrices That Sum to Target

https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/

咖哥看了题，说以后他去面试别人就面这道题，被面的哥们老倒霉蛋了，那么祝愿大家。。。。。。

1. 题目：

   要求跟560一样，但是这次是在一个二维数组之中，求子数组也变成了求子矩阵，求数量

2. 思路：

   如果使用暴力遍历，那么不同矩阵的大小有m*n种，遍历每种矩阵也需要m*n，求和需要m*n（如果不用求和优化），那么这就是一个六次方的时间复杂度，板儿B ETL，我们之前求和使用了hash map+prefix sum的解题方法，那么本题能否继承呢？如果可以，我们可以大概推算出只需要遍历矩阵一遍即可，时间复杂度可以降至mn，hash map的使用没啥难度，可是如何在二维矩阵中求出prefix sum呢？

   一维数组中，prefix sum是sum[i~j] = sum[j]-sum[i-1],而在矩阵中，如果我们定义sum[i][j]为从原点到(i,j)这个矩阵的sum，那么我们如何根据他前方已经求出的元素推导出来呢？我们可获得的最近的点有四个：左上矩阵的sum[i-1][j-1],左侧矩阵的sum[i][j-1],上侧矩阵的sum[i-1][j]，以及当前点的值nums[i][j]；如果我们画个图，我们不难发现，我们将左侧矩阵和上册矩阵的和相加，再减去左上矩阵的sum（被算了两次），正好是sum[i][j]除去右下角元素nums[i][j]的和，因此我们可以大致推导出二维矩阵的prefix sum公式：
```
sum[i][j] = sum[i-1][j] + sum[i][j-1] - sum[i-1][j-1] + nums[i][j]
```

   大家画个图，就很好理解了，得出这个公式之后，问题就很好解决了，之前我们看到prefix sum的推导会有减一操作，如果直接减一可能会导致-1的出现，因此我们还是多加一行一列，作为缓冲，全部置入0即可，意为数组为空的时候sum为0，这样就避免了-1的出现

   可以看出，一维转二维的题，其实思路多半是不变的，之前我们有一道trap rain water，有一维和二维两道题，其实思路也没有变，只是因为维度的改变，推导公式发生了一些改变，面试的时候我们冷静思考，应该没有问题

3. 代码：

```java
class Solution {
    public int numSubmatrixSumTarget(int[][] matrix, int target) {
        
        int m = matrix.length;
        int n = matrix[0].length;
        int[][] prefixSum = new int[m+1][n+1];
        int res = 0;
        
        for(int i=1; i<m+1; i++){
            for(int j=1; j<n+1; j++){
                prefixSum[i][j] = prefixSum[i-1][j] + prefixSum[i][j-1] - prefixSum[i-1][j-1] + matrix[i-1][j-1];
            }
        }
        
        for(int up=0; up<m; up++){
            for(int down=up+1; down<m+1; down++){
                Map<Integer, Integer> map = new HashMap<Integer, Integer>();
                for(int j=0; j<n+1; j++){
                    int sum = prefixSum[down][j] - prefixSum[up][j];
                    if(map.containsKey(sum - target)){
                        res += map.get(sum - target);
                    }
                    map.put(sum, map.getOrDefault(sum,0)+1);
                }
            }
        }
        
        return res;
    }
}
```

### 2. partition 类型问题

#### partition的抽象表达 leetcode 324. Wiggle Sort II

https://leetcode.com/problems/wiggle-sort-ii/

1. 题目：

   给予一个数组，现在要求重排数组，要求最后输出时数组内部大小关系是a[0]<a[1]>a[2]<a[3]>a[4]<a[5]......

2. 思路：

   本题的基础题型是wiggle sort I，两者唯一的不同是I中最后数组内部大小关系是大于等于和小于等于，II中却不能等于，这两点看似区别不大，解题办法却天壤之别，I中我们直接按序遍历数组，定义一个boolean变量来记录当前是要找一个比当前元素更大的元素还是更小的元素，如果遇到的元素大小关系与当前相反，直接swap即可

   现在不能等于，其实差了很多，比如这个例子：[1,3,2,2,3,1],如果按照I中的要求，那么直接就满足要求了，但是如果不能等于，那么2和2那里就不满足要求，可能的一种解答是:[2,3,1,3,1,2]，那么这道题如何进行解答呢？

   我们可以先看一下答案的格式，是一个峰值接一个较低值，那么如果我们把数组分为两半，一半保存峰值，一半保存较低值，那么只需要每次先输出一个较低值，再输出一个峰值，即可。这个思想在quick sort中使用过，即选取一个pivot，将数组分为两半，一半大于等于pivot，一段小于pivot，再进行后续处理，现在我们不妨也按照这个思路来分：我们数组一共有n个元素，那么如果我们按照大小选取中间元素mid，那么会有（n-1）/2个元素比mid小，另外的元素比mid大，因此我们需要找到这个mid元素，换言之，找到第n/2大的元素（中位数），是不是听起来很熟悉？没错，就是我们之前基础班里做过的quick select那道题：find the kth largest element，那道题我们分别用heap和quick select（也就是partition）找到了第k大元素，显然quick select是更好的选择，我们每次选取一个pivot，查看小于pivot的元素数量够不够n/2，如果够了，那么说明找多了，递归往回找，如果不够，那么说明还需要找，我们减去已经找到的个数，继续往下递归查找，直到正好找到第k大

    那么本题也迎刃而解，我们先找到中位数，随后定义双指针，一个从头开始放较小元素，一个从尾开始放较大元素，当然我们需要根据数组长度的奇偶性判断最后一个元素是较大元素还是较小元素，随后遍历原数组，根据遍历元素与mid的关系，将元素置入相应指针的位置，并且指针在处理完后自减2

   本题是partition的经典应用，也是这类题目条件只变一点，解题思路却变化很大的题，这种题在面试中较为难解，需要我们多刷题，根据以往遇到过的题来思考

3. 代码：

```java
public class Solution {
    public static void wiggleSort(int[] nums) {
        final int n = nums.length;
        int[] tem = new int[n];
        for (int i = 0; i < n; i++) {
            tem[i] = nums[i];
        }
        int mid = partition(tem, 0, n-1, n/2);
        int[] ans = new int[n];
        for (int i = 0; i < n; i++) {
            ans[i] = mid;
        }
        
        int l = 1, r = (n%2 == 0) ? n-2 : n-1;
        for (int i = 0; i < n; i++) {
            if (nums[i] < mid) {
                ans[r] = nums[i];
                r -= 2;
            } else if (nums[i] > mid) {
                ans[l] = nums[i];
                l += 2;
            }
        }
        for (int i = 0; i < n; i++) {
            nums[i] = ans[i];
        }
    }
    
    public static int partition(int[] nums, int l, int r, int rank) {
        int left = l, right = r;
        int now = nums[left];
        while (left < right) {
            while (left < right && nums[right] >= now) {
                right--;
            }
            nums[left] = nums[right];
            while (left < right && nums[left] <= now) {
                left++;
            }
            nums[right] = nums[left];
        }
        if (left - l == rank) {
            return now;
        } else if (left - l < rank) {
            return partition(nums, left + 1, r, rank - (left - l + 1));
        } else {
            return partition(nums, l, right - 1, rank);
        }
    }
}
```

#### quick sort 抽象问题 lintcode 399. Nuts & Bolts Problem

https://www.lintcode.com/problem/nuts-bolts-problem/description

1. 题目：

   给予两组string[]，第一组代表螺丝，全是小写字母，第二组代表螺母，全是大写字母，如果在统一大小写后，一个螺丝字符串和螺母字符串是一样的，那么他们两个是配套的，现在要求重新排序其中一个string[]，使得最后两个string[]对应的字符串都是配套的，但是一个数组内部的对比大小是不可以的，我们只提供一个对比螺丝和螺母的函数compare，也就是说你可以对比螺丝和螺母的大小关系，却不能在螺丝或者螺母自己内部互相比对（就像现实中，我们可以尝试将螺丝拧入螺母中来确定大小关系，但是在肉眼不好识别的情况下，无法确定螺丝自己或者螺母自己之间的大小关系）

2. 思路：

   如果可以自己内部对比，那么很简单了，两放各自排序，最后输出的就是根据大小排序好的，而且互相配套的了，但是现在我们只能两个数组之间比对，咋办呢？我们可以看一下compare函数的功能，即对比一个螺丝和一个螺母字符串，如果配套返回0，螺母大返回1，螺丝大返回-1，如果我们随机挑选一个螺母，将所有螺丝都比对一遍，那么我们就可以找出配套的，大于螺母的和小于螺母的，是不是看起来又非常眼熟？是的，其实就是quick sort，只不过这次我们的pivot从数组内部找元素，变成了另外一个数组找，两者互相找pivot，互相排序，每次我们找出pivot所在的位置，然后让另外一个数组也使用这个pivot进行排序，那么两个数组中这个pivot的位置就确定了，随后我们再对pivot左右两边分别排序，每次确定一个pivot的位置；实际上就是两者公用一套排序规则，因此最后排序后的结果也是一样的，这一点就非常巧妙了

   因此大概的流程就是我们先进入quicksort函数，先选用一个pivot，对其中一个数组排序，确定这个pivot对应的位置，使得排序完成后这个位置左边都是比pivot小，右边比pivot大，随后将这个得出的pivot传入另外一个数组，让其使用这个pivot进行partition，这步操作完成后，二者pivot位置上的元素就对应起来了，随后继续递归，对左右两边再分别进行quick sort就可以了

   通过这道题我们可以看出quick sort实际是非常灵活的，partition作为其关键部分，即可以单独出题，也可以作为quick sort中间的考查内容之一

3. 代码：

```java
/**
 * public class NBCompare {
 *     public int cmp(String a, String b);
 * }
 * You can use compare.cmp(a, b) to compare nuts "a" and bolts "b",
 * if "a" is bigger than "b", it will return 1, else if they are equal,
 * it will return 0, else if "a" is smaller than "b", it will return -1.
 * When "a" is not a nut or "b" is not a bolt, it will return 2, which is not valid.
*/
public class Solution {
    /**
     * @param nuts: an array of integers
     * @param bolts: an array of integers
     * @param compare: a instance of Comparator
     * @return: nothing
     */
    public void sortNutsAndBolts(String[] nuts, String[] bolts, NBComparator compare) {
        int n = nuts.length, m = bolts.length;
        if(m!=n) {
            return;
        }
        // 通过快速排序的思想进行排序
        quickSort(nuts,bolts,compare, 0, n-1);
    }
    void quickSort(String[] nuts, String[] bolts,NBComparator compare, int start, int end){
        if(start >= end) {
            return;
        }
        int index = partition(nuts,bolts[start], compare, start, end);
        partition(bolts, nuts[index], compare, start, end);// 找到匹配的bolt
        // 将bolts划分为两部分
        quickSort(nuts, bolts, compare, start, index-1);
        quickSort(nuts, bolts, compare, index+1, end);
    }
    int partition(String[] NorB, String pivot, NBComparator compare, int start, int end){
        int m = start;// m表示枢轴的实际位置。
        for(int i = start+1; i<=end; ++i){// 判断并且对nut和bolt通过交换进行排序
            if(compare.cmp(pivot, NorB[i]) == 1 || compare.cmp(NorB[i],pivot) == -1){
                String tmp = NorB[++m];
                NorB[m] = NorB[i];
                NorB[i] = tmp;
            }else if(compare.cmp(pivot,NorB[i]) == 0 || compare.cmp(NorB[i], pivot) == 0){
                String tmp = NorB[start];
                NorB[start] = NorB[i];
                NorB[i] = tmp;
                i--;
            }
        }
        String tmp = NorB[m];
        NorB[m] = NorB[start];
        NorB[start] = tmp;
        return m;
    }
};
```

### 3. iterator类问题

#### 光翼展开准备题 lintcode 22. Flatten List

https://www.lintcode.com/problem/flatten-list/description

1. 题目：

   给予一个多维数组，现在要求你将数组按序展开为一维，有几个函数可以使用：isInteger，判断当前的元素是不是整型，getInteger，获取当前的整形元素，如果不是整型，返回null，getList，返回当前的list，如果不是list，返回null

2. 思路：

   跟我们之前做过的展开字符串类似，不过因为没有多重套娃关系，所以不需要stack，按序走就可以了，不过本题我们有三个辅助函数来判断下一个元素的内容是integer还是list，并获取相关内容，那么思路便比较明了了，不过这种遍历并不能一次解决所有问题，因此我们一次遍历只能打开一层括号，比如有一个数据是[2,[3,[4]]]，我们一次遍历可以将数据变成2,[3,[4]]，我们还需要两次遍历才能将最内层的4变成integer从而放入答案中，因此我们需要一个变量来记录当前还有没有需要打开的括号了，而时间复杂度也是从n到n²：如果没有括号，就是n，如果所有元素都是一层层括号嵌套的，那么就是n²。接下来我们说一下思路：
   1. 我们定义一个boolean变量记录是否还需要打开括号，进入while循环，随后for循环遍历生数据，将boolean置为false，
   2. 如果当前遇到的是整型，将数据填入结果中
   3. 如果遇到的是一个子数组，那么说明我们仍需要打开括号，那么重新将boolean变量置为true，使得继续得以while循环
   4. 直到boolean变量为false，意为不再得到使boolean变量置为true的子数组了，此时退出while循环，结果中都是int，返回

3. 代码：

```java
* public interface NestedInteger {
 *
 *     // @return true if this NestedInteger holds a single integer,
 *     // rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds,
 *     // if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // @return the nested list that this NestedInteger holds,
 *     // if it holds a nested list
 *     // Return null if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */

public class Solution {

    // @param nestedList a list of NestedInteger
    // @return a list of integer
    public List<Integer> flatten(List<NestedInteger> nestedList) {
        // Write your code here
        List<Integer> res = new ArrayList<Integer>();
        List<NestedInteger> list = nestedList;
        boolean needFlat = true;
        while(needFlat){
            needFlat = false;
            List<NestedInteger> temp = new ArrayList<>();
            for(NestedInteger num: list){
                if(num.isInteger()){
                    temp.add(num);
                }else{
                    temp.addAll(num.getList());
                    needFlat = true;
                }
            }
            list = temp;
        }
        
        for(NestedInteger num: list){
            res.add(num.getInteger());
        }
        return res;
    }
}
```

#### 真·光翼展开 leetcode 341. Flatten Nested List Iterator

https://leetcode.com/problems/flatten-nested-list-iterator/

1. 题目：

   还是展开数组，但是这次我们需要写一个class来解决问题，里面包含next函数和hasnext函数，每次next会输出一个结果，hasnext判断是否还可以继续输出，我们仍可以使用上述的三个辅助函数

2. 思路：  

   有的小伙伴可能就说了：我都做过上边的题了，那么我直接处理完数据，将结果记入一个数组里，每次next输出一个，hasnext判断到没到尾就ok了，那可不行，这类iterator问题最忌讳这种解法，就算你这么写出来了，面试官很可能让你重做不预处理的做法（俗称不会领会领导意图），因此我们这里使用正宗的iterator解法来解题

   我们之前说过iterator类型问题中，next只管输出（真你🐎惯得），hasnext不但要判断是否有可以输出的元素，还要处理好数据，使得next函数直接拿到下一个元素就可以输出（当老妈子），换言之，本题中无论我们用什么数据结构来存储这些数据，next函数能直接拿到的下一个数据必须被处理成为整型，而不能放个list在那，因此我们hasnext函数的功能比较明了，一直处理生数据，直到next函数下一个应该输出的元素被处理为int，并判断还有没有后续元素

   比如[[[1],2],3,[4,[5]]]这个例子，第一个元素是子数组[[1],2]，按顺序1应该先被输出，但是next函数娇生惯养是不会输出数组的，那我们hasnext函数必须先把[[1],2]处理为1和2，或者至少先把1提取出来，next才会输出，对于这种把数据一股脑给我们，但是我们一次只处理其中一部分的操作，我们需要借助stack，所以我们先把数据无论是integer还是list都放入stack，但是这样顺序是倒着的，因此我们在用第二个stack吧顺序颠倒过来（就是我们之前讲过的双stack才能保证数据是正序的），当然之前说这些都是构造函数的活儿，接下来我们说过，每次调用next函数的时候，会在内部先调用hasnext来处理数据和保证有输出，因此下一步我们就进入hasnext来处理数据：我们总体的目的是保证栈顶元素是int，这样next函数直接调用stack.pop()就行了，大致的思路是：
   1. 借助上题的思路，我们需要一个while循环，循环是用来判断现在栈顶的元素是不是integer，如果不是，那么进入循环处理栈顶元素直至next函数可以直接输出栈顶的int元素
   2. 进入循环，那么我们栈顶当前一定是list，我们就用上道题的思路，一次打开一层括号，并将其重新置入栈中，以方便while循环进行下次判断
   3. 直至栈顶元素是int，终于伺候明白了next这个B，那么我们还得判断有没有的输出，直接返回！stack.isEmpty就可以了，stack是我们存储后续数据的地方，如果是空的，那么说明没有后续元素了，也就没有可输出的了

   至于next函数，就先调用hasnext，如果有，输出栈顶元素，没有，报错就行了

   这就是iterator问题的标准思考模式，切记不要预处理，而是要动态处理数据，使用hasnext这个老妈子伺候好next这个公子哥就行了

3. 代码：

```java
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * public interface NestedInteger {
 *
 *     // @return true if this NestedInteger holds a single integer, rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds, if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // @return the nested list that this NestedInteger holds, if it holds a nested list
 *     // Return null if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */
public class NestedIterator implements Iterator<Integer> {
    
    private Stack<NestedInteger> stack = new Stack<NestedInteger>();
    
    public NestedIterator(List<NestedInteger> nestedList) {
        Stack<NestedInteger> temp = new Stack<NestedInteger>();
        for(NestedInteger num: nestedList){
            temp.add(num);
        }        
        while(!temp.isEmpty()){
            stack.add(temp.pop());
        }
    }

    @Override
    public Integer next() {
        if(!this.hasNext()){
            return null;
        }else{
            return stack.pop().getInteger();
        }
        
    }

    @Override
    public boolean hasNext() {
        while(!stack.isEmpty() && !stack.peek().isInteger()){
            Stack<NestedInteger> temp = new Stack<NestedInteger>();
            List<NestedInteger> list = stack.pop().getList();
            for(NestedInteger num: list){
                temp.add(num);
            }
            while(!temp.isEmpty()){
                stack.add(temp.pop());
            }
        }
        return !stack.isEmpty();
    }
}

/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i = new NestedIterator(nestedList);
 * while (i.hasNext()) v[f()] = i.next();
 */
```

#### 九章算法班到此结束~祝大家拿到心仪的offer

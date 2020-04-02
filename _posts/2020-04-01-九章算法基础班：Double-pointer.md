---
layout:     post
title:      九章基础班：Double pointer
subtitle:   two sum && partition
date:       2020-04-01
author:     lmcdhr
header-img: img/LeetCode_Sharing.png
catalog: true
tags:
    - Leetcode
    - Double Pointer
    - 九章基础班

---

## double pointer

## 1. 使用场景及题型
#### 1. 同向及反向双指针类：
1. 一般的数列数组中使用，类似于求回文，in-place交换等等



#### 2. Two Sum类问题
1. Two Sum类问题一般都是求数组中多个数字之和的类似问题
2. Two Sum类问题一般有Hash Map解法和双指针解法，一般而言，双指针解法的通用性较好，且时间复杂度较好，但是如果数组没有排序好，需要花费额外的nlogn时间进行排序；但是空间复杂度上，因为不需要额外的hash map，空间复杂度比hash map好



#### 3. Partition类问题
1. Partition类一般是将数组按照指定条件进行分类并重新排列，一般指针记录每一种类型的交界处
2. 一些特殊的Sort也可以算作Partition类问题，比如经典的三色旗（荷兰旗）问题

## 2. 栗子
### 1. 简单的同向及反向双指针
#### leetcode 26. Remove Duplicates from Sorted Array
https://leetcode.com/problems/remove-duplicates-from-sorted-array/
1. 题目：给予一个sorted int[]，要求从将数组中的重复元素去掉并返回不重复元素的个数（如果有n个不重复元素，则将数组前n个元素替换为这不重复的n个元素，并返回n） 
2. 思路：

     这道题在leetcode差评如潮，因为大家觉得题目描述很tricky，按照常理应该是将重复元素去掉，返回一个新的不包含重复元素的数组，而非像题目这样将不重复元素挪到前面；但是做过了一些双指针题目过后，我发现批评多的原因可能是大家没有体会解这类题方法的精髓所在：一般这类使用双指针移动数组内元素的，最简便（总步骤，而非交换步骤走下来最少的）办法，都是将要的元素移到数组的前方。

     大家可以看一下leetcode283. Move Zeroes这道题，我第一次做的时候，想到的思路是一个指针在下一个非0的位置，另一个指针在下一个为0的位置，随后二者交换，这样两个指针都要走一遍整个数组才能结束；然而利用这道题的思路，我只需要一个指针走完整个数组即可（具体解法继续看），这种做法会将所有满足条件的数移动到前方，这样减少了其中一个指针挪动的次数，交换次数保持不变，虽然仍是O(n)的解法，但是实际步数减少了

     接下来说一下思路：
     1. 准备两个指针，置于数组头，一个指针p1遍历全array，另一个指针p2指向下一个非重复数字最后应该所在的位置
     2. p1指针遍历整个数组，如果p1指针与p2指针指向的数字相同：
          1. 可能1：两者在同一位置，那么p1应该继续往下，不予处理
          2. 可能2：不在同一位置，但是数字相同，那么是重复，我们应该让p1继续往下查找一个不重复的数字置入p2的位置，因此仍然不予处理，继续往下
    
     因此这种情况下，继续往下，不予处理
     3. 如果p1指针与p2指针指向的数字不同:
           1. 那么只有一种可能，就是两者不在一个位置，且p1找到了一个与p2不一样的数字，这个数字应该从小到大被置于p2的位置，于是我们对值进行交换，并将p2++，继续查找下一个不重复的数字
     4. p1走到尾，遍历完毕，此时p2从0到n，将全部不重复元素置于数组头部，此时返回p2+1即可
3. 代码：
```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if(nums.length == 0|| nums.length == 1) return nums.length;
        int j = 0;
        for(int i=1; i<nums.length; i++){
            if(nums[i] != nums[j]){
                j++;
                nums[j] = nums[i];
            }
        }
        return j+1;
    }
}
```


### 2. Two Sum类型

#### 永远滴神：leetcode 1. Two Sum
https://leetcode.com/problems/two-sum/
1. 题目：不知道的请去眼科或者脑科
2. 思路：

      本题的思路有两种，一种使用HashMap，时间n空间n，这里不多赘述，这里讲一下使用双指针的办法，因为数组未排序，因此时间复杂度是nlogn（排好序的是n），空间1（本题要求返回下标，因此需要排序的算法不适用，这里给出hash map的解法代码，但是接下来的讲解我们主要讲双指针的思路,双指针类型的代码示例在下一题中给出）

      我们使用两根指针，一个从左往右（从小到大），一个从右往左（从大到小），本题中说有且只有一个解，因此我们获得一个解return即可，
      1. 当两个指针对应的树加和>target时，说明右侧的（较大的）数过大，换言之无论左侧较小的数如何移动，都会大于target,因此我们将右指针左移，再做判断
      2. 同理，当<target时，左指针所在的数加上右指针所有左侧的数都会<target，因此我们应该将左指针右移，再做判断

      算法很好写，但是当时做题想到了一个问题，只移动一个指针是否包含了所有情况？比如>target时，我们移动右指针，那么会不会存在左指针左侧的数+右指针当前的数就==target了？ 答案当然是不会，因为我们左指针能移动到现在的位置，其左侧所有的数都已经做过判断了，而且判断为过小，所以说在我们想到这个问题之前，那些数就已经判断完毕了，因此不存在跳过正确答案的问题

3. 代码：
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        for(int i=0; i<nums.length; i++){
            if(map.containsKey(target - nums[i])){
                return new int[]{map.get(target-nums[i]),i};
            }
            else{
                map.put(nums[i],i);
            }
        }
        return new int[]{};
    }
}
```



#### leetcode 167. Two Sum II - Input array is sorted

https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/
1. 题目：与two sum相同，但是数据已经sort好
2. 思路：双指针
3. 代码:
```
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;
        while(left < right){
            int sum = numbers[left] + numbers[right];
            if(sum == target){
                return new int[]{left+1,right+1};
            }
            else if(sum > target){
                right--;
            }
            else{
                left++;
            }
        }
        return new int[]{};
    }
}
```


#### leetcode 15. 3->4->n Sum

https://leetcode.com/problems/3sum/

https://leetcode.com/problems/4sum/
1. 题目：2 sum中两个数的和改为三个数的和，并返回所有可能的解的集合（不可以有重复）
2. 思路：

      总体的思路不难想，有了2sum的基础，我们遍历整个数组，对于每一个元素i,只需要将2sum中的target变为target-i，并将i之后的数组进行以target-i的two sum即可，本题的一个要点在于去重，那么我们首先考虑，重复的解出现在哪里？
        
      1. 遍历i的时候，出现重复的i
      2. two sum中，左右两个指针在两次不同的处理中处理了相同的数

      针对这两种情况，我们发现重复出现在：处理完某一种情况后，三个指针各自移动，而移动后所在的数与之前的数相同，我们在指针移动时，加入一个while循环使其处理的下一个数不与当前的相等即可

      但是又来了一个问题，i指针是否可以与two sum内指针的数字相同？这个问题的回答是可以，且必须可以，极端的情况下，比如我们数组是若干个1，而找的值是3，那么必须有一个答案是[1,1,1]如果不可以的话，会缺失这个答案；那么是否会造成重复解呢？不会的，i在处理这个数之后，便会除法去重while循环向后移动，不会再处理这个数，而左右指针处理的是i指针右侧的剩余数组，因此在输出上一个答案之后，便也不会再处理这个数字，因此不存在第二次重复；而且我们two sum内部也会使用while循环确保left<right，因此left指针处理完一个数之后，right指针不会再去处理，也不存在重复的问题
3. 引申

     two sum类型其实可以引申到N sum，思路其实就是将n sum一次剥一层，一直到2 sum才renturn，leetcode中最多到4 sum，但是题目的评论区有一个小哥写了n sum的程序，大家可以自行查看
4. 代码：
```
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<>();
        
        for(int i=0; i<nums.length-2; i++){
            int comp = 0-nums[i];
            int temp = nums[i];
            twoSum(temp, nums, comp, res, i+1);
            while(i<nums.length -1 && nums[i+1] == temp ){
                i++;
            }
        }
        
        return res;
    }
    
    private void twoSum(int doing, int[] nums, int comp, List<List<Integer>> res, int startindex){
        int left = startindex;
        int right = nums.length - 1;
        while(left < right){
            int templeft = nums[left];
            int tempright = nums[right];
            int sum = templeft + tempright;
            if(sum == comp){
                List<Integer> subset = new ArrayList<Integer>();
                subset.add(doing);
                subset.add(templeft);
                subset.add(tempright);
                res.add(subset);
                while(left < right && nums[right] == tempright){
                    right--;
                }
                while(left < right && nums[left] == templeft){
                    left++;
                }
            }
            else if(sum > comp){
                right--;
                while(left < right && nums[right] == tempright){
                    right--;
                }
            }
            else{
                left++;
                while(left < right && nums[left] == templeft){
                    left++;
                }
            }
        }
    }
}
```


#### 不等式two sum之一 leetcode 1099. Two Sum Less Than K

https://leetcode.com/problems/two-sum-less-than-k/
1. 题目：给予一个数组，及一个target，要求找到两个比k小的两数之和，且这个和最接近k
2. 思路：

      two sum类型问题使用双指针都可以解决，不同的题最大的区别就在于双指针如何移动，之前我们做了==target的题目，现在做< 或者 >的题其实是一样的，本题中的大概思路：
      1. 也是如果和小于target,那么左指针需要右移，同时判断当前的sum是否距离target更进一步，是的话更新sum
      2. 如果大于等于target，那么右指针左移
3. 代码：
```java
class Solution {
    public int twoSumLessThanK(int[] A, int K) {
        if(A.length < 2) return -1;
        Arrays.sort(A);
        int sum = -1;
        int left = 0;
        int right = A.length -1;
        while(left < right){
            int temp = A[left] + A[right];
            if(temp < K ){
                if(K - temp < K - sum)
                    sum = temp;
                left++;
            }
            else{
                right--;
            }
        }
        return sum;
    }
}
```


#### 不等式two sum之二 leetcode 611. Valid Triangle Number

https://leetcode.com/problems/valid-triangle-number/
1. 题目：给予一个数组，求出所有三个数字的组合的数量，且以这三个数为长度的边可以组成一个三角形
2. 思路：

      三边能形成三角形的条件是：两边之和大于第三边，more specific，较短的两边之和大于较长的那个边，我们可以事先对数组排序，这样就可以实现这一判断

      在做过了上一道题和3sum之后，我们不难想到，我们可以结合这两天题，将题目转换为：使用双指针找到满足a+b>c的所有边，之前说过不同的双指针题目，关键在于思考指针如何移动，在解决这一问题之前，我们应该先考虑：如何设置这几个指针？我们之前已经说过，判断条件是较短两边之和大于第三边，于是我们需要三个指针，而现在我们已经有了一个升序数组，那么我们应该做的是，主指针从数组内第三个数开始，一直到尾，将主指针左侧的数组区域当做我们的判断区间并设立双指针，这样就满足了主指针指向最长边，双指针指向两个较短的边。随后我们便可以考虑指针如何移动的问题

      1. 左右指针数字加和>主指针数字，说明该位置满足条件，但是我们更应该想到，既然当前左指针都满足条件，那么左指针右侧的区间内，都满足>主指针数字的要求，于是我们便可以一次性找到left-right种满足条件的可能，相当于我们把left从当前位置到right-1的范围内都判断完了，换言之，该位置的right指针判断完毕了，所以此时我们应该right--，判断下一个right
      2. 如果不满足条件，说明left过小，我们直接left++即可
3. 代码：
```java
class Solution {
    public int triangleNumber(int[] nums) {
        if(nums.length <= 2) return 0;
        Arrays.sort(nums);
        int count = 0;
        for(int i=2; i<nums.length; i++){
            int left = 0;
            int right = i-1;
            while(left < right){
                int sum = nums[left] + nums[right];
                if(sum > nums[i]){
                    count += right - left;
                    right--;
                }
                else{
                    left++;
                }
            }
        }
        return count;
    }
}
```


### 3. partition问题

#### leetcode 75. Sort Colors
https://leetcode.com/problems/sort-colors/
1. 题目：给予一个数组，内有若干个0,1,2要求整理数组，将所有0置于数组左侧，1中间，2右边（与sort效果相同）
2. 思路：

      这是partition类中经典的三色旗（荷兰旗）问题，这类问题一般是将目标分为几类（一般是三类），进行重新排序，一般解决的方法是，使用双指针，分别记录左右两个类的界限，一个主指针进行遍历，根据不同情况进行交换，这样当主指针与右指针交汇时，应该位于中间的元素也已经被排好，整个过程结束。而对于本题：

      1. 左右指针分别位于头尾，主指针从头开始，左右指针分别代表当前处理到的数字中，0和2的界限
      2. 循环开始，如果当前处理到0，则与左指针的数进行in place交换，并将左指针和主指针++
      3. 如果处理到1，暂时不予理会，主指针++
      4. 如果处理到2，与右指针交换，右指针--，主指针不动

      这里第一次做的时候，有两点不理解：为什么处理到2的时候主指针不动？处理到1的时候不予例会，如果1在左边或者右边，会不会无法到达中间？

      1. 关于主指针不动的问题，我们可以自己推几步，发现左指针因为和主指针初始位置相同，根据我们的思路，左指针的位置总会 <= 主指针，换言之，左指针左侧一定全是0，而左指针和主指针中间的区域内，左指针还没走到过，而主指针已经走过了，所以我们可以判断，可能是1（处理到1不予理会，主指针直接++），不会是0（0都已经跟左指针交换过，并且左指针++），可以看到0和1都是符合我们的预期的，所以主指针++没有问题，但是2呢？如果我们处理到2的时候，主指针++，那么我们就无法处理到从右指针交换过来的数字，如果交换过来的是2或者0，那么就会被跳过，无法被正确操作；一言以蔽之，我们可以确定主指针左侧的数字已经被处理过，所以可以++，但是从右侧交换过来的数字我们仍需要再次处理，因此不可以++，下次循环的时候仍要处理
       2. 关于1会不会被忽略，我们可以极端一点，1在最左侧，这样左指针和主指针就会错开，左指针保持在1这个位置，主指针继续往前，直到发现一个0，将这个1与0交换，这个1就被处理到了，而如果没有0，这个1就在第一位，也是符合要求的；在右边的情况与这个类似，大家可以自行推导一下，就发现没有问题
3. 代码：
```java
class Solution {
    public void sortColors(int[] nums) {
        if(nums.length < 2) return;
        int left = 0;
        int right = nums.length - 1;
        int cur = 0;
        while(cur <= right){
            if(nums[cur] == 0){
                int temp = nums[left];
                nums[left] = 0;
                nums[cur] = temp;
                left++;
                cur++;
            }
            else if(nums[cur] == 2){
                int temp = nums[right];
                nums[right] = 2;
                nums[cur] = temp;
                right--;
            }
            else{
                cur++;
            }
        }
    }
}
```
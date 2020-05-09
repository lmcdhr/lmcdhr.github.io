---
layout:     post
title:      九章算法基础班：Data Structure III
subtitle:   Union Find & Trie
date:       2020-05-08
author:     lmcdhr
header-img: img/LeetCode_Sharing.png
catalog: true
tags:
    - Leetcode
    - Data Structure
    - 九章高级班
---



## Data Structure III



## 1. Union Find



### 1. 基本原理及实现

1. 解决的问题： 

     根据数据的等级关系，来解决数据查询合并的问题，或者说，查询数据是否在一个集合中
2. 实现：

     每一个数据都有一个上级（father），我们只需要记录每个数据及其father，通过while循环逐层查找，直到一个数据的father就是他自己为止



### 2. find函数

1. 功能：查找一个结点的最终father结点
2. 模板1：有路径压缩
```java
    public int find(int target){
        if(father[target] == target){
            return target;
        }
        return father[target] = find(father[target]);
    }
```
3. 模板2：无路径压缩
```java
    public int find(int target){
        if(father[target] == target){
            return target;
        }
        return find(father[target]);
    }
```
4. 二者区别：
    1. 二者唯一的区别在于向下查找时，是否会通过递归给当前查找到的结点赋值，第一种会讲所有子节点的father更新为最终的father，而第二个只会找到father，而不会更新中间的结点
    2. 无路径压缩，find函数再次查找的时候时间复杂度为O(n),因为需要从头找到尾，而路径压缩完毕后，直接就可以找到father了,但是修改只需要更改终极father即可，复杂度O(1)
    3. 使用路径压缩，find函数时间复杂度在第一次之后，相同路径上的点查找都变为O(1)，但是如果需要修改，时间是O(n)
5. 时间复杂度：

    严格意义来说，路径压缩之后的find时间复杂度是log*n（是log start n而不是log n，请自行google），但是我们在日常工作中认为是O(1)（leetcode中当做logn来看，不知为何）



### 3. union函数

1. 功能：将两个点合并（通过设置father连接到一起）
2. 代码：
```
    public void union(int a, int b){
        int root_a = find(a);
        int root_b = find(b);
        if(root_a != root_b) father[root_a] = root_b;
    }
```
3. 一点说明

     关于最后一句的设置顺序，没有严格要求，设置谁是father都可以



### 4. rank table

1. 由来：

   之前我们已经提到，union的过程相当于给数据之间定义了一种主从关系（我们定义的，而非数据本身的），但是我们只是定义了一种默认的合并关系，而实际应用中，我们合并根节点不同的两个点时，应该把根节点所在的树的高度（秩）小的一方并入较大的一方（类似于更小的树并入更大的树之中），这样新生成的树的高度会是较大树的高度，否则就会变成较大的树的高度+1,而且在某些情况下，会出现极端情况，即出现一条线状的树。
2. 实现：

   使用一个与原数据维度相同的矩阵，当某数据不是任何一个点的父节点时，rank值为1；两个点合并时，rank值较小的一个并入rank值较大的一个，如果相同，那么定义一种默认的顺序，且将父节点的rank+1
3. 作用：

   此举本质上为两个点合并时的一种优化，为了使新生成的树高度最小，从而减少find函数找到相关元素的步数



### 5. 考察题型：

   一般是图或者矩阵，目的是查询两个点是否在一个集合内（连通），或者是查询某类集合的数量（有多少独立连通块），总而言之是图中的连接问题



### 6. 相关思考

   一般用并查集可以做出来的题，用bfs/dfs都可以做出来，但是有些题（比如graph valid tree一题），使用并查集比两者更优，毕竟dfs或bfs本质其实就是图的遍历，要遍历所有的点；使用并查集优化bfs/dfs与我们之前dp优化bfs/dfs有异曲同工之妙，也是借助了之前存储的信息（已经连接好的点）



## 2. Trie Tree



### 1. Trie的实现

   请看下面栗子中implement trie一题

   Trie本质就是一棵树，每个结点保存单词中的一个字母，于是从root开始，每到一个叶子结点，就可以从头至尾表达一个单词；不过需要注意上题中isfinished这一变量，因为某个单词很可能包含于另外一个单词中，比如app和apple，我们需要在单词结尾所在的结点加入isfinished的boolean类型变量来标记，表示从root开始至此已经是一个单词，否则这些包含于其中的单词就不会被识别出来了



### 2. Trie与hash

   二者都可以实现这种保存单词的操作，但是Trie的空间复杂度优于hash，因为如果出现上述的单词包含问题，在trie中只需要保存较长的那个单词，而hash中需要保存两个单独的单词，因此trie的空间复杂度更好



### 3. 解决的问题

1. 单词中挨个字母遍历
2. 已经使用hash，需要优化空间
3. prefix查找问题



## 3. 栗子



### 1. Union Find



#### 1. dfs的优化：leetcode 261. Graph Valid Tree

https://leetcode.com/problems/graph-valid-tree/
1. 题目：给予一个二维数组（维度n*2），内部每个数组代表一组点，表示两个点有连接关系，要求判断这些点组成的图是否能构成树
2. 思路：

     本题之前bfs处做过，当时使用hashmap存储每个点和他相邻的点，再bfs全部遍历，既要遍历边，也要遍历点，时间空间复杂度都是O(N+E),这次我们使用union find尝试优化

     树的定义是无向图，每个点有且只有一条边与另一个点连通，没有环，所有点连通；因此我们可以通过并查集将已经联通的点进行记录（使用路径压缩），如果图中存在环，那么两个点的father结点会是相同的，我们直接返回false即可，判断完没有环的循环过后，我们再判断所有点是否连通，这里直接判断点的数量是否是边的数量+1即可

     这里我们可以看到，uf的算法仅仅遍历边，因为他的本质是找数据之间的连接关系，换言之就是找边，而使用bfs，我们既要遍历边，即点与点之间的联系，也要遍历点来查看是否有环，因此本题算是使用union find对bfs的优化；而很多连通问题上，使用union find起码会保证性能不比bfs/dfs差

3. 代码
```java
class Solution {
    public boolean validTree(int n, int[][] edges) {
        
        int[] uf = new int[n];
        for(int i=0; i<uf.length; i++){
            uf[i] = i;
        }
        
        for(int[] edge: edges){
            if(union(edge[0], edge[1],uf) == false)
                return false;
        }
        return edges.length == n-1;
    }
    
    private boolean union(int a, int b, int[] uf){
        int x = find(a, uf);
        int y = find(b, uf);
        if(x != y){
            uf[x] = y;
            return true;
        }
        else{
            return false;
        }
    }
    
    private int find(int a, int[] uf){
        if(uf[a] == a){
            return a;
        }
        return uf[a] = find(uf[a],uf);
    }
}
```
4. 类似题型：

    leetcode 323. Number of Connected Components in an Undirected Graph

    https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/



#### 2. 老题新作 leetcode 305. Number of Islands II

https://leetcode.com/problems/number-of-islands-ii/

1. 题目：number of island I 是求出连接岛的数量，这次是将输入的数列看成是每次输入一个子数组，要求输出每次输入子数组后，对应的islands数量

2. 思路：

    number of island I 我们已经解出如何利用三种方法(bfs,dfs,unionfind)解题，那么本题最直接的思路就是每次新输入一个子数组，就做一次number of island I, 时间复杂度kmn（数组长度k，矩阵维度m*n），本题我们使用unionfind进行优化

    本题中，使用bfs和dfs的劣势在于，每新输入一个点，我们需要重新遍历整个图，之前的结果完全用不上；但是如果使用union find，每次图形更新时，我们只需要查看：
    1. 是否已经更新过该点？如果更新过，continue
    2. 如果是新的点，那么可能出现了一个新的岛，我们count++
    3. 在此之后，查看他四周有没有已经存在的点？如果有，且他们的父节点不一致（说明并不是一个连通块内）的话，说明这个点并不可以作为新的岛，而是其他连通块新进入的部分，直接count--
    4. 四周每有一个点，就要count--一次，因为如果四周有多个岛，那么这个新点的加入会将他们连在一起，使用rank table进行合并

    本题中我们可以看到，union find巧妙地利用了每次输入后的图形，对一个新的点，我们检查其四周就可以得出连通块个数的变化，时间复杂度简化为(m*n+k)，而且m*n是我们初始化的时候所耗费的，真正花费在处理上的时间复杂度只是k

3. 代码：
```java
class Solution {

    int[] uf;
    int[] rank;
    int[][] graph;
    List<Integer> res;
    int count;
    int[][] directions = new int[][]{{1,0},{-1,0},{0,1},{0,-1}};
    
    public List<Integer> numIslands2(int m, int n, int[][] positions) {
        res = new ArrayList<Integer>();
        if(positions == null || positions.length == 0)
            return res;
        uf = new int[m*n];
        rank = new int[m*n];
        graph = new int[m][n];
        count = 0;
        
        for(int i=0; i<m*n; i++){
            uf[i] = i;
            rank[i] = 1;
        }

        for(int[] position: positions){
            int x = position[0];
            int y = position[1];
            if(graph[x][y] == 1){
                res.add(count);
                continue;
            }
            count++;
            graph[x][y] = 1;
            unionFind(x,y);
            res.add(count);
        }
        
        return res;
    }
    
    private int find(int x){
        if(uf[x] == x)
            return x;
        return uf[x] = find(uf[x]);
    }
    
    private void union(int x, int y){
        int root_x = find(x);
        int root_y = find(y);
        
        if(root_x == root_y)
            return;
        if(rank[root_x] > rank[root_y]){
            uf[root_y] = root_x;
        }else if(rank[root_x] < rank[root_y]){
            uf[root_x] = root_y;
        }else{
            uf[root_x] = root_y;
            rank[root_y]++;
        }
    }
    
    private void unionFind(int x, int y){
        for(int[] direction: directions){
            int new_x = x+direction[0];
            int new_y = y+direction[1];
            if(new_x<0 || new_y<0 || new_x>=graph.length || new_y>=graph[0].length || graph[new_x][new_y] == 0){
                continue;
            }
            int new_cor = new_x*graph[0].length+new_y;
            int cor = x*graph[0].length+y;
            if(find(new_cor) != find(cor)){
                union(cor,new_cor);
                count--;
            }
        }
    }
    
}
```



### 2. Trie



#### 1. 万恶之源 leetcode 208. Implement Trie (Prefix Tree)

https://leetcode.com/problems/implement-trie-prefix-tree/

1. 题目：实现一个trie树，要求实现insert， search 和 start with（prefix search）功能
2. 思路：

     trie树的基本操作，不多说了，需要注意设置isFinished这个变量，以及单词互相包含的问题；其余都是基本tree的操作与遍历

3. 代码：
```java
class TrieNode{

    private Map<Character,TrieNode> children;
    private char word;
    private boolean isFinish;
    
    public TrieNode(){
        this.word = ' ';
        this.children = new HashMap<Character,TrieNode>();
        this.isFinish = false;
    }
    
    public char getWord(){
        return this.word;
    }
    
    public void setWord(char word){
        this.word = word;
    }
    
    public void setFinish(boolean result){
        this.isFinish = result;
    }
    
    public void setNext(TrieNode node){
        this.children.put(node.word,node);
    }
    
    public boolean hasFinished(){
        return this.isFinish;
    }
    
    public boolean containsWord(char word){
        return this.children.containsKey(word);
    }
    
    public TrieNode getNextNode(char word){
        return this.children.get(word);
    }
}

class Trie {
    
    private TrieNode root;

    /** Initialize your data structure here. */
    public Trie() {
        this.root = new TrieNode();
    }
    
    /** Inserts a word into the trie. */
    public void insert(String word) {
        char[] words = word.toCharArray();
        TrieNode cur = root;
        for(int i=0; i<words.length; i++){
            char w = words[i];
            if(cur.containsWord(w)){
                cur = cur.getNextNode(w);
            }
            else{
                TrieNode newnode = new TrieNode();
                newnode.setWord(w);
                cur.setNext(newnode);
                cur = newnode;
            }
            if(i==words.length-1){
                cur.setFinish(true);
            }
        }
    }
    
    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        char[] words = word.toCharArray();
        TrieNode cur = root;
        for(char w: words){
            if(cur.containsWord(w)){
                cur = cur.getNextNode(w);
            }
            else{
                return false;
            }
        }
        return cur.hasFinished();
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        char[] words = prefix.toCharArray();
        TrieNode cur = root;
        for(char w: words){
            if(cur.containsWord(w)){
                cur = cur.getNextNode(w);
            }
            else{
                return false;
            }
        }
        return true;
    }
}

/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
```



#### 2. leetcode 211. Add and Search Word - Data structure design

https://leetcode.com/problems/add-and-search-word-data-structure-design/

1. 题目：实现一个数据结构，能够存储单词并搜索，搜索时要求可以使用.作为万能符号（类似于正则搜索，例如b.d, ..p）
2. 思路：

     存储单词并且搜索，毫无疑问用trie，本题的难点在于对.的处理，不过思路也不难，只需要使用递归，对所有.所在的位置内，不为空的字母进行下一级搜索即可；我们递归是对单词中的字母依次向下处理，因此我们可知传入的参数是单词本身，我们所处的index，以及当前到达trie树的结点cur；递归的退出条件是到达单词末尾

3. 代码：
```java
class TrieNode{
    
    TrieNode[] children;
    boolean isFinished;
    
    public TrieNode(){
        children = new TrieNode[26];
        for(int i=0; i<26; i++){
            children[i] = null;
        }
        isFinished = false;
    }
}

public class WordDictionary {
    
    TrieNode root;
    
    public WordDictionary() {
        root = new TrieNode();
    }
 
    public void addWord(String word) {
        TrieNode cur = root;
        char[] words = word.toCharArray();
        for(int i=0; i<words.length; i++){
            char w = words[i];
            if(cur.children[w-'a'] != null){
                cur = cur.children[w-'a'];
            }else{
                TrieNode newnode = new TrieNode();
                cur.children[w-'a'] = newnode;
                cur = newnode;
            }
        }
        cur.isFinished = true;
    }
    
    
    public boolean search(String word) {
        char[] words = word.toCharArray();
        return find(0,words,root);
    }
    
    private boolean find(int index, char[] words, TrieNode cur){

        if(index == words.length){
            return cur.isFinished;
        }
        char w = words[index];
        if(w == '.'){
            for(int i=0; i<26; i++){
                if(cur.children[i] != null){
                    if(find(index+1, words, cur.children[i]))
                        return true;
                }
            }
            return false;
        }else if(cur.children[w - 'a'] != null){
            return find(index+1, words, cur.children[w - 'a']);
        }else{
            return false;
        }
    }
}

/**
 * Your WordDictionary object will be instantiated and called as such:
 * WordDictionary obj = new WordDictionary();
 * obj.addWord(word);
 * boolean param_2 = obj.search(word);
 */
```



#### 3. trie+dfs leetcode 212. Word Search II

https://leetcode.com/problems/word-search-ii/

1. 题目：给予一个二维char矩阵，以及一些单词，要求找出在二维矩阵中，从任意一点开始，向四个方向移动，可以找出的单词（就是实现圈词游戏）

2. 思路：

    本题最直观的思路是纯dfs和back tracking，从一个点出发，向四个方向dfs，每得到新的单词，就对比单词列表，查看是否有相同的，有的话记录结果；我们能否对这一过程进行优化呢？ 

    trie树所解决的问题是更快捷的单词搜索，那么本题中有两个单词搜索过程，一个是从矩阵获得单词，一个是从单词列表进行比对，那么这两个过程我们可以使用trie优化吗？

    1. 矩阵获得单词：我们知道trie树需要一个root点，从该点开始记录单词，即需要一个起始点，但是本题中，明确的说明可以从矩阵任意一点开始，如果我们要给矩阵创建trie树，我们一共需要(m*n)这么多颗树，因为每一个点都可能是起始点，这样并不能做到优化，反而弄巧成拙，因此我们不选择trie

    2. 比对所获结果：单词列表内是一些单词，而我们搜索的话是每个单词从头至尾，符合我们之前两道题中创建trie的过程以及要求，使用trie树可以比简单的遍历更快；即使使用hash，我们已经提过了trie空间上更好，因此我们选择trie优化这一部分

    之后便是实现代码，我们从矩阵的每一个点开始dfs+backtracking，每获得一个单词，丢入trie进行搜索；本题时间复杂度非常tricky，这里不做讨论，感兴趣的同学可以去leetcode看大佬的解答

3. 代码：
```java
class TrieNode{
    TrieNode[] children;
    boolean isFinished;
    public TrieNode(){
        this.children = new TrieNode[26];
        for(int i=0; i<26; i++){
            this.children[i] = null;
        }
        this.isFinished = false;
    }
}

class Solution {
    
    Set<String> res;
    TrieNode root;
    int[][] directions = new int[][]{{0,1},{0,-1},{1,0},{-1,0}} ;
    int[][] visited;
    
    public List<String> findWords(char[][] board, String[] words) {
        res = new HashSet<String>();
        if(board == null || board.length == 0 || words == null || words.length == 0){
            return new ArrayList<String>();
        }
        root = new TrieNode();
        visited = new int[board.length][board[0].length];
        for(String word: words){
            addNode(root,word);
        }
        searchWords(board);
        return new ArrayList<String>(res);
    }
    
    private void addNode(TrieNode cur, String word){
        char[] w = word.toCharArray();
        for(int i=0; i<w.length; i++){
            if(cur.children[w[i] -'a'] != null){
                cur = cur.children[w[i] -'a'];
            }
            else{
                TrieNode newNode = new TrieNode();
                cur.children[w[i] -'a'] = newNode;
                cur = newNode;
            }
        }
        cur.isFinished = true;
    }
    
    private void searchWords(char[][] board){
        String temp = "";
        for(int i=0; i<board.length; i++){
            for(int j=0; j<board[0].length; j++){
                if(root.children[board[i][j]-'a'] != null){
                    visited[i][j] = 1;
                    dfs(i,j,board,root.children[board[i][j]-'a'],temp+board[i][j]);
                    visited[i][j] = 0;
                }
            }
        }
    }
    
    private void dfs(int i, int j, char[][] board, TrieNode cur, String temp){
        if(cur.isFinished == true){
            res.add(temp);
        }
        if(isLast(cur)){
            return;
        }
        for(int[] direction: directions){
            int new_i = i+direction[0];
            int new_j = j+direction[1];
            if(new_i<0 || new_j<0 || new_i>=board.length || new_j>=board[0].length || visited[new_i][new_j] == 1){
                continue;
            }
            char word = board[new_i][new_j];
            if(cur.children[word-'a'] != null){
                temp += word;
                visited[new_i][new_j] = 1;
                dfs(new_i, new_j, board, cur.children[word-'a'], temp);
                temp = temp.substring(0,temp.length()-1);
                visited[new_i][new_j] = 0;
            }
        }
    }
    
    private boolean isLast(TrieNode cur){
        for(int i=0; i<26; i++){
            if(cur.children[i] != null){
                return false;
            }
        }
        return true;
    }
    
}
```

4. 类似题型： leetcode 425. Word Squares
https://leetcode.com/problems/word-squares/
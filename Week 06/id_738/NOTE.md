# 第六周学习总结 + 双向BFS代码模板 + 分析单词搜索2用Tire树方式实现的时间复杂度
## 知识点总结
### 字典树
- 字典树：Trie树、单词查找树、键树
  - 应用
   - 统计和排序大量的字符串(但并不仅限于字符串)，经常被用在搜索引擎系统用于文本词频统计
  - 基本性质结构
    - 树形结构
    - 节点本身不存完整单词
    - 从根节点到某一节点，路径上经过的字符连接起来，为该节点对应的字符串（图中节点里面的字符并不是代表节点会存这么一个或者多个字符）
    - 每个节点的所有子节点路径代表的字符都不同
    - 如果涉及到统计频次，那么节点存储额外的数字，代表频次
  - 优点：
    - 最大限度地减少无谓的字符串比较
    - 查询效率比哈希表高
  - 核心思想
    - 空间换时间
    - 利用字符串的公共前缀来降低查询时间的开销以达到提高效率的目的
  - 代码模板：

```python

class Trie(object):
  
    def __init__(self): 
        self.root = {} 
        self.end_of_word = "#" 
 
    def insert(self, word): 
        node = self.root 
        for char in word: 
            node = node.setdefault(char, {}) 
        node[self.end_of_word] = self.end_of_word 
 
    def search(self, word): 
        node = self.root 
        for char in word: 
            if char not in node: 
                return False 
            node = node[char] 
        return self.end_of_word in node 
 
    def startsWith(self, prefix): 
        node = self.root 
        for char in prefix: 
            if char not in node: 
                return False 
            node = node[char] 
        return True

```

### 并查集

- 不会，压根就不会，一会，直接用就行了。。。
- 适用场景
  - 组团、配对问题：判断两个东西是不是一个集体中
- 并查集基本操作
  - makeSet(s): 建立一个新的并查集，其中包含s个单元素集合。
  - unionSet(x, y): 把元素x和元素y所在的集合合并，要求x和y所在的集合不相交，如果相交则不合并。
  - find(x): 找到元素x所在的集合的代表，该操作也可以用于判断两个元素是否位于同一个集合，只要它们各自的代表比较一下就可以了。
- 并查集和BFS、DFS的运用场景差异一些做题总结

```
这周课程中的题目，并查集解题 的 时间复杂度 永远比 BFS,DFS要高。

对静态的集体数据，一次性解决问题，用并查集方法并不是最优的。

但是对于变化的集体数据，并查集可以基于已经构建好的数据空间，进行调整和改动，并能在O(1)下随时查出两者的归组问题，这是BFS,DFS做不到的。

在动态变化的情况下，并查集还是空间换时间的思想。
```

- 实现代码

```python
def init(p): 
    # for i = 0 .. n: p[i] = i; 
    p = [i for i in range(n)] 
 
def union(self, p, i, j): 
    p1 = self.parent(p, i) 
    p2 = self.parent(p, j) 
    p[p1] = p2 
 
def parent(self, p, i): 
    root = i 
    while p[root] != root: 
        root = p[root] 
    while p[i] != i: # 路径压缩
        x = i
        i = p[i]
        p[x] = root 
    return root
```

### 高级搜索

#### 优化搜索
- 初级搜索
  - 朴素搜索
  - 优化方式：不重复，剪枝
  - 搜索方向：
    - DFS
    - BFS
    - 双向搜索
    - 启发式搜索

#### 双向BFS
- 代码模板

```python
# 如果搜索变换结果有限制在一个列表limit里面，那么先转化为set方便后面查找
limit = set(limit)
# 特殊值过滤，如果题解要求搜索目标一定要在limit里面，则要过滤掉哪些没有在里面的情况返回
if target not in limit:
    return
# 头尾BFS，定义头尾集合，存储头尾每一层到达的值
head = {start}
tail = {target}
# 遍历步数，按照题解可能为0或者-1
step = 0
# 对头尾进行广度优先搜索，这里有个细节处理，始终将head和tail长度小的集合赋值给head
# 这样搜索效率更高
while head:
    # 每一层步数+1
    step += 1
    # 准备下一层到达的值的集合
    next_head = set()
    # 处理本层逻辑
    for n in head:
        next_n = get_next_level_node(n) # 通过本层的节点获取下一层处理节点
        # 如果替换的值在tail里面找到表示搜索到了，返回步数 或者 结果
        if new_n in tail: 
            return step （or） value 
        if new_n in limit: # 如果新节点满足题意限制条件
            next_head.add(new_n) # 加入到一下层处理节点集合中
            # 遍历过的节点从限定列表里面移除，防止重复遍历
            limit.remove(new_n)
    # 更新head集合为下一层的值的集合
    head = next_head
    # 头尾互换，前面提到的 始终将head和tail长度小的集合赋值给head
    if len(head) > len(tail):
        head, tail = tail, head
# 没有找到返回异常结果值-1
return -1

```

#### 启发式搜索

- 启发式搜索：智能搜索、A*搜索
  - 通过优先级去进行搜索
  - 代码模板：

```python
def AstarSearch(graph, start, end):
    pq = collections.priority_queue() #优先级 -> 估价函数
    pq.append([start])
    visited.add(start)
    while pq:
        node = pq.pop() # can we add more inteligence here
        visited.add(node)

        process(node)
        nodes = generate_related_nodes(node)
        unvisited = [node for node in nodes if node not in visited]
        pq.push(unvisited)
```

- 估价函数
  - h(n)，它用来评价哪些节点是最有希望的是一个我们要找的及诶点，h(n)会返回一个非负实数，也可以认为是从节点n的目标节点路径的估计成本。
  - 启发式函数式一种告知搜索方向的方法。它提供了一种明智的方法来猜测哪个邻居节点会导向一个目标。
  - [相似度测量方法](https://dataaspirant.com/2015/04/11/five-most-popular-similarity-measures-implementation-in-python/)
  - [8 puzzles 解法比较](https://zxi.mytechroad.com/blog/searching/8-puzzles-bidirectional-astar-vs-bidirectional-bfs/)

### 红黑树和AVL树

#### 平衡树的作用和种类

- 二叉搜索树在最坏的情况下，可能是一条“棍子”，搜索时间复杂度退化到O(n)。
  
- 保证性能的关键
  - 保证二维维度 -> 左右子树节点平衡
  - Balanced -> 平衡二叉树
- [平衡树](https://en.wikipedia.org/wiki/Self-balancing_binary_search_tree)：面试着重掌握
  - 2-3树
  - AVL树
  - B树
  - 红黑树

#### AVL树
- AVL树
  - 发明者G.M.Adelson-Velsky和Evgenii Landis
  - Balance Factor(平衡因子)：
    - 左子树的高度减去它的右子树的高度(有时相反)。
    - balance factor = {-1, 0, 1}
  - 平衡二叉搜索树
  - 每个节点存平衡因子balance factor = {-1, 0, 1}
  - 不足
    - 节点需要存储额外信息
    - 调整次数频繁
  - 判断是否是AVL树
    - 扫描每个节点，看节点存储的旋转因子是否为{-1, 0, 1}
  - 通过旋转操作来进行平衡（四种）（注意旋转节点带有子树的情况）
    - 左左型 -> 右旋平衡
    - 右右型 -> 左旋平衡
    - 左右型 -> 先左旋，再右旋平衡
    - 右左型 -> 先右旋，再左旋平衡

#### 红黑树

- 红黑树
  - 近似平衡二叉树
  - 能够确保任何一个节点的左右子树的高度差小于两倍
    - 比如左子树高度为2，右子树最大高度为4
    - 比如右子树高度为5，左子树最大高度为10
- 红黑树是满足如下条件的二叉搜索树：
  - 每个节点要么是红色，要么是黑色
  - 根节点是黑色
  - 每个叶节点是黑色的，且是空节点
  - 不能有相邻接的两个红色节点
  - 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点
- 性质
  - 从根到叶子节点最长的可能路径不多于最短的可能路径的两倍长

## 分析单词搜索 2 用 Tire 树方式实现的时间复杂度

[单词搜索2](https://leetcode-cn.com/problems/word-search-ii/)

```python
class Solution(object):
    def findWords(self, board, words):
        """
        单词搜索2：https://leetcode-cn.com/problems/word-search-ii/

        :type board: List[List[str]]
        :type words: List[str]
        :rtype: List[str]
        """
        def dfs(x, y, cur_word, cur_node, board):
            if '#' in cur_node:
                result.append(cur_word)
            tmp, board[y][x] = board[y][x], None
            for (dx, dy) in ((-1, 0), (1, 0), (0, -1), (0, 1)):
                _x, _y = x + dx, y + dy
                if 0 <= _x < col_size and 0 <= _y < row_size and board[_y][_x] and board[_y][_x] in cur_node:
                    dfs(_x, _y, cur_word + board[_y][_x], cur_node[board[_y][_x]], board)
            board[y][x] = tmp

        root = {}
        for word in words:
            node = root
            for w in word:
                node = node.setdefault(w, {})
            node['#'] = '#'

        result, col_size, row_size = [], len(board[0]), len(board)
        for n in range(col_size):
            for m in range(row_size):
                if board[m][n] in root:
                    dfs(n, m, board[m][n], root[board[m][n]], board)
        return set(result)
```

- 时间复杂度分析

```
设words长度为 k ,每个单词的平均长度为 t , board长宽为 n * m
1. 构建words的字典树时间复杂度为 O(k * t)
2. 对board的每个字母进行扫描，时间复杂度为 O(n * m)
3. 对board的每个字母进行dfs操作
 3.1 从字典树的根节点，判断到该字母为止的字符串是否为一个单词，时间复杂度为O(1)
 3.2 对该字母的扩散4个方向的字母，进行下探递归dfs
   3.2.1 这里可以理解为一个四叉树，四叉树的平均深度为字典树的平均深度，也就是单词的平均长度 t 
   3.2.2 深度为 t的四叉树，节点一共有 4^0 + 4^1 + ... + 4^i {i<=t}，等比数列求和得节点 (4^t - 1) / 3
   3.3.3 综上，dfs这个方法的时间复杂度为 O((4^t- 1) / 3)
综上，该题解的时间复杂度为：
O(k * t) + O(n * m * (4^t- 1) / 3) 
规整为：
O(n * m * 4^t) 

```




















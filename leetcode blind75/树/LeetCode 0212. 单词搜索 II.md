> *题目链接：* https://leetcode.cn/problems/word-search-ii/
>
> *视频题解：* https://www.bilibili.com/video/BV1rvWpeDEEn/

# LeetCode 212. 单词搜索 II

## 题目描述

给定一个 `m x n` 二维字符网格 `board` 和一个单词（字符串）列表 `words`， *返回所有二维网格上的单词* 。

单词必须按照字母顺序，通过 **相邻的单元格** 内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母在一个单词中不允许被重复使用。

**举个例子：**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0212-01.png)

```
输入：board = [["o","a","a","n"],["e","t","a","e"],["i","h","k","r"],["i","f","l","v"]], words = ["oath","pea","eat","rain"]
输出：["eat","oath"]
```

## 思路解析

本题可以对前缀树稍加改造，用来保存给定的单词。

首先复习一下前缀树。

类似二叉树，首先需要定义出树的节点。二叉树最多只有两个孩子节点，二叉树的节点C++定义如下。

```cpp
struct TreeNode {
      int val;
      TreeNode *left;
      TreeNode *right;
      //这里忽略构造函数
};
```

这里的前缀树最多有`26`个孩子节点。在每个节点中，使用一个`hash`表来保存`节点值`与`孩子节点`之间的对应关系。前缀树节点C++定义如下。

```cpp
struct TrieNode {
    //字符和指向孩子节点指针的映射关系
    unordered_map<char, TrieNode*> children;
    //既可以用来判断当前节点是否为单词的结尾，又可以保存单词
    string word;
    //这里忽略构造函数
};
```

相比二叉树，前缀树节点的孩子节点比较多，而且前缀树节点中没有`val`值，其实它的`val`值可以通过`父节点`的`children`这个`hash`表推算出来，所以没有必要再增加一个`val`字段。

前缀树节点的结构如下图，图中的`node`字段在真实结构体中是不存在的，为了方便理解`node`相当于二叉树的`val`字段，弄清楚图中节点的布局是搞懂前缀树的关键。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0212-02.png)

由单词`"a"`生成的前缀树如下图。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0212-03.png)

前缀树的根节点是没有类似二叉树`val`字段信息的，单词`"a"`中只有一个字符，生成的前缀树需要两个节点，孩子节点表示字符`‘a’`，孩子节点中的`word`字段不为空表示这个节点为单词`“a”`在前缀树中的最后一个节点。

单词搜索算法如下：

1. 把给定的单词插入到前缀树中。
2. 深度优先搜索字符矩阵`board`，一边搜索一边匹配前缀树。

## C++代码

```cpp
struct TrieNode {
    //字符和指向孩子节点指针的映射关系
    unordered_map<char, TrieNode*> children;
    //既可以用来判断当前节点是否为单词的结尾，又可以保存单词
    string word;

    TrieNode() {
        word = "";
    }
    void insert(string word) {
        TrieNode* cur_node = this;
        for (int i = 0; i < word.length(); ++i) {
            if (cur_node->children.count(word[i]) == 0) {
                //建立新节点和字符之间的映射关系
                cur_node->children[word[i]] = new TrieNode();
            }
            cur_node = cur_node->children[word[i]];
        }
        cur_node->word = word;
    }
};

class Solution {
public:
    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        TrieNode* root = new TrieNode();
        //根据给定的单词构造前缀树
        for (int i = 0; i < words.size(); ++i) {
            root->insert(words[i]);
        }

        vector<string> res;
        //使用set是为了对结果去重
        set<string> tmp_res;
        for (int row = 0; row < board.size(); ++row) {
            for (int col = 0; col < board[0].size(); ++col) {  //矩阵的每一个元素做为起点开始搜索
                findWords_help(board, row, col, root, tmp_res);
            }
        }
        for (auto str : tmp_res) {
            res.push_back(str);
        }
        return res;
    }
    void findWords_help(vector<vector<char>>& board, int row, int col, TrieNode* node, set<string>& res) {
        //回溯条件
        if (row < 0 || col < 0 || row >= board.size() || col >= board[0].size() || board[row][col] == '*' || !(node->children.count(board[row][col]))) {
            return;
        }

        node = node->children[board[row][col]];
        if (node->word != "" && !res.count(node->word)) {
            //这里不能return，因为当前找到的单词有可能是另一个单词的前缀
            res.emplace(node->word);
        }
        char c = board[row][col];
        board[row][col] = '*';
        //搜索前后左右相邻的字符
        findWords_help(board, row - 1, col, node, res);
        findWords_help(board, row + 1, col, node, res);
        findWords_help(board, row, col - 1, node, res);
        findWords_help(board, row, col + 1, node, res);
        board[row][col] = c;
    }
};
```

## java代码

```java
class TrieNode {
    //字符和指向孩子节点指针的映射关系
    Map<Character, TrieNode> children;
    //既可以用来判断当前节点是否为单词的结尾，又可以保存单词
    String word;

    TrieNode() {
        children = new HashMap<>();
        word = "";
    }

    void insert(String word) {
        TrieNode curNode = this;
        for (int i = 0; i < word.length(); ++i) {
            char c = word.charAt(i);
            if (!curNode.children.containsKey(c)) {
                //建立新节点和字符之间的映射关系
                curNode.children.put(c, new TrieNode());
            }
            curNode = curNode.children.get(c);
        }
        curNode.word = word;
    }
}

class Solution {
    
    public List<String> findWords(char[][] board, String[] words) {
        TrieNode root = new TrieNode();
        //根据给定的单词构造前缀树
        for (String word : words) {
            root.insert(word);
        }

        Set<String> tmpRes = new HashSet<>();
        List<String> res = new ArrayList<>();
        for (int row = 0; row < board.length; ++row) {
            for (int col = 0; col < board[0].length; ++col) {  //矩阵的每一个元素作为起点开始搜索
                findWordsHelp(board, row, col, root, tmpRes);
            }
        }
        res.addAll(tmpRes);
        return res;
    }

    private void findWordsHelp(char[][] board, int row, int col, TrieNode node, Set<String> res) {
        //回溯条件
        if (row < 0 || col < 0 || row >= board.length || col >= board[0].length || board[row][col] == '*' || !node.children.containsKey(board[row][col])) {
            return;
        }

        node = node.children.get(board[row][col]);
        if (!node.word.isEmpty() && !res.contains(node.word)) {
            //这里不能return，因为当前找到的单词有可能是另一个单词的前缀
            res.add(node.word);
        }
        
        char c = board[row][col];
        board[row][col] = '*';
        //搜索前后左右相邻的字符
        findWordsHelp(board, row - 1, col, node, res);
        findWordsHelp(board, row + 1, col, node, res);
        findWordsHelp(board, row, col - 1, node, res);
        findWordsHelp(board, row, col + 1, node, res);
        board[row][col] = c;
    }
}
```

## python代码

```python
class TrieNode:
    def __init__(self):
        # 字符和指向孩子节点指针的映射关系
        self.children = {}
        # 既可以用来判断当前节点是否为单词的结尾，又可以保存单词
        self.word = ""

    def insert(self, word):
        cur_node = self
        for char in word:
            if char not in cur_node.children:
                # 建立新节点和字符之间的映射关系
                cur_node.children[char] = TrieNode()
            cur_node = cur_node.children[char]
        cur_node.word = word


class Solution:
    def findWords(self, board: List[List[str]], words: List[str]) -> List[str]:
        root = TrieNode()
        # 根据给定的单词构造前缀树
        for word in words:
            root.insert(word)

        res = set()
        for row in range(len(board)):
            for col in range(len(board[0])):  # 矩阵的每一个元素作为起点开始搜索
                self.findWords_help(board, row, col, root, res)

        return list(res)

    def findWords_help(self, board, row, col, node, res):
        # 回溯条件
        if row < 0 or col < 0 or row >= len(board) or col >= len(board[0]) or board[row][col] == '*' or board[row][col] not in node.children:
            return

        node = node.children[board[row][col]]
        if node.word and node.word not in res:
            # 这里不能return，因为当前找到的单词有可能是另一个单词的前缀
            res.add(node.word)
        
        char = board[row][col]
        board[row][col] = '*'
        # 搜索前后左右相邻的字符
        self.findWords_help(board, row - 1, col, node, res)
        self.findWords_help(board, row + 1, col, node, res)
        self.findWords_help(board, row, col - 1, node, res)
        self.findWords_help(board, row, col + 1, node, res)
        board[row][col] = char
```

## 复杂度分析

**时间复杂度：** 因为这棵前缀树为四叉树树，时间复杂度为*O(mnt4<sup>t-1</sup>)*，其中`m`为`board`的行数，`n`为`board`的列数，`t`为最长单词的长度，4<sup>t-1</sup>是前缀树的叶子数，即一棵树最大搜索路径数。

**空间复杂度：** 空间复杂度跟前缀树的总节点数相关。


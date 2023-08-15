> *题目链接：* https://leetcode.cn/problems/design-add-and-search-words-data-structure/

# LeetCode 211. 添加与搜索单词 - 数据结构设计

## 题目描述

请你设计一个数据结构，支持 **添加新单词** 和 **查找字符串是否与任何先前添加的字符串匹配** 。

实现词典类 `WordDictionary` ：

* `WordDictionary()` 初始化词典对象
* `void addWord(word)` 将 `word` 添加到数据结构中，之后可以对它进行匹配
* `bool search(word)` 如果数据结构中存在字符串与 `word` 匹配，则返回 `true` ；否则，返回  `false` 。`word` 中可能包含一些 `'.'` ，每个 `.` 都可以表示任何一个字母。

**举个例子：**

```cpp
输入：
["WordDictionary","addWord","addWord","addWord","search","search","search","search"]
[[],["bad"],["dad"],["mad"],["pad"],["bad"],[".ad"],["b.."]]
输出：
[null,null,null,null,false,true,true,true]

解释：
WordDictionary wordDictionary = new WordDictionary();
wordDictionary.addWord("bad");
wordDictionary.addWord("dad");
wordDictionary.addWord("mad");
wordDictionary.search("pad"); // 返回 False
wordDictionary.search("bad"); // 返回 True
wordDictionary.search(".ad"); // 返回 True
wordDictionary.search("b.."); // 返回 True
```
## 思路解析

本题可以通过对前缀树稍加改造来实现，首先复习一下前缀树。

类似二叉树，首先需要定义出树的节点。二叉树最多只有两个孩子节点，二叉树的节点一般定义如下。

```cpp
struct TreeNode {
      int val;
      TreeNode *left;
      TreeNode *right;
      //这里忽略构造函数
};
```

这里的前缀树最多有`26`个孩子节点。在每个节点中，使用一个`hash`表来保存`节点值`与`孩子节点`之间的对应关系。前缀树节点定义如下。

```cpp
struct TrieNode {
    //字符和指向孩子节点指针的映射关系
    unordered_map<char, TrieNode*> children;
    //node节点是否是单词的结尾
    bool isWord;
    //这里忽略构造函数
};
```

相比二叉树，前缀树节点的孩子节点比较多，而且前缀树节点中没有`val`值，其实它的`val`值可以通过`父节点`的`children`这个`hash`表推算出来，所以没有必要再增加一个`val`字段。

前缀树节点的结构如下图，图中的`node`字段在真实结构体中是不存在的，为了方便理解`node`相当于二叉树的`val`字段，弄清楚图中节点的布局是搞懂前缀树的关键。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0211-01.png)

由单词`"a"`生成的前缀树如下图。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0211-02.png)

前缀树的根节点是没有类似二叉树`val`字段信息的，单词`"a"`中只有一个字符，生成的前缀树需要两个节点，孩子节点表示字符`‘a’`，孩子节点中的`isWord = true`表示这个节点为单词`“a”`在前缀树中的最后一个节点。

1. **单词的插入**

单词插入步骤和前缀树的插入是一样的，遍历单词，从前缀树的根节点开始，如果单词的字符不在节点的`children`中，就创建新节点，并把新节点和字符之间的关系保存在原节点的`children`中，直到遍历完单词。

```cpp
  void addWord(string word) {
        TrieNode* cur_node = m_root;
        for (int i = 0; i < word.length(); ++i) {
            if (cur_node->children.count(word[i]) == 0) {
                //建立新节点和字符之间的映射关系
                cur_node->children[word[i]] = new TrieNode();
            }
            cur_node = cur_node->children[word[i]];
        }
        cur_node->isWord = true;
  }
```

2. **单词的搜索**

单词搜索和前缀树的搜索有点差别，单词中可能存在万能匹配字符`'.'`，使得单词搜索不只搜索一条路径，一条路径不匹配换下一条路径搜索，所以搜索过程需要用到回溯。

```cpp
 bool search_help(string& word, TrieNode* root, int index) {
        TrieNode* cur_node = root;
        for (int i = index; i < word.length(); ++i) {
            if (word[i] == '.') {
                //每条路径依次搜索
                for (auto& child : cur_node->children) {
                    //有一条路径匹配就返回
                    if (search_help(word, child.second, i + 1)) {
                        return true;
                    }
                }
                return false;    
            } else {
                if (cur_node->children.count(word[i]) == 0) {
                    return false;
                }   
                cur_node = cur_node->children[word[i]];
            }
        }
        return cur_node->isWord;
  }
```

## C++代码

```cpp
struct TrieNode {
    //字符和指向孩子节点指针的映射关系
    unordered_map<char, TrieNode*> children;
    //node节点是否是单词的结尾
    bool isWord;
    TrieNode() {
        children.clear();
        isWord = false;
    }
};

class WordDictionary {
public:
    WordDictionary() {
         m_root = new TrieNode();
    }
    
    void addWord(string word) {
        TrieNode* cur_node = m_root;
        for (int i = 0; i < word.length(); ++i) {
            if (cur_node->children.count(word[i]) == 0) {
                //建立新节点和字符之间的映射关系
                cur_node->children[word[i]] = new TrieNode();
            }
            cur_node = cur_node->children[word[i]];
        }
        cur_node->isWord = true;
    }
    
    bool search(string word) {
        return search_help(word, m_root, 0);
    }

    bool search_help(string& word, TrieNode* root, int index) {
        TrieNode* cur_node = root;
        for (int i = index; i < word.length(); ++i) {
            if (word[i] == '.') {
                //每条路径依次搜索
                for (auto& child : cur_node->children) {
                    //有一条路径匹配就返回
                    if (search_help(word, child.second, i + 1)) {
                        return true;
                    }
                }
                return false;    
            } else {
                if (cur_node->children.count(word[i]) == 0) {
                    return false;
                }   
                cur_node = cur_node->children[word[i]];
            }
        }
        return cur_node->isWord;
    }
private:
    TrieNode* m_root;
};

/**
 * Your WordDictionary object will be instantiated and called as such:
 * WordDictionary* obj = new WordDictionary();
 * obj->addWord(word);
 * bool param_2 = obj->search(word);
 */
```

## 复杂度分析

**时间复杂度：** 插入的时间复杂度和被插入单词的长度相关，搜索的时间复杂度跟整棵树的节点数相关，因为有可能被查找的单词全由`.`组成，这样可能要搜索整棵树。

**空间复杂度：** 跟整棵树的节点数相关。


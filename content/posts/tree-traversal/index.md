---
title: "Tree traversal"
date: 2023-06-15T20:00:00+08:00
draft: false
math: true
tags: ["DSA"]
---

今天來複習一下常見遍歷樹的方式。

## Recursive way
### Inorder traversal
先拜訪 left subtree，再依序拜訪 root node 跟 right subtree。
```c++
void Inorder_traversal(TreeNode *node){
    if (node == NULL){
        return;
    }

    Inorder_traversal(node->left);
    cout << node->val << " ";
    Inorder_traversal(node->rihgt);
    return;
}
```

### Preorder traversal
先拜訪 root node，再依序拜訪 left subtree 跟 right subtree。

```c++
void Preorder_traversal(TreeNode *node){
    if (node == NULL){
        return;
    }

    cout << node->val << " ";
    Preorder_traversal(node->left);
    Preorder_traversal(node->rihgt);
    return;
}
```

### Postorder traversal
先依序拜訪 left subtree 跟 right subtree，再拜訪 root node。

```c++
void Postorder_traversal(TreeNode *node){
    if (node == NULL){
        return;
    }

    Postorder_traversal(node->left);
    Postorder_traversal(node->rihgt);
    cout << node->val << " ";
    return;
}
```

可以看到用 recursive 寫的話，三種方式的 code 基本上長一樣，而且複雜度也都一樣，只是差在遍歷 root node 的時間不一樣。  
**time complexity:** $O(n)$  
**space complextiy:** $O(h)$, where $h = $ tree height

## Iterative way
但是我覺得比較有趣的是 iterative 的方式，三種方式各有要注意的地方。

### Inorder traversal
1. 不斷往左邊走，走的過程中把 current push 到 stack 裡面，直到 current 變成 NULL
2. current = stack.top()
3. 往右邊走
4. 重複步驟 1

由於 Inorder traversal 的順序是 left subtree -> root node -> right subtree，因此步驟 1 要不斷往左邊走。  
當 `current == NULL` 的時候，代表沒有 left subtree 了，此時 `stack.top()` 就會是剛剛的 root node，因此讓 `current = stack.top()`，並且印出來。  
接下來往右邊走，等同於遍歷 right subtree，最後重複步驟 1 直到整個過程結束。

```c++
void Inorder_traversal(TreeNode *node){
    stack<TreeNode*> stk;
    TreeNode *cur = node;
    while (not stk.empty() || cur != NULL){
        while (cur != NULL){
            stk.push(cur);
            cur = cur->left;
        }
        cur = stk.top();
        stk.pop();
        cout << cur->val << " ";
        cur = cur->right;
    }
    return;
}
```

### Preorder traversal 

這邊的 code 基本上跟 Inorder traversal 長一樣，唯一的差別就是印出來的時機。  
由於 Preorder traversal 的順序是 root node -> left subtree -> right subtree，因此在走到 left subtree 之前，就要先印出來。

```c++
void Preorder_traversal(TreeNode *node){
    stack<TreeNode*> stk;
    TreeNode *cur = node;
    while (not stk.empty() || cur != NULL){
        while (cur != NULL){
            cout << cur->val << " ";
            stk.push(cur);
            cur = cur->left;
        }
        cur = stk.top();
        stk.pop();
        cur = cur->right;
    }
    return;
}
```

不過我還有看到[另外一個方法](https://shubo.io/iterative-binary-tree-traversal/)，思路不太一樣。  
這個的想法比較簡單，不斷 push 右邊跟左邊的節點，就可以保證左邊的節點一直在 stack 的上面，進而實現 Preorder traversal。

```c++
void Preorder_traversal(TreeNode *node){
    stack<TreeNode*> stk;
    stk.push(node);
    while (not stk.empty()){
        cur = stk.top();
        stk.pop();
        cout << cur->val << " ";
        if (cur->right != NULL){
            stk.push(cur->right)
        }
        if (cur->left != NULL){
            stk.push(cur->left)
        }
    }
    return;
}
```

不過可以發現 `cur->left` 在 push 進去之後，立刻就被 pop 出來，因此實際上可以不需要 push 進去。  

下面這段 code 跟 Preorder 的第一段長得很像，但是想法不太一樣。  
第一段是往左邊走的時候 push current，並且 pop 的時候再往右邊走。  
這邊是往左邊走的時候 push right child，pop 的時候就是右邊的節點，基本上是一樣的方法。  

要特別注意的是，這邊需要檢查 stack 是否為空的，原因是如果 `cur->right == NULL`，此時並不會 push 任何東西到 stack 裡面。可以用 2 為 root，1 為 left child 的 binary tree 想想看，如果此時沒有檢查 stack 是否為空，會出現錯誤。

```c++
void Preorder_traversal(TreeNode *node){
    stack<TreeNode*> stk;
    TreeNode *cur = node;
    while (not stk.empty() || cur != NULL){
        while (cur != NULL){
            cout << cur->val << " ";
            if (cur->right != NULL){
                stk.push(cur->right);
            }
            cur = cur->left;
        }
        if (not stk.empty()){
            cur = stk.top();
            stk.pop();
        }
    }
    return;
}
```

### Postorder traversal

Postorder traversal 的順序是 left subtree -> right subtree -> root node，其實可以反過來做，也就是 root node -> right subtree -> left subtree 然後再反轉結果就好（如果是把順序存到 vector 裡面的話）。  
root node -> right subtree -> left subtree 就是剛剛做過的 Preorder traversal，只是 left subtre、right subtree 反過來而已。

```c++
vector<int> Postorder_traversal(TreeNode *node){
    vector<int> result;
    stack<TreeNode*> stk;
    TreeNode *cur = node;
    while (not stk.empty() || cur != NULL){
        while (cur != NULL){
            result.push_back(cur->val);
            stk.push(cur);
            cur = cur->right;
        }
        cur = stk.top();
        stk.pop();
        cur = cur->left;
    }
    reverse(begin(result), end(result));
    return result;
}
```

不過這樣不太有效率，因為需要額外的空間來儲存印出來的順序。  

Postorder traversal 難的地方在於在遍歷 right subtree 之後，還要回到 root。  
在前面的 Preorder 跟 Inorder traversal 中，當把 root pop 出來之後，接下來往右走就好，因為接下來都不會再用到 root node。  
但是 Postorder traversal 中，往右走之後，還要想辦法回到 root。  
有一個聰明的方法就是 push 兩次 root node。  
如果是第一次 pop root node（也就是現在要拜訪 right subtree），那麼此時 `cur == stk.top()`，選擇往右走。  
如果是第二次 pop root node（也就是現在要拜訪 root node），那麼此時只要印出來就好。

```c++
void Postorder_traversal(TreeNode *node){
    stack<TreeNode*> stk;
    TreeNode *cur = node;
    while (not stk.empty() || cur != NULL){
        while (cur != NULL){
            stk.push(cur);
            stk.push(cur);
            cur = cur->left;
        }
        cur = stk.top();
        stk.pop();
        if (not stk.empty() && cur == stk.top()){
            cur = cur->right;
        }
        else {
            cout << cur->val << " ";
            cur = NULL;
        }
    }
    return;
}
```

## 參考資料
1. https://shubo.io/iterative-binary-tree-traversal/
2. https://www.geeksforgeeks.org/iterative-preorder-traversal/
3. https://www.geeksforgeeks.org/iterative-postorder-traversal-using-stack/
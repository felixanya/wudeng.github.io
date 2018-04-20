# Morris Tranversal

二叉树的遍历最经典的方法就两种：递归、栈迭代。早在1978年，Knuth大神提了一个问题：不用递推和栈，
是否可以遍历二叉树。1979年Morris同学给出了答案：莫里斯遍历。莫里斯遍历主要解决的问题是，遍历完子节点，
怎么回到父节点。莫里斯给出的答案就是我把它总结为八个字：“遇河搭桥，过河拆桥”。

“遇河搭桥”指的是在遍历子节点之前，先建立好遍历完子节点回到父节点的指针。
“过河拆桥”指的是回到父节点以后，把之前建立的指针重置掉。
至于桥怎么搭，这个思想来源于线索二叉树（Threaded binary tree）。

## 线索二叉树
所谓线索二叉树，也就是二叉树添加了指向节点的前驱和后继的指针的二叉树称为线索二叉树。
* 原本为空的右指针改为指向该节点中序序列的后续。
* 原本为空的左指针改为指向该节点中序序列的前驱。

线索二叉树的优点：
* 线性的遍历，比递归遍历快。
* 方便的找到一个节点的父节点。比显式使用父指针或者栈效率更高。这是因为线索二叉树具有这样的性质：
    - R是K的右孩子，那么从R的左孩子出发，一路向左，最终能找到一个指向K节点前驱指针。 右孩子往左找。
    - Q是P的左孩子，那么从Q的右孩子出发，一路向右，最终能找到一个指向P节点的后续指针。左孩子往右找。

通过利用线索二叉树中建立的后继节点，莫里斯遍历实现了非递归，不用栈，O(1)空间的二叉树遍历。
莫里斯遍历提供了中序遍历，在中序基础上很容易实现前序遍历，而后序遍历则要麻烦一点。

## Morris中序遍历
Inorder：Left -> Root -> Right。

思路是建立中序后续线索，完成遍历后删除线索。

中序遍历过程：
- 如果左孩子为空，输出当前节点，将右孩子设为当前节点
- 如果左孩子不为空，从左孩子开始往右找最右节点
    - 如果最右节点右指针为空，将右指针指向当前节点。当前节点设为左孩子
    - 如果最右节点右指针指向当前节点，将最优节点的右指针置空。当前节点设为右孩子
- 重复以上步骤，直到当前节点为空

在遍历的过程中修改了二叉树，但是第二次回到current的时候的时候会将其前驱节点的右指针置空，恢复原状。
从二叉树中从上往下的时候建立线索，从下往上的时候删除线索。

```cpp
void inorderMorrisTraversal(TreeNode *root) {
    TreeNode *cur = root, *prev = NULL;
    while (cur != NULL)
    {
        if (cur->left == NULL)          // 1.
        {
            printf("%d ", cur->val);
            cur = cur->right;
        }
        else
        {
            // find predecessor
            prev = cur->left;
            while (prev->right != NULL && prev->right != cur)
                prev = prev->right;

            if (prev->right == NULL)   // 2.a)
            {
                prev->right = cur;
                cur = cur->left;
            }
            else                       // 2.b)
            {
                prev->right = NULL;
                printf("%d ", cur->val);
                cur = cur->right;
            }
        }
    }
}
```

## Morris前序遍历
Preorder: Root -> Left -> Right

在中序遍历的基础上稍微修改一下就是前序遍历。前序遍历过程：
- 如果左孩子为空，输出当前节点，将右孩子设为当前节点
- 如果左孩子不为空
    - 从左孩子开始一路向右，直到右指针为空或者指向当前节点
        - 如果为空，将当前节点设为该节点的右孩子，将左孩子设为当前节点
        - 如果指向当前节点，将该节点的右孩子置空，将当前节点指向右孩子
- 重复以上步骤，直到当前节点为空

```cpp
void preorderMorrisTraversal(TreeNode *root) {
    TreeNode *cur = root, *prev = NULL;
    while (cur != NULL)
    {
        if (cur->left == NULL)
        {
            printf("%d ", cur->val);
            cur = cur->right;
        }
        else
        {
            prev = cur->left;
            while (prev->right != NULL && prev->right != cur)
                prev = prev->right;

            if (prev->right == NULL)
            {
                printf("%d ", cur->val);  // the only difference with inorder-traversal
                prev->right = cur;
                cur = cur->left;
            }
            else
            {
                prev->right = NULL;
                cur = cur->right;
            }
        }
    }
}
```

## 后序遍历

相比中序和前序，莫里斯后序遍历要麻烦一些。需要临时建立一个节点dump，令其左孩子为root，需要一个子过程，
用于倒序输出两个节点之间路径上的各个节点。

为什么需要一个临时节点：无论是中序还是前序，遍历完以后，最后一个节点必定是最右下角的节点。Morris遍历
过程中只创建了中序后继节点，意味着只能从左子树回到父节点，而没有办法从右子树回到父节点。但是后序遍历中，
父节点才是最后一个节点，所以访问完右子树以后还需要回到根节点。所以可以通过增加一个临时节点，使得整个二叉树
成为临时节点的左子树。这样就通过回到临时节点的方式间接的回到了根节点。

后序遍历过程：
- 当前节点设为临时节点dump
- 如果当前节点的左子树为空，将右孩子设为当前节点
- 如果当前节点的左子树不为空
    - 找到位于当前节点左孩子最右的前驱节点
        - 如果前驱节点的右孩子为空，将前驱节点的右孩子设为当前节点
        - 如果前驱节点的右孩子不为空，倒序输出从当前节点左孩子到当前节点前驱节点路径上的点，并将前驱节点的右孩子置空
- 重复以上步骤直到当前节点为空

```cpp
void reverse(TreeNode *from, TreeNode *to) // reverse the tree nodes 'from' -> 'to'.
{
    if (from == to)
        return;
    TreeNode *x = from, *y = from->right, *z;
    while (true)
    {
        z = y->right;
        y->right = x;
        x = y;
        y = z;
        if (x == to)
            break;
    }
}

void printReverse(TreeNode* from, TreeNode *to) // print the reversed tree nodes 'from' -> 'to'.
{
    reverse(from, to);

    TreeNode *p = to;
    while (true)
    {
        printf("%d ", p->val);
        if (p == from)
            break;
        p = p->right;
    }

    reverse(to, from);
}

void postorderMorrisTraversal(TreeNode *root) {
    TreeNode dump(0);
    dump.left = root;
    TreeNode *cur = &dump, *prev = NULL;
    while (cur)
    {
        if (cur->left == NULL)
        {
            cur = cur->right;
        }
        else
        {
            prev = cur->left;
            while (prev->right != NULL && prev->right != cur)
                prev = prev->right;

            if (prev->right == NULL)
            {
                prev->right = cur;
                cur = cur->left;
            }
            else
            {
                printReverse(cur->left, prev);  // call print
                prev->right = NULL;
                cur = cur->right;
            }
        }
    }
}
```

## 总结

莫里斯遍历中，中间节点都经历了两次遍历。第一次需要创建指向当前节点的右指针。第二次删除之前创建的右指针。
第一次往左边遍历。第二次往右边遍历。中序和前序的区别就在于：中序是第二次遍历的时候输出，而前序是第一次的时候输出。


## 参考文献
* https://www.geeksforgeeks.org/inorder-tree-traversal-without-recursion-and-without-stack/
* https://www.geeksforgeeks.org/morris-traversal-for-preorder/
* https://www.cnblogs.com/AnnieKim/archive/2013/06/15/MorrisTraversal.html 图片和代码出处

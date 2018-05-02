LeetCode (246) Lowest Common Ancestor of a Binary Tree


```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) return root;
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        return left == null ? right : right == null ? left : root;
    }
}
```


```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool in(TreeNode *root, TreeNode *p) {
        if (root == NULL) {
            return false;
        } else if (root == p) {
            return true;
        } else {
            return in(root->left, p) || in(root->right, p);
        }
    }

    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (root == p || root == q) {
            return root;
        } else if (in(root->left, p)) {
            if (in(root->left, q)) {
                return lowestCommonAncestor(root->left, p, q);
            } else {
                return root;
            }
        } else {
            if (in(root->right, q)) {
                return lowestCommonAncestor(root->right, p, q);
            } else {
                return root;
            }
        }
    }

};
```

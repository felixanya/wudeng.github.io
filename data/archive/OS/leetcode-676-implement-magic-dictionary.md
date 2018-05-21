Leetcode (676) Implement Magic Dictionary

Trie树节点不需要保存char值。char值存在边上。

```java
class MagicDictionary {

    static class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isWord;
        public Node() {
            for (int i=0; i<26; i++) {
                children[i] = null;
            }
        }
    }

    TrieNode root;

    private insert(String key) {
        TrieNode ptr = root;
        int index;
        for (int i=0; i<key.length(); i++) {
            index = key.charAt(i) - 'a';
            if (ptr.children[index] == null) {
                ptr.children[index] = new TrieNode();

            }
        }
    }

    /** Initialize your data structure here. */
    public MagicDictionary() {
        root = new TrieNode();
    }
    
    /** Build a dictionary through a list of words */
    public void buildDict(String[] dict) {
        for (int i = 0; i < dict.length; i++) {
            insert(dict[i]);
        }
    }
    
    /** Returns if there is any word in the trie that equals to the given word after modifying exactly one character */
    public boolean search(String word) {
        
    }
}

/**
 * Your MagicDictionary object will be instantiated and called as such:
 * MagicDictionary obj = new MagicDictionary();
 * obj.buildDict(dict);
 * boolean param_2 = obj.search(word);
 */
```
# 一致性哈希

## 一般方案

hash(key) % N

分布式缓存，节点的增删导致几乎所有key重新映射，对后端服务器造成巨大压力。
而且会发生数据不一致。比方说修改了A缓存服务器上的数据尚未写回数据库，这时映射变化了，客户端发起了读取该记录的请求，通过另外的缓存服务器读取这时读到的就是老的内容。

解决节点动态变化的难点：一致性Hash。

## 一致性哈希 Consistent hashing

* 请求分流与负载均衡
* 分布式缓存
* 分布式存储


每个节点有唯一ID，与Key同构。映射到一个环上。根据Key在环上的位置顺时钟查找Node。
为了解决分布不均的问题，每个节点可以映射到环上的多个位置。

需要支持的操作主要包括：
* 增加节点，在环上增加节点对应的replica个位置
* 删除节点
* 根据key查找环上对应的节点

```
package consistent_hashing
import (
    "crypto/md5"
    "hash/crc32"
    "strconv"
    "sort"
    "sync"
)
type Node struct {
    Id      string
    Address string
}
type ConsistentHashing struct {
    mutex    sync.RWMutex
    nodes    map[int]Node
    replicas int
}
func NewConsistentHashing(nodes []Node, replicas int) *ConsistentHashing {
    ch := &ConsistentHashing{nodes: make(map[int]Node), replicas: replicas}
    for _, node := range nodes {
        ch.AddNode(node)
    }
    return ch
}
func (ch *ConsistentHashing) AddNode(node Node) {
    ch.mutex.Lock()
    defer ch.mutex.Unlock()
    for i := 0; i < ch.replicas; i++ {
        k := hash(node.Id + "_" + strconv.Itoa(i))
        ch.nodes[k] = node
    }
}
func (ch *ConsistentHashing) RemoveNode(node Node) {
    ch.mutex.Lock()
    defer ch.mutex.Unlock()
    for i := 0; i < ch.replicas; i++ {
        k := hash(node.Id + "_" + strconv.Itoa(i))
        delete(ch.nodes, k)
    }
}
func (ch *ConsistentHashing) GetNode(outerKey string) Node {
    key := hash(outerKey)
    nodeKey := ch.findNearestNodeKeyClockwise(key)
    return ch.nodes[nodeKey]
}
func (ch *ConsistentHashing) findNearestNodeKeyClockwise(key int) int {
    ch.mutex.RLock()
    sortKeys := sortKeys(ch.nodes)
    ch.mutex.RUnlock()
    for _, k := range sortKeys {
        if key <= k {
            return k
        }
    }
    return sortKeys[0]
}
func sortKeys(m map[int]Node) []int {
    var sortedKeys []int
    for k := range m {
        sortedKeys = append(sortedKeys, k)
    }
    sort.Ints(sortedKeys)
    return sortedKeys
}
func hash(key string) int {
    md5Chan := make(chan []byte, 1)
    md5Sum := md5.Sum([]byte(key))
    md5Chan <- md5Sum[:]
    return int(crc32.ChecksumIEEE(<-md5Chan))
}
```


```
package main
import (
    "consistent_hashing"
    "net/http"
    "net/http/httputil"
    "net/url"
)
func main() {
    nodes := []consistent_hashing.Node{
        {"0", "http://10.10.1.10/"},
        {"1", "http://10.10.1.11/"},
        {"2", "http://10.10.1.12/"},
    }
    ch := consistent_hashing.NewConsistentHashing(nodes, 100)
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        sign := r.Header.Get("sign")
        node := ch.GetNode(sign)
        uri, _ := url.Parse(node.Address)
        httputil.NewSingleHostReverseProxy(uri)
    })
    http.ListenAndServe(":8080", nil)
}
```

怎样创建多个虚拟节点：
    replicas
    for i := 0; i < replicas; i++ {
        k := hash(node.id + "_" + strconv:Itoa(i))
        nodes[id] = node
    }

怎样找到key对应的节点
    排序，遍历


* http://www.zsythink.net/archives/1182
* https://leileiluoluo.com/posts/consistent-hashing-and-high-available-cluster-proxy.html

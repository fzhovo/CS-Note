# LRU (LinkedHashMap)

```go
type Node struct {
    Key int
    Val int
}

type LRUCache struct {
    Map   map[int]*list.Element
    Cache *list.List
    Cap   int
}

func Constructor(capacity int) LRUCache {
    return LRUCache{
        Map:   make(map[int]*list.Element),
        Cache: list.New(),
        Cap:   capacity,
    }
}

func (lru *LRUCache) Get(key int) int {
    if _, ok := lru.Map[key]; !ok {
        return -1
    }
    // 将该数据提升为最近使用的
    lru.makeRecently(key)
    return lru.Map[key].Value.(*Node).Val
}

func (lru *LRUCache) Put(key int, value int) {
    if _, ok := lru.Map[key]; ok {
        // 删除旧的数据
        lru.deleteKey(key)
        // 新插入的数据为最近使用的数据
        lru.addRecently(key, value)
        return
    }
    if lru.Cache.Len() == lru.Cap {
        // 删除最久未使用的元素
        lru.removeLeastRecently()
    }
    // 添加为最近使用的元素
    lru.addRecently(key, value)
}

// 将某个key提升为最近使用的
func (lru *LRUCache) makeRecently(key int) {
    e := lru.Map[key]
    // 移到链表尾部
    lru.Cache.MoveToBack(e)
}

// 添加最近使用的元素
func (lru *LRUCache) addRecently(key, val int) {
    // 链表尾部就是最近使用的元素
    lru.Cache.PushBack(&Node{Key: key, Val: val})
    // 在map中添加key的映射
    lru.Map[key] = lru.Cache.Back()
}

// 删除某一个key
func (lru *LRUCache) deleteKey(key int) {
    e := lru.Map[key]
    // 从链表中删除
    lru.Cache.Remove(e)
    // 从map中删除
    delete(lru.Map, key)
}

// 删除最久未使用的元素
func (lru *LRUCache) removeLeastRecently() {
    // 链表头部的第一个元素就是最久未使用的
    e := lru.Cache.Front()
    // 从链表中删除
    lru.Cache.Remove(e)
    // 从map中删除
    delete(lru.Map, e.Value.(*Node).Key)
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * obj := Constructor(capacity);
 * param_1 := obj.Get(key);
 * obj.Put(key,value);
 */
```
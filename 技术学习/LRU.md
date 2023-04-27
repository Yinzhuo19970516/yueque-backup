# LRU
## LRU 简介

least Recently Used 最近最少使用，用于操作系统内存管理，在前端开发中常用于优化页面性能和资源利用率。  
直接翻译就是“最不经常使用的数据，重要性是最低的，应该优先删除”  
以下是在前端中使用 LRU 算法的几个应用场景：

- **前端路由**：在单页应用（SPA）中，通过将路由信息保存在缓存中，可以避免每次访问页面时都需要重新加载数据，从而提高页面响应速度和用户体验。
- **图片懒加载**：对于大型图片库，可以使用 LRU 算法对已加载的图片进行缓存，当一个新图片需要被加载时，可以先检查该图片是否已经在缓存中存在，如果存在则直接从缓存中获取，否则从服务器加载。
- **数据缓存**：对于需要频繁读取的数据或者需要复杂计算才能得出结果的数据，可以使用 LRU 算法对其进行缓存，以减少重复计算的时间。
- **字体应用**：对于网站上使用的字体文件，可以使用 LRU 算法将最常用的字体文件存储在缓存中，从而加快页面渲染速度和节省网络流量。

总之，LRU 算法可用于提升前端应用的性能和用户体验，但需要根据具体的应用场景选择合适的算法并进行合理的配置。

## 如何实现

那么如何实现一个 LRU 算法呢？我们一起看看 leetcode 146 这道题目  
[https://leetcode.cn/problems/lru-cache/](https://leetcode.cn/problems/lru-cache/)  
设计一个 LRU 类，实现 get put 方法  
题目简单描述：  
请你设计并实现一个满足 [LRU (最近最少使用) 缓存](https://baike.baidu.com/item/LRU) 约束的数据结构。  
实现 LRUCache 类：

- LRUCache(int capacity) 以 **正整数** 作为容量 capacity 初始化 LRU 缓存
- int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
- void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 **逐出** 最久未使用的关键字。

## 实现思路

我们使用一个 map 来缓存数据。  
当获取数据 key 时，优先判断是否存在于 map，如果在我们先拿到这个值存为 temp，然后从 map 中删除，重新 set 进 map 中  
当插入数据时，优先判断是否存在于 map，如果不存在，直接 set，如果存在，删除后哦吗，重新 set  
这样我们保证最近使用的都在 map 的最下层，当内存超出时，直接删除 map 顶层元素即可  
this.map.delete(this.map.keys().next().value)

```javascript
var LRUCache = function (capacity) {
  this.capacity = capacity;
  this.map = new Map();
};

LRUCache.prototype.get = function (key) {
  if (this.map.has(key)) {
    let temp = this.map.get(key);
    this.map.delete(key);
    this.map.set(key, temp);
    return temp;
  } else {
    return -1;
  }
};
LRUCache.prototype.put = function (key, value) {
  if (this.map.has(key)) {
    this.map.delete(key);
  }
  this.map.set(key, value);
  if (this.map.size > this.capacity) {
    this.map.delete(this.map.keys().next().value);
  }
};
```

## 缺陷

虽然该实现使用了 Map 对象，但是在最坏情况下，如果哈希函数分布不均匀，可能会导致哈希冲突，使得某些操作的时间复杂度变为 O(n)。因此，在实际应用中，如果需要高效地处理大规模数据，建议使用双向链表或其他更高效的数据结构。  
假设有一个哈希表，大小为 5，使用的哈希函数为 key % 5。现在插入以下 6 个键值对：

```javascript
{key: 1, value: 'a'}
{key: 2, value: 'b'}
{key: 3, value: 'c'}
{key: 4, value: 'd'}
{key: 6, value: 'e'}
{key: 11, value: 'f'}
```

根据给定的哈希函数 key % 5，可以将每个键映射到哈希表中的一个桶。具体来说，将键除以 5 并取余数，以得到它应该插入的桶的索引。  
使用这个哈希函数，将上述六个键值对插入哈希表中，得到以下结果：  
在索引 1 的桶中插入 {key: 1, value: 'a'}  
在索引 2 的桶中插入 {key: 2, value: 'b'}  
'在索引 3 的桶中插入 {key: 3, value: 'c'}  
在索引 4 的桶中插入 {key: 4, value: 'd'}  
在索引 1 的桶中插入 {key: 6, value: 'e'}  
在索引 1 的桶中插入 {key: 11, value: 'f'}  
注意，在将键为 6 和 11 的键值对插入哈希表时，它们都被映射到索引 1 的桶中。这是因为它们分别与 1 余数相同。  
当出现哈希冲突时，即多个键被映射到同一桶时  
这种情况下，在操作时需要遍历整个桶来查找指定的键值对，因此操作的时间复杂度变为 O(n)。

## 双向链表+哈希表

那么如何达到 O(1)的时间复杂度呢？  
那肯定选用 map 查询。修改，删除也要尽量 O(1) 完成。搜寻常见的数据结构，链表，栈，队列，树，图。树和图排除，栈和队列无法任意查询中间的元素，也排除。所以选用链表来实现。但是如果选用单链表，删除这个结点，需要 O(n) 遍历一遍找到前驱结点。所以选用双向链表，在删除的时候也能 O(1) 完成。  
核心就是：最近最多使用的节点永远在链表结尾，最近最少使用的节点在链表开头。

### 双向链表

双向链表的结构  
value: 存储的值  
prev: 指向前一个元素的指针  
next: 指向下一个元素的指针  
Head 和 Tail 是**虚拟的头部和尾部节点**，这是为了方便找到链表的首末设定的

```javascript
       ┌──────>┐    ┌───────>┐   ┌───────>┐
  head • (x|"three"|•)  (•|"two"|•)  (•|"one"|x) • tail
               └<────────┘   └<───────┘   └<─────┘
```

### 实现思路

- 使用一个 Map 对象来存储键值对
- 使用一个双向链表维护键值对的顺序
- 抽离出一个 addToTaill 方法（将节点插入末尾）抽离出一个 remove 方法（删除节点）
- 当执行 put 操作时，判断节点是否在 map 中
  - 如果存在，获取当前节点值，在双向链表中 remove 删除改节点，重新调用 addToTail 添加到末尾
  - 如果不存在，建立一个双向链表节点，调用 addToTail 添加到末尾，同时添加进 map
  - 如果超过容量 this.size > this.cap，删除当前 head 节点，从 map 中删除当前 key
- 当执行 get 操作时，判断节点是否在 map 中
  - 如果不存在，返回-1
  - 如果存在，获取当前 key,value，重新执行 put 操作

```javascript
class ListNode {
  constructor(key, value) {
    this.key = key;
    this.val = value;
    this.prev = null;
    this.next = null;
  }
}

class LRUCache {
  constructor(capacity = 10) {
    this.capacity = capacity;
    // 实际保存的键值对数量
    this.size = 0;
    this.map = {};
    //代表最旧的节点
    this.head = null;
    //代表最新的节点
    this.tail = null;
  }

  get(key) {
    const node = this.map.get(key);
    if (!node) return -1;
    let node = this.map[key];
    this.put(node.key, node.val);
    return node.value;
  }

  put(key, value) {
    if (this.map[key]) {
      let node = this.map[key];
      node.val = value;
      this.remove(node);
      this.addTotail(node);
    } else {
      let node = new ListNode(key, value);
      this.addTotail(node);
      this.map[key] = node;
      this.size++;
    }
    if (this.size > this.cap) {
      let key = this.head.key;
      this.remove(this.head);
      delete this.map[key];
      this.size--;
    }
  }
  addToTail(node) {
    if (this.tail) {
      this.tail.next = node;
      node.prev = this.tail;
      this.tail = node;
    } else {
      this.tail = node;
      this.head = node;
    }
  }
  remove(node) {
    if (node.prev) {
      node.prev.next = node.next;
    } else {
      this.head = this.head.next;
    }
    if (node.next) {
      node.next.prev = node.prev;
    } else {
      this.tail = this.tail.prev;
    }
    node.prev = node.next = null;
  }
}
```

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/ualsdegx4oobworc
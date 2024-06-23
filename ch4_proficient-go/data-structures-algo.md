### Implementing a Binary Search Tree (BST)

A Binary Search Tree is a data structure where each node has at most two children, with the left child being smaller and the right child being larger than the node itself.

1. **Define the Node Structure:**
   ```go
   type TreeNode struct {
       Key   int
       Left  *TreeNode
       Right *TreeNode
   }
   ```

2. **Implement Insertion:**
   ```go
   func insert(root *TreeNode, key int) *TreeNode {
       if root == nil {
           return &TreeNode{Key: key}
       }
       if key < root.Key {
           root.Left = insert(root.Left, key)
       } else if key > root.Key {
           root.Right = insert(root.Right, key)
       }
       return root
   }
   ```

3. **Implement Traversals (In-order, Pre-order, Post-order):**
   ```go
   func inorderTraversal(root *TreeNode) {
       if root != nil {
           inorderTraversal(root.Left)
           fmt.Println(root.Key)
           inorderTraversal(root.Right)
       }
   }

   func preorderTraversal(root *TreeNode) {
       if root != nil {
           fmt.Println(root.Key)
           preorderTraversal(root.Left)
           preorderTraversal(root.Right)
       }
   }

   func postorderTraversal(root *TreeNode) {
       if root != nil {
           postorderTraversal(root.Left)
           postorderTraversal(root.Right)
           fmt.Println(root.Key)
       }
   }
   ```

4. **Usage Example:**
   ```go
   func main() {
       var root *TreeNode
       keys := []int{50, 30, 20, 40, 70, 60, 80}

       for _, key := range keys {
           root = insert(root, key)
       }

       fmt.Println("In-order traversal:")
       inorderTraversal(root)

       fmt.Println("\nPre-order traversal:")
       preorderTraversal(root)

       fmt.Println("\nPost-order traversal:")
       postorderTraversal(root)
   }
   ```

### Creating a Simple LRU Cache

LRU (Least Recently Used) Cache is a cache eviction policy where the least recently used items are removed when the cache reaches its capacity.

1. **Define the LRUCache Structure:**
   ```go
   type LRUCache struct {
       capacity int
       cache    map[int]*list.Element
       lruList  *list.List
   }
   ```

2. **Initialize the LRUCache:**
   ```go
   func NewLRUCache(capacity int) *LRUCache {
       return &LRUCache{
           capacity: capacity,
           cache:    make(map[int]*list.Element),
           lruList:  list.New(),
       }
   }
   ```

3. **Implement Get and Put Operations:**
   ```go
   func (lru *LRUCache) Get(key int) int {
       if elem, ok := lru.cache[key]; ok {
           lru.lruList.MoveToFront(elem)
           return elem.Value.(cacheEntry).value
       }
       return -1
   }

   func (lru *LRUCache) Put(key, value int) {
       if elem, ok := lru.cache[key]; ok {
           // Update existing entry
           elem.Value = cacheEntry{key, value}
           lru.lruList.MoveToFront(elem)
       } else {
           // Add new entry
           if lru.lruList.Len() >= lru.capacity {
               // Evict least recently used item
               delete(lru.cache, lru.lruList.Back().Value.(cacheEntry).key)
               lru.lruList.Remove(lru.lruList.Back())
           }
           // Add new item
           newElem := lru.lruList.PushFront(cacheEntry{key, value})
           lru.cache[key] = newElem
       }
   }
   ```

4. **Define Helper Structures:**
   ```go
   type cacheEntry struct {
       key   int
       value int
   }
   ```

5. **Usage Example:**
   ```go
   func main() {
       cache := NewLRUCache(2)

       cache.Put(1, 1)
       cache.Put(2, 2)
       fmt.Println(cache.Get(1)) // Output: 1

       cache.Put(3, 3)            // evicts key 2
       fmt.Println(cache.Get(2)) // Output: -1 (not found)

       cache.Put(4, 4)            // evicts key 1
       fmt.Println(cache.Get(1)) // Output: -1 (not found)
       fmt.Println(cache.Get(3)) // Output: 3
       fmt.Println(cache.Get(4)) // Output: 4
   }
   ```

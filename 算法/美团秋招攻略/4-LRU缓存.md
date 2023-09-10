# 146-LRU缓存

[146-LRU缓存](https://leetcode.cn/problems/lru-cache/?envType=study-plan-v2&envId=meituan-2023-fall-sprint)，难度中等

![image-20230828174114239](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308281741298.png)

```java
class LRUCache {
	private int cap;
    private LinkedHashMap<Integer, Integer> cache = new LinkedHashMap<>();
    public LRUCache(int capacity) {
		this.cap = capacity;
    }
    
    public int get(int key) {
		if(!cache.containsKey(key))
            return -1;
        makeRecently(key);
        return cache.get(key);
    }
    
    public void put(int key, int value) {
		if(cache.containsKey(key)){
            cache.put(key, value);
            makeRecently(key);
            return;
        }
        if(cache.size() >= this.cap){
            int oldestKey = cache.keySet().iterator().next();
            cache.remove(oldestKey);
        }
        cache.put(key, value);
    }
    
    public void makeRecently(int key){
        int val = cache.get(key);
        cache.remove(key);
        cache.put(key, val);
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```


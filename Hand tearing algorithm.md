# 生产者消费者

多线程同步问题的经典案例。该问题描述了**共享固定大小缓冲区**的**两个线程**--即所谓的“生产者”和“消费者”。

生产者的主要作用是生成一定量的数据放到缓冲区中，然后重复此过程。与此同时，消费者也在缓冲区消耗这些数据。该问题的关键就是**要保证生产者不会在缓冲区满时加入数据，消费者也不会在缓冲区中空时消耗数据。**

**解决思路**可以简单概括为：

- 生产者持续生产，直到缓冲区满，**满时阻塞**；缓冲区不满后，继续生产；
- 消费者持续消费，直到缓冲区空，**空时阻塞**；缓冲区不空后，继续消费；
- 生产者和消费者都可以有多个；


```
1、wait()/notify()方式；

2、await()/signal()方式；

3、BlockingQueue阻塞队列方式；
```

1. wait / notify 方式实现


2. BlockingQueue 阻塞队列实现方式




# 单例模式

代码：
```java
public class Singleton {
    
    private volatile static Singleton instance;

    private Singleton() {
    }

    public  static Singleton getInstance() {
       //先判断对象是否已经实例过，没有实例化过才进入加锁代码
        if (instance == null) {
            //类对象加锁
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

为什么要使用`volatile`关键字修饰`instance`:


因为代码`instance = new Singleton();`的执行过程有三步：

1. 为 `uniqueInstance` 分配内存空间
2. 初始化 `uniqueInstance`
3. 将 `uniqueInstance` 指向分配的内存地址

由于指令重排可能导致代码的运行顺序并不是1->2->3，而可能是1->3->2。例如，线程 T1 执行了 1 和 3，此时 T2 调用 `getUniqueInstance`() 后发现 `uniqueInstance` 不为空，因此返回 `uniqueInstance`，但此时 `uniqueInstance` 还未被初始化。

使用 `volatile` 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。

反射可以破坏这种单例模式，只需要将构造器函数的`private`权限破坏掉就可以无限创造。
```java
Singleton instance = Singleton.getInstance();
//获取构造函数
Constructor<Singleton> declaredConstructor = Singleton.class.getDeclaredConstructor(null);
//破坏私有权限
declaredConstructor.setAccessible(true);
//创建实例
Singleton instance2 = declaredConstructor.newInstance();

System.out.println(instance);
System.out.println(instance2);
```


# LRU

最近最久未使用淘汰法缓存。当缓存满了的时候，需要删除最久最久未使用的数据，以插入新的数据。

> 注意如何遍历LinkedHashMap的方法，有`Lambda`法、`ForEach EntrySet`法。
> 
> [HashMap 的 7 种遍历方式](https://juejin.cn/post/6844904144331866119#heading-3)


```java
package leetcode.editor.cn.DataStruct.LRU;

import java.util.LinkedHashMap;

public class LRUCache {

    int capacity;
    LinkedHashMap<Integer,Integer> cache = new LinkedHashMap<>();

    //构造函数
    public LRUCache(int capacity){
        this.capacity = capacity;
    }

    //
    public int get(int key){
        if (!cache.containsValue(key)){
            return -1;
        }
        //把key变为最近使用
        makeRecently(key);
        return cache.get(key);
    }

    public void put(int key,int value){
        //如果key存在，更新key并更新为最近使用
        if (cache.containsValue(key)){
            cache.put(key,value);
            makeRecently(key);
            return;
        }

        //若key不存在 先判断cache的容量
        if (cache.size() >= this.capacity) {
            //如果cache容量满了
            //需要删除最近最久未使用的node
            int oldestKey = cache.keySet().iterator().next();
            cache.remove(oldestKey);
        }
        //把新的key放到链表尾部
        cache.put(key,value);
    }
    //重要！！
    private void makeRecently(int key) {
        int val = cache.get(key);
        //删除key
        cache.remove(key);
        //重新插到队尾
        cache.put(key,val);
    }

    public static void main(String[] args) {
        LRUCache lruCache = new LRUCache(3);
        lruCache.put(1,1);
        lruCache.put(2,2);
        lruCache.put(3,3);
        System.out.println("key 1 ,val :"+lruCache.get(1));
        lruCache.put(4,4);
        //查看lruCache的存储信息
        System.out.println("--------------");
        lruCache.cache.forEach((key,val)->{
            System.out.print("key :"+key +",");
            System.out.println("val :" + val);
        });

        /*
        key 1 ,val :1
        --------------
        key :3,val :3
        key :1,val :1
        key :4,val :4
        //由此可见LinkedHashMap是尾插法
        */
    }
}

```

# 交替打印

# 线程池

# NIO(New Input/Output) 

# 死锁

# 排序算法

## 快排
```java

public void quicksort(int[] nums,int lo,int hi){
    if(lo>=hi)
        return;
    
    int p = partition(nums,lo,hi);
    
    quicksort(nums,lo,p-1);
    quicksort(nums,p+1,hi);
}

int partition(int[] nums,int lo,int hi){
    int pivot = nums[hi];
    
    int i = lo;
    
    for(int j=lo;j<hi;j++){
        if(num[j]<pivot){
            swap(nums,i,j);
            i++;
        }
    }
    
    swap(nums,i,hi);
    
    return i;
}

void swap(int[] nums,int i,int j){
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

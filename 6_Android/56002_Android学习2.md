# Java语言程序设计

## 简答题

### 1、简述Collection接口和Map接口的实现类，及其区别、应用



### 2、简述HashMap的特性以及底层实现原理



### 3、请写出Queue的实现类，其区别和优缺点



### 4、请写出一个快排

``` Java
public class Sorting
{
    public int[] QuickSorting(int[] temArray)
    {
        int left = 0;
        int right = **temArray.length - 1**;
        QuickSort(temArray,left,right);
        return temArray;
    }
    private void QuickSort(int[] temArray,int left,int right)
    {
        if(right <= left)
        {
            return;
        }
        else
        {
            int pivot = PartitionArray(temArray,left,right);
            QuickSort(**temArray**,left,pivot-1);
            QuickSort(**temArray**,pivot+1,right);
        }
    }
    private void PartitionArray(int[] temArray,int left,int right)
    {
        int leftPointer = left;
        int rightPointer = right - 1;
        int pivotValue = temArray[right];

        while(true)
        {
            while(temArray[leftPointer]**<**pivotValue)
            {
                leftPointer++;
            }
            while(temArray[rightPointer]**>**pivotValue && rightPointer > 0)
            {
                rightPointer--;
            }

            if(rightPointer<=leftPointer)
            {
                break;
            }
            else
            {
                int temp = temArray[leftPointer];
                temArray[leftPointer] = temArray[rightPointer];
                temArray[rightPointer] = temp;
            }
        }

        temArray[right] = temArray[leftPointer];
        temArray[leftPointer] = pivotValue;
        return leftPointer;
    }
}
```



### 5、实现一个最优单例模式。

```Java
public class Singleton
{
    public **static** class Inner
    {
        public static Singleton singleton = new Singleton();
    }

    private Singleton()
    {

    }

    public **static** Singleton GetInstance()
    {
        return Inner.singleton;
    }
}
```



### 6、实现一个线程池（ThreadPoolExecutor）。

``` java
public class ThreadExcutor
{
    private static BlockingQueue<Runnable> queue = null;
    private final HashSet<Worker> workers = new HashSet<Worker>();
    private final List<Thread> threadList = new ArrayList<Thread>();

    private volatile boolean RUNNING = true;
    private boolean shutdown = false;
    private int poolSize = 0;
    private int coreSize = 0;

    public ThreadExcutor(int poolSize)
    {

    }

    public void exec(Runnable runnable)
    {

    }

    public void addThread(Runnable runnable)
    {

    }

    public void shutdown()
    {

    }

    class Worker implements Runnable
    {
        public Worker(Runnable runnable)
        {
            queue.offer(runnable);
        }

        @Override
        public void run()
        {

        }

        public Runnable getTask() throws InterruptedException
        {

        }

        public void interruptIfIdel()
        {

        }
    }
}
```





## 笔试题

### 1、简述Binder的原理。



### 2、Touch事件的传递流程。



### 3、Activity启动模式都有哪些，及其区别。



### 4、写出掌握的Gradle性能优化（大概即可）。



### 5、SufaceView和TextureView的区别。



### 6、是否使用过IntentService，简述其原理和使用场景。



### 7、用RxJava1或者RxJava2实现“每隔1秒执行一次，一共执行5次”



### 8、用databinding实现点击事件



### 9、Android如何调用C、C++代码，并简述流程
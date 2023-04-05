1. 程序、进程、线程
2. 线程的创建
   1. 继承Thread类
   2. 实现Runnable接口
   3. 实现Callable接口
   4. 创建线程池
3. 线程的同步（解决线程不安全问题）
   1. 同步代码块
   2. 同步方法
   3. Lock锁（多个线程一个🔒）
4. 线程通信
5. 经典例子：生产者（producer）消费者（customer）问题

<a name="fMxPp"></a>
# 线程的创建
<a name="LqChm"></a>
## 继承Thread类

1. 要实现多线程的类继承于Thread类
2. 重写 run 方法
3. 在主进程里创建子类的实例对象
4. 每个实例对象都是一条独立的线程
5. 实例对象调用父类Thread类里的start方法
6. 该线程开始执行run方法里的操作
<a name="n9kkE"></a>
## 实现Runnable接口

1. 要实现多线程的类**A**实现Runnable接口
2. 重新run方法
3. 在主进程里实例化**一个**（根据实际需求）实现类**A**对象
4. 实例化多线程，该对象**a**可以作为Runnable target 放入 Thread类的构造器里实例化Thread对象
5. 此时由实现类**A实现的**所有多线程的共用原先的run方法
6. 由Thread实例化对象调用start方法。
<a name="xZtNa"></a>
## 实现Callable接口

1. 类A实现Callable接口
2. 类A重新call方法
3. 在主程序中实例化一个类A对象a
4. 把**对象a**作为参数放入FutureTask 构造器里，根据需要线程数量实例化多个FutureTask 类，得到多个实例对象**abc...**。
5. 把abc...分别放入Thread类的构造器中，实例化多线程Thread类的对象。
6. 若想获得call方法的返回值，
```java
public class CallTest {
    public static void main(String[] args) {
        //3.创建Callable接口实现类的对象
        Customer c = new Customer();
//4.将此Callable实现类的对象作为参数传递到FutureTask构造器中，创建FutureTask的对象
        FutureTask future = new FutureTask(c);
        FutureTask futureTask = new FutureTask(c);
//5.将FutureTask的对象作为参数传递到Thread类的构造器中，创建Thread对象，并调用start方法
        Thread t1 = new Thread(future);
        t1.setName("一号");

        t1.start();
        new Thread(futureTask).start();

         try {
            //6.获取Callable中的call方法的返回值
			//get方法的返回值为重写的call方法的返回值。
            Object sum = futureTask.get();
            System.out.println("总和为："+sum);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
//1.创建一个实现Callable的实现类
class Customer implements Callable {
//2.实现call方法，将此线程需要执行的操作声明在call方法中   
     @Override
    public Object call() throws Exception {
        return null;
    }
}
```
<a name="o4LUe"></a>
### 新增方式：Future接口

1. 可以对具体Runnable、Callable任务的执行结果进行取消、查询是否完成、获取结果等。
2. FutrueTask是Futrue接口的唯一的实现类
3. FutureTask 同时实现了Runnable, Future接口。它既可以作为 Runnable被线程执行，又可以作为Future得到Callable的返回值
<a name="LVnTe"></a>
### 新增方式：实现Callable接口
与使用Runnable相比， Callable功能更强大些：

- **相比run()方法，可以有返回值**
- **方法可以抛出异常**
- **支持泛型的返回值**
- **需要借助FutureTask类，比如获取返回结果**
<a name="OA0Lr"></a>
## 创建线程池
<a name="dpMYp"></a>
### 线程池的优势

1. 降低系统资源消耗，通过重用已存在的线程，降低线程创建和销毁造成的消耗；
2. 提高系统响应速度，无需等待新线程的创建便能立即执行；
3. 方便线程管控，线程若是无限制的创建，不仅会额外消耗大量系统资源，更是占用过多资源而阻塞系统或oom等状况，从而降低系统的稳定性。线程池能有效管控线程，统一分配、调优，提供资源使用率；
4. 更强大的功能，线程池提供了定时、定期以及可控线程数等功能的线程池。
<a name="kIc9w"></a>
### Java线程池相关API

1. **ExecutorService**：真正的线程池接口。
   1. **void execute(Runnable command)** ：执行任务/命令，没有返回值，一般用来执行 
   2. **Runnable <T> Future<T> submit(Callable<T> task)**：执行任务，有返回值，一般又来执行Callable 
   3. **void shutdown() **：关闭连接池
2. **Executors**：工具类、线程池的工厂类，用于创建并返回不同类型的线程池。
   1. ** Executors.newCachedThreadPool()**：创建一个可根据需要创建新线程的线程池 
   2.  **Executors.newFixedThreadPool(n)**：创建一个可重用固定线程数的线程池 
   3. **Executors.newSingleThreadExecutor()** ：创建一个只有一个线程的线程池 
   4. **Executors.newScheduledThreadPool(n)**：创建一个线程池，它可安排在给定延迟后运 行命令或者定期地执行。
3. 线程池相关设计参数
- _corePoolSize：核心池的大小  _
- _maximumPoolSize：最大线程数 _
- _keepAliveTime：线程没有任务时最多保持多长时间后会终止 _
- _ …_
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * Created by lily via on 2022/10/18 17:11
 */

public class theradPoolTest {
    public static void main(String[] args) {
//        1. 获取一个固定大小的线程池
        ExecutorService service = Executors.newFixedThreadPool(10);
//        2. 实例化要多线程处理的对象
        NumCounter n1 = new NumCounter();
//    若实例化了两个对象则两个对象分别拥有一套独立的run方法--->NumCounter n2 = new NumCounter();
//        3. 利用线程池里已造好的线程执行run方法
        service.execute(n1);
        service.execute(n1);
//       4. 关闭连接池
        service.shutdown();
    }
}

class NumCounter implements Runnable{
//    共享数据num
    private int num = 100;

    @Override
    public void run() {
        while (true) {
// 每一轮该线程从if判断语句中出来都要再判断一次--->解决线程不安全问题
            synchronized (this) {
                if (num > 0) {
                    if (num % 7 == 0) {
                        System.out.println(Thread.currentThread().getName() + "...num=" + num);
                    }num--;
                } else break;
            }
        }
    }
}
```
<a name="EQ3N9"></a>
# 线程的同步
<a name="H5PLf"></a>
## 同步代码块

1. 使用方式：
   1. synchonized(同步监视器/锁){

需要被同步的代码/多个线程共享需要处理的数据<br />}

2. 一旦被同步监视器所包裹，{}内的代码会只允许单线程处理，即未进入同步监视器内的线程视为被阻塞，直到同步监视器内的线程执行完毕，外部的线程才会有机会被cpu分配资源进入运行状态。
3. synchonized(obj) 使用的锁对象可以为任一所有需要进行同步操作线程所共有的对象
   1. 自己实例化一个对象 例如 Object obj = new Object();
   2. 在 implement Runnable 接口时由于只实例化了一个共享数据类对象，所以可以用本对象来充当同步监视器----> this关键字即代表本类的实例对象
   3. 在 extends Thread类中由于实例化了多个数据类对象，所以可以用数据类 window1.class来当做同步进程对象。类本身也是一个对象，创建唯一的一个类对象 Class clazz = new Windows();  新建了一个windows类的类对象。
4. 使用同步监视器的优缺点：
   1. 优点：保证了处理共享数据时的线程安全
   2. 缺点：在进入锁内时视为单线程运行，效率低
5. 包裹需要被同步的代码时的注意点：
   1. 不能把共享数据和逻辑代码囊括多了或者是囊括少了，会造成无效同步或者是运行逻辑错误的问题。
<a name="Qp62p"></a>
## 同步方法

1. 其中的同步监视器也和同步代码块的要求相同
2. 利用synchonized关键字修饰该方法，该方法内的代码块都会变成同步代码块，握住同步监视器的线程，此时该同步方法时只允许一个线程执行，执行完毕后释放同步监视器。
<a name="FSX63"></a>
# 

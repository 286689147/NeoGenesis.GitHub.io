## 创建线程的方式一：继承Thread类。

#### 步骤：

1，定义一个类继承Thread类。
2，覆盖Thread类中的run方法。
3，直接创建Thread的子类对象创建线程。
4，调用start方法开启线程并调用线程的任务run方法执行。

```java
package day12;

class Demo extends Thread{
    private String name;
    Demo(String name){
        this.name = name;
    }

    @Override
    public void run() {
        for (int x = 0; x < 10; x++) {
            //for (int y = -999999999; y < 999999999; y++) {}
            System.out.println(name+"x="+x+".....name"+getName());
        }
    }

}

public class ThreadDemo2 {
    public static void main(String[] args) {

//        Thread t1 = new Thread();

        Demo d1 = new Demo("旺财");
        Demo d2 = new Demo("xiangqiang");
        d1.start();// 开启线程，调用run方法。

        d2.start();
        System.out.println("over");
    }
}
/*
 调用run和调用start有什么区别？
调用start方法开启线程会调用线程的任务run方法 执行业务
run() 方法当作普通方法的方式调用，程序还是要顺序执行
  */
```

- [x] **创建线程的目的是为了开启一条执行路径，去运行指定的代码和其他代码实现同时运行。**
- [x] **而运行的指定代码就是这个执行路径的任务。**
- [x] **jvm创建的主线程的任务都是定义在了主函数中。**

- [x] **而自定义的线程它的任务在哪儿呢？**
  **Thread类用于描述线程，线程是需要任务的，所以Thread类也有对任务的描述。**
  **这个任务通过Thread类中的run方法类体现，也就是说，run方法就是封装自定义线程运行任务的函数。**
- [x] **run方法中就是定义线程要运行的任务代码。**
- [x] **开启线程是为了运行指定代码，所以只有继承Thread类，并重写run方法。**
  **将运行的代码定义在run方法中即可。**


# Java多线程之实现Callable接口（创建线程的方法之一）



**实现Callable接口**
（有泛型，不加编译器也不会报错）
与使用Runnable相比， Callable功能更强大：
1)相比run()方法没有返回值，需要重写call方法，call方法可以有返回值，分线程做完之后还可以返回一个结果给另外的线程用
2)方法可以抛出异常 ，抛出的异常可以给外面接受，接收之后就知道线程出什么问题。run方法只能使用try，catch的方式解决异常
3)支持泛型的返回值
4) 想使用需要借助FutureTask类，比如获取返回结果

**Future接口**
1)可以对具体Runnable、Callable任务的执行结果进行取消、查询是否完成、获取结果等
2)FutrueTask是Futrue接口的唯一实现类
3)FutureTask同时实现了Runnable, Future接口。它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值

**步骤：**

- 1.创建实现Callable接口的实现类
- 2.实现call方法，将此线程需要做的操作声明在call当中，如果不想返回东西，就return null
- 3.创建Callable实现类的对象
- 4.创建FutureTask对象，将创建好的Callable实现类对象作为参数传到FutureTask构造器当中
- 5.将创建好的FutureTask对象作为参数传给Thread类的构造器，创建Thread类的对象，调用start方法
- 6.（此步骤可省略）如果对Callable中call的返回值感兴趣，在没有使用泛型的情况下可以用Object对象变量接受FutureTask的对象.get()的结果

**说明：** 有的时候，一个线程做完以后需要给另外一个线程提供数据，另一个线程拿到数据之后才继续执行，这就需要get方法， get()：返回值即为FutureTask构造器参数Callable实现类重写的call的返回值

**如何理解实现Callable接口创建多线程的方式比实现Runnable接口创建多线程的方式强大？**

- 1)call()有返回值，分线程执行完后可以给另一个线程返回结果
- 2)call()可以抛出异常，在调用get方法处就可以捕获异常，根据捕获的异常，可以拿到感兴趣的信息，知道出了什么问题
- 3)Callable支持泛型，如果用了泛型（泛型不能用基本类型），比如这么干implements Callable< Integer>,那么call的返回值类型就必须写成Integer
  但不能这么写：Integer sum=f.get();此时仍认为是Object类型，因为FutureTask也有泛型，这么干才可以：
  FutureTask< Integer> f = new FutureTask<>(m);
  Integer sum=f.get();

```
package test0;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class ThreadDemo {
    public static void main(String[] args) {
        myThread m = new myThread();
        //不能直接调用start，它也没有start方法，要想使用要借助FutureTask类（有泛型）（涉及到Future接口）
        FutureTask f = new FutureTask(m);//注意：通过Alt+ctrl+/可以查看它的构造器信息，其中有一个构造器需要传一个Callable对象

        //要想启动线程必须new Thread，前面两种多线程创建方式的实质都是new Thread，因为要去调用start方法
        //利用FutureTask这个类也实现了Runnable接口，所以也可以当做参数传给Thread的构造器

        Thread t=new Thread(f);
        t.start();//启动线程，也可以写成new Thread(f).start();然后就会执行call方法
        
        try {//如果对于call的返回值不感兴趣，就不用调用get（），get的目的只是获取call方法的返回值，和启动线程无关
            Object sum=f.get();//call方法的返回值就是get的返回值
            System.out.println("总和为："+sum);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }//try，catch都是在主线程中做的
    }
}
class myThread implements Callable{

    //call就相当于run一样
    @Override
    //遍历100以内的偶数，并返回和
    public Object call() throws Exception {
        int sum=0;
        for(int i=1;i<=100;i++){
            System.out.println(i);
            sum+=i;
        }
        return sum;//Object是类层面的，int是基本数据类型，不存在继承关系，这里会自动装箱Integer，把Integer赋给了Object类型，涉及到多态
    }
}
```
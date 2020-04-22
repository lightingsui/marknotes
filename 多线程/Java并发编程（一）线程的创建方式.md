# Java并发编程（一）线程的创建方式

现成的创建方式共有三种，三种各有各的优点，各有各的特色。在项目中可根据自己的需求灵活的进行选择。

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-03-10_20-07-39.png)

## 实现方式一

实现方式一采用的是实现Runable接口来执行多线程，但是有一个弊端就是执行的run方法是没有返回值的，也不可以抛出异常。

```java
public class ThreadOne implements Runnable {

    private int a = 10;

    @Override
    public void run() {
        System.out.println("线程" + Thread.currentThread().getName() + a);
    }
}

class Test {
    public static void main(String[] args) throws Exception {
        Thread thread = new Thread(new ThreadOne());
        thread.start();
    }
}
```

创建Thread类，并将实现了Runable接口的对象传入到Thread中。调用Thread中的start方法即可执行线程。

## 实现方法二

实现方法二采用的继承Thread类，因为java是一种单继承语言，此类继承了Thread类，那么就无法再继承其它的类的。并且，此种方法也是无法获取run方法中的返回值以及run方法无法抛出异常。

```java
public class ThreadTwo extends Thread {

    private int a = 10;

    @Override
    public void run() {
        System.out.println("线程" + Thread.currentThread().getName() + a);
    }
}

class TestTwo {
    public static void main(String[] args) throws Exception {
        ThreadTwo threadTwo = new ThreadTwo();
        threadTwo.start();
    }
}
```

## 实现方法三

实现方法三弥补了一下前两种方法的缺点，即可以获取线程call方法的返回值，call方法也可以抛出异常，但是代码要比前两种复杂一些，通过实现Callable接口，然后将实现Callable接口的类通过构造参数传入到FutureTask中，再将Future通过构造参数传入到Thread中，其实FutureTask起到的是一个转接的作用。

```java
public class ThreadThree implements Callable {

    private int a = 10;
    
    @Override
    public Object call() throws Exception {
        synchronized (this){
            return ++a;
        }
    }
}

class TestThree {
    public static void main(String[] args) throws Exception{
        ThreadThree threadThree = new ThreadThree();

        for (int i = 0; i < 7; i++) {
            FutureTask<ThreadThree> threadThreeFutureTask = new FutureTask<ThreadThree>(threadThree);
            Thread thread = new Thread(threadThreeFutureTask);

            thread.start();
            System.out.println("线程返回值为：" + threadThreeFutureTask.get());

        }

        System.out.println("线程执行完毕");

    }
}
```

## 总结

1. 实现方式一与实现方式二相对来说较实现方式三简单，但是缺点就是无法run方法是没有返回值的，run方法也不能抛出异常，但是实现方式三可以获取call方法获取的返回值，也可抛出异常。
2. 实现方式一与实现方式三采用的是实现接口的方法，但是实现方式二采用的是继承Thread类，这就会受到java单继承的拘束
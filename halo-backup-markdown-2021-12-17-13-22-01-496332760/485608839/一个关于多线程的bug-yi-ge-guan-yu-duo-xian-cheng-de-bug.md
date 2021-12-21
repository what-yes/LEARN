---
title: 一个关于多线程的bug
date: 2021-12-10 21:24:52.364
updated: 2021-12-10 21:24:52.364
url: https://what-yes.top/archives/yi-ge-guan-yu-duo-xian-cheng-de-bug
categories: Bug
tags: Bug | 多线程 
---

一个关于多线程的bug

下午本来想写一个三线程轮流加1到100的代码，死活写不出来，一直报：

`java.lang.IllegalMonitorStateException`

代码如下：

```java
class Task implements Runnable{
    private static Integer num = 0;
    private static int flag = 0;
    private int ThreadID;

    Task(int id){
        this.ThreadID = id;
    }

    @Override
    public void run(){
        while(true){
            if(num >= 100)
                break;
            synchronized(num){
                while(flag%3 != ThreadID){
                    try {
                        num.wait();
                    } catch (Exception e) {
                        // TODO Auto-generated catch block
                        // e.printStackTrace();
                    }
                }
                if(num < 100){
                    System.out.println(Thread.currentThread().getName() + "------------->"+(++num));
                }
                flag++;
                num.notifyAll();
                    
                
            }
        }
        
    }
}

public class syn2 {
    public static void main(String[] args) {
        Thread thread0 = new Thread(new Task(0),"AA");
        Thread thread1 = new Thread(new Task(1),"BB");
        Thread thread2 = new Thread(new Task(2),"CC");

        thread0.start();
        thread1.start();
        thread2.start();
    }
}
```

后来索性去网上找了一份正确代码：

```java
class Task implements Runnable{
    private static Object lock = new Object(); 
    private static Integer num = new Integer(0);
    private static int flag = 0;
    private int ThreadID;

    Task(int id){
        this.ThreadID = id;
    }

    @Override
    public void run(){
        while(true){
            if(num >= 100)
                break;
            synchronized(lock){
                while(flag%3 != ThreadID){
                    try {
                        lock.wait();
                    } catch (Exception e) {
                        // TODO Auto-generated catch block
                        // e.printStackTrace();
                    }
                }
                if(num < 100){
                    System.out.println(Thread.currentThread().getName() + "------------->"+(++num));
                }
                flag++;
                lock.notifyAll();
                    
                
            }
        }
        
    }
}
```

发现它就是加了一个`private static Object lock = new Object();`  然后把`synchronized`中的参数以及调用`wait,notify`方法的对象改成了`lock`，就成功了。但是我之前用的`num`跟`lock`不一样吗？不都是`static`的吗？怎么就有问题呢？真是气死我了


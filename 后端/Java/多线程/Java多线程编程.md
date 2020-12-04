# Java多线程编程

## 线程的启动

### 1）继承Thread类

重写Thread类的run()方法，然后调用实例的start()方法启动线程。

代码实现:

```java
public class printTest02 {

    public static void main(String[] args) {
        new Thread(new Printer("Good!")).start();

        new Thread(new Printer("Nice!")).start();
    }
}

class Printer implements Runnable {
    private final String msg;

    public Printer(String msg) {
        this.msg = msg;
    }

    public void run() {
        for (int i = 0; i < 1000; i ++) {
            System.out.print(msg);
        }
    }
}
```

### 2)实现Runnable接口

实现Runnable接口的run()方法，通过Thread的构造器将实现Runnable接口的类实放入，再通过Thread类的start()方法开启线程:

代码实现

```java
public class printTest02 {

    public static void main(String[] args) {
        new Thread(new Printer("Good!")).start();

        new Thread(new Printer("Nice!")).start();
    }
}

class Printer implements Runnable {
    private final String msg;

    public Printer(String msg) {
        this.msg = msg;
    }

    public void run() {
        for (int i = 0; i < 1000; i ++) {
            System.out.print(msg);
        }
    }
}
```

### 补充说明：Thread类和RUnnable接口

​	Thread类本身也实现了Runnable接口，也有run方法，只不过Thread类的run方法是空实现。Thread类的run方法通常是被子类覆盖(override)。



## 线程的暂时停止

使当前线程停止

代码实现:

```java
public class SleepTest {
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            System.out.println("sleep");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```


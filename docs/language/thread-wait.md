#### wait

1. 作用

   当前线程调用wait()方法，释放锁资源，==只会释放当前共享变量的锁资源，线程持有的其他变量的锁不会释放（调用了谁的wait方法，释放谁的锁资源）==，进入等待。直到被唤醒，然后再等待获取锁资源，再继续执行

   > 当一个线程调用共享变量的`wait()`方法时，该调用线程会被阻塞挂起，直到发生下面几件事之一才返回：（1）其他线程调用了该共享对象的`notify()`或者`notifyAll()`方法；（2）其他线程调用了该线程的`interrupt()`方法，该线程抛出`IllegalMonitorStateException`异常。

2. 获取监视器锁

   - 执行synchronized同步代码块，使用该共享变量作为参数

     ```java
     synchronized (obj) {
     	// do something
     }
     ```
   - 调用该共享变量的方法，该方法使用了synchronized修饰
     ```java
     synchronized void add (int a) {
     	// do something
     }
     ```

3. 虚假唤醒

   虽然虚假唤醒的发生概率较低，但要避免发生，需要在循环中不断判断是否满足唤醒条件，否则就继续等待

   ```java
   synchronized (obj) {
       while (condition does not hold) {
           obj.wait();
       }
   }
   ```

   > 一个线程可以从挂起状态变为可运行状态（被唤醒），即使该线程没有被其他线程调用`notify() `、`notifyAll()`方法进行通知，或者被中断，或者等待超时，这就是所谓的虚假唤醒

4. 生产者消费者
   queue为共享变量，生产者线程在调用queue的`wait()`方法前，使用synchronized关键字拿到了该共享变量queue的监视器锁
   ```java
   // 生产者线程
   synchronized (queue) {
   	while (queue.size() == MAX_SIZE){
   		try {
   			queue.wait();
   		} catch (Exception e) {
   			e.printStackTrace();
   		}
   	}
   	queue.add(element);
   	queue.notifyAll();
   }
   // 消费者线程
   synchronized (queue) {
   	while (queue.size() == 0) {
   		try {
   			queue.wait();
   		} catch (Exception e) {
   			e.printStackTrace();
   		}
   	}
   	queue.take();
   	queue.notifyAll();
   }
   ```

#### notify
1. 作用

   将调用过该共享变量`wait()`方法的挂起线程唤醒，然后开始竞争锁资源

   > 一个共享变量上可能会有多个线程在等待，具体唤醒哪个等待的线程是随机的。此外，被唤醒的线程不能马上就从`wait()`方法返回并继续执行，它必须在获取了共向对象的监视器锁后才可以返回，也就是唤醒它的线程释放了共享对象的监视器锁后，被唤醒的线程与其他线程一起竞争监视器锁，竞争到监视器锁之后才开始继续执行。

2. notify()&notifyAll()
   - notify()：随机唤醒一个挂起线程
   - notifyAll()：唤醒所有挂起线程

3. 注意点
   > 在共享变量上调用`notifyAll()`方法==只会唤醒调用这个方法之前调用了wait系列函数而被放入共享变量等待集合里面的线程==。如果调用`notifyAll()`方法后一个线程调用了该共享变量的`wait()`方法，就是一个新的等待集合
   ```java
   	Thread threadA = new Thread(() -> {
            synchronized (resourceA) {
                System.out.println("threadA get resourceA lock");
                try {
                    System.out.println("threadA begin wait");
                    resourceA.wait();
                    System.out.println("threadA end wait");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
        Thread threadB = new Thread(() -> {
            synchronized (resourceA) {
                System.out.println("threadB get resourceA lock");
                try {
                    System.out.println("threadB begin wait");
                    resourceA.wait();
                    System.out.println("threadB end wait");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
    
        Thread threadC = new Thread(() -> {
            synchronized (resourceA) {
                System.out.println("threadC begin wait_notify");
                //resourceA.notify();
                resourceA.notifyAll();
            }
        });
    
        // 线程A执行，获取资源A的锁，挂起并释放锁
        threadA.start();
    
        // 线程B执行，获取资源A的锁，挂起并释放锁
        threadB.start();
    
        // 主线程休眠1秒，为了让线程B执行到挂起操作
        // 删除此代码，线程c可能在b之前执行
        //Thread.sleep(1000);
    
        // 线程C执行，获取资源A的锁，唤醒被挂起的线程A
        threadC.start();
    
        threadA.join();
        threadB.join();
        threadC.join();
    
        System.out.println("main over");
   ```
   **注释掉上述代码43行，则线程C可能在线程B之前执行，那么B挂起之后就无法唤醒**

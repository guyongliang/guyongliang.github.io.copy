转自：[https://blog.csdn.net/zhangliangzi/article/details/52485319](https://blog.csdn.net/zhangliangzi/article/details/52485319)

#### Java中断interrupt详解

interrupt简述

interrupt() 方法只是改变中断状态而已，它不会中断一个正在运行的线程。这一方法实际完成的是，给受阻塞的线程发出一个中断信号，这样受阻线程就得以退出阻塞的状态。 更确切的说，如果线程被Object.wait, Thread.join和Thread.sleep三种方法之一阻塞，此时调用该线程的interrupt()方法，那么该线程将抛出一个 InterruptedException中断异常（该线程必须事先预备好处理此异常），从而提早地终结被阻塞状态。如果线程没有被阻塞，这时调用 interrupt()将不起作用，直到执行到wait(),sleep(),join()时,才马上会抛出 InterruptedException。

线 程A在执行sleep,wait,join时,线程B调用线程A的interrupt方法,的确这一个时候A会有 InterruptedException 异常抛出来。但这其实是在sleep,wait,join这些方法内部会不断检查中断状态的值,而自己抛出的InterruptedException。 如果线程A正在执行一些指定的操作时如值,for,while,if,调用方法等,不会去检查中断状态,则线程A不会抛出 InterruptedException,而会一直执行着自己的操作。

注意:

当线程A执行到wait(),sleep(),join()时,抛出InterruptedException后，中断状态已经被系统复位了，线程A调用Thread.interrupted()返回的是false。

如果线程被调用了interrupt()，此时该线程并不在wait(),sleep(),join()时，下次执行wait(),sleep(),join()时，一样会抛出InterruptedException，当然抛出后该线程的中断状态也会被系统复位。

（总结一下：调用interrupt()方法，立刻改变的是中断状态，但如果不是在阻塞态，就不会抛出异常；如果在进入阻塞态后，中断状态为已中断，就会立刻抛出异常）

1. sleep() &interrupt()

线程A正在使用sleep()暂停着: Thread.sleep(100000)，如果要取消它的等待状态,可以在正在执行的线程里(比如这里是B)调用a.interrupt()［a是线程A对应到的Thread实例］，令线程A放弃睡眠操作。即，在线程B中执行a.interrupt()，处于阻塞中的线程a将放弃睡眠操作。

当在sleep中时线程被调用interrupt()时，就马上会放弃暂停的状态并抛出InterruptedException。抛出异常的,是A线程。

2. wait() &interrupt()

线程A调用了wait()进入了等待状态，也可以用interrupt()取消。不过这时候要注意锁定的问题。线程在进入等待区,会把锁定解除,当对等待中的线程调用interrupt()时，会先重新获取锁定，再抛出异常。在获取锁定之前，是无法抛出异常的。

3. join() &interrupt()

当线程以join()等待其他线程结束时，当它被调用interrupt()，它与sleep()时一样，会马上跳到catch块里.。

注意，调用的interrupt()方法，一定是调用被阻塞线程的interrupt方法。如在线程a中调用线程t.interrupt()。

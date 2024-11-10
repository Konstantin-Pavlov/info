Deadlock (взаимная блокировка) – это состояние в многопоточных программах, когда два или более потока блокируют друг друга, ожидая освобождения ресурсов, которые заблокированы другим потоком. В итоге ни один из потоков не может продолжить работу, и программа застывает.

### Причины возникновения deadlock
В Java deadlock часто возникает, если выполняются следующие условия:
1. Взаимное блокирование: поток блокирует ресурс, и при этом ожидает другой, уже заблокированный другим потоком ресурс.
2. Удержание и ожидание: поток удерживает один ресурс и ждет освобождения другого.
3. Отсутствие принудительного освобождения: ресурсы не освобождаются автоматически.
4. Круговая зависимость: несколько потоков создают цикл, в котором каждый поток ожидает ресурс, заблокированный другим потоком.

пример:
(В данном примере lock1 и lock2 являются объектами типа Object, которые используются как мониторы для синхронизации потоков (у каждого объекта в джаве есть свой монитор))
```java
public class DeadLockRunner {
    public static void main(String[] args) {
        final Object lock1 = new Object();
        final Object lock2 = new Object();

        ThreadOne threadOne = new ThreadOne(lock1, lock2);
        ThreadTwo threadTwo = new ThreadTwo(lock1, lock2);

        threadOne.start();
        threadTwo.start();

    }
}
```
```java
public class ThreadOne extends Thread {
    private final Object lock1;
    private final Object lock2;

    public ThreadOne(final Object lock1, final Object lock2) {
        this.lock1 = lock1;
        this.lock2 = lock2;
    }

    @Override
    public void run() {
        synchronized (lock2) {
            System.out.println("ThreadOne, lock2");
            synchronized (lock1) {
                System.out.println("ThreadOne, lock1");
            }
        }
    }
}
```
```java
public class ThreadTwo extends Thread {
    private final Object lock1;
    private final Object lock2;
    public ThreadTwo(final Object lock1, final Object lock2) {
        this.lock1 = lock1;
        this.lock2 = lock2;
    }
    @Override
    public void run() {
        synchronized (lock1) {
            System.out.println("ThreadTwo, lock1");
            synchronized (lock2) {
                System.out.println("ThreadTwo, lock2");
            }
        }
    }
}
```

Deadlock в этом коде возникает из-за того, что два потока (`ThreadOne` и `ThreadTwo`) блокируют ресурсы в противоположном порядке, создавая ситуацию взаимного ожидания.

### Последовательность событий:

1. **`ThreadOne`** сначала захватывает `lock2` (в строке `synchronized (lock2)`), а затем пытается получить `lock1`.
2. **`ThreadTwo`** сначала захватывает `lock1` (в строке `synchronized (lock1)`), а затем пытается получить `lock2`.

В результате:

- **`ThreadOne`** держит `lock2` и ожидает `lock1`.
- **`ThreadTwo`** держит `lock1` и ожидает `lock2`.

Таким образом, оба потока заблокированы, каждый ожидает освобождения ресурса, который удерживается другим потоком, что вызывает deadlock.

### Решение:

Чтобы избежать deadlock, можно изменить порядок захвата блокировок, например, всегда захватывать `lock1` перед `lock2` в обоих потоках.


Как можно отловить deadlock в более сложном коде:
В консоли с помощью команды jps найти pid (Process ID) процесса
С помощью команды jstack <process pid> посмотреть логи

в логах можно найти объекты которые создали deadlock и так же строки где это произошло

```
C:\Users\user>jps
18656 Launcher
3120
8608 Jps
11324
21068 DeadLockRunner

C:\Users\user>jstack 21068
2024-11-10 08:49:14
Full thread dump Java HotSpot(TM) 64-Bit Server VM (21+35-LTS-2513 mixed mode, sharing):

Threads class SMR info:
_java_thread_list=0x000002357ce4e450, length=14, elements={
0x000002357c959120, 0x000002357c95a1f0, 0x000002357c960190, 0x000002357c963110,
0x000002357c963b70, 0x000002357c964930, 0x000002357c96ad80, 0x000002357c9739e0,
0x000002357cb7f500, 0x000002357cd0f560, 0x000002357cd19240, 0x000002357cd1dd40,
0x000002357cd1e3b0, 0x000002355b183790
}

"Reference Handler" #9 [17776] daemon prio=10 os_prio=2 cpu=0.00ms elapsed=22.29s tid=0x000002357c959120 nid=17776 waiting on condition  [0x000000888fbff000]
   java.lang.Thread.State: RUNNABLE
        at java.lang.ref.Reference.waitForReferencePendingList(java.base@21/Native Method)
        at java.lang.ref.Reference.processPendingReferences(java.base@21/Reference.java:246)
        at java.lang.ref.Reference$ReferenceHandler.run(java.base@21/Reference.java:208)

"Finalizer" #10 [5172] daemon prio=8 os_prio=1 cpu=0.00ms elapsed=22.29s tid=0x000002357c95a1f0 nid=5172 in Object.wait()  [0x000000888fcff000]
   java.lang.Thread.State: WAITING (on object monitor)
        at java.lang.Object.wait0(java.base@21/Native Method)
        - waiting on <0x0000000711c0b6c0> (a java.lang.ref.NativeReferenceQueue$Lock)
        at java.lang.Object.wait(java.base@21/Object.java:366)
        at java.lang.Object.wait(java.base@21/Object.java:339)
        at java.lang.ref.NativeReferenceQueue.await(java.base@21/NativeReferenceQueue.java:48)
        at java.lang.ref.ReferenceQueue.remove0(java.base@21/ReferenceQueue.java:158)
        at java.lang.ref.NativeReferenceQueue.remove(java.base@21/NativeReferenceQueue.java:89)
        - locked <0x0000000711c0b6c0> (a java.lang.ref.NativeReferenceQueue$Lock)
        at java.lang.ref.Finalizer$FinalizerThread.run(java.base@21/Finalizer.java:173)

"Signal Dispatcher" #11 [10160] daemon prio=9 os_prio=2 cpu=0.00ms elapsed=22.29s tid=0x000002357c960190 nid=10160 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Attach Listener" #12 [13280] daemon prio=5 os_prio=2 cpu=31.25ms elapsed=22.29s tid=0x000002357c963110 nid=13280 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Service Thread" #13 [7588] daemon prio=9 os_prio=0 cpu=0.00ms elapsed=22.29s tid=0x000002357c963b70 nid=7588 runnable  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Monitor Deflation Thread" #14 [3924] daemon prio=9 os_prio=0 cpu=0.00ms elapsed=22.29s tid=0x000002357c964930 nid=3924 runnable  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread0" #15 [1808] daemon prio=9 os_prio=2 cpu=46.88ms elapsed=22.29s tid=0x000002357c96ad80 nid=1808 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE
   No compile task

"C1 CompilerThread0" #18 [4804] daemon prio=9 os_prio=2 cpu=78.12ms elapsed=22.29s tid=0x000002357c9739e0 nid=4804 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE
   No compile task

"Common-Cleaner" #19 [6832] daemon prio=8 os_prio=1 cpu=0.00ms elapsed=22.27s tid=0x000002357cb7f500 nid=6832 waiting on condition  [0x00000088903ff000]
   java.lang.Thread.State: TIMED_WAITING (parking)
        at jdk.internal.misc.Unsafe.park(java.base@21/Native Method)
        - parking to wait for  <0x0000000711dddcc0> (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
        at java.util.concurrent.locks.LockSupport.parkNanos(java.base@21/LockSupport.java:269)
        at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(java.base@21/AbstractQueuedSynchronizer.java:1847)
        at java.lang.ref.ReferenceQueue.await(java.base@21/ReferenceQueue.java:71)
        at java.lang.ref.ReferenceQueue.remove0(java.base@21/ReferenceQueue.java:143)
        at java.lang.ref.ReferenceQueue.remove(java.base@21/ReferenceQueue.java:218)
        at jdk.internal.ref.CleanerImpl.run(java.base@21/CleanerImpl.java:140)
        at java.lang.Thread.runWith(java.base@21/Thread.java:1596)
        at java.lang.Thread.run(java.base@21/Thread.java:1583)
        at jdk.internal.misc.InnocuousThread.run(java.base@21/InnocuousThread.java:186)

"Monitor Ctrl-Break" #20 [8032] daemon prio=5 os_prio=0 cpu=15.62ms elapsed=22.23s tid=0x000002357cd0f560 nid=8032 runnable  [0x00000088904fe000]
   java.lang.Thread.State: RUNNABLE
        at sun.nio.ch.SocketDispatcher.read0(java.base@21/Native Method)
        at sun.nio.ch.SocketDispatcher.read(java.base@21/SocketDispatcher.java:46)
        at sun.nio.ch.NioSocketImpl.tryRead(java.base@21/NioSocketImpl.java:256)
        at sun.nio.ch.NioSocketImpl.implRead(java.base@21/NioSocketImpl.java:307)
        at sun.nio.ch.NioSocketImpl.read(java.base@21/NioSocketImpl.java:346)
        at sun.nio.ch.NioSocketImpl$1.read(java.base@21/NioSocketImpl.java:796)
        at java.net.Socket$SocketInputStream.read(java.base@21/Socket.java:1099)
        at sun.nio.cs.StreamDecoder.readBytes(java.base@21/StreamDecoder.java:329)
        at sun.nio.cs.StreamDecoder.implRead(java.base@21/StreamDecoder.java:372)
        at sun.nio.cs.StreamDecoder.lockedRead(java.base@21/StreamDecoder.java:215)
        at sun.nio.cs.StreamDecoder.read(java.base@21/StreamDecoder.java:169)
        at java.io.InputStreamReader.read(java.base@21/InputStreamReader.java:188)
        at java.io.BufferedReader.fill(java.base@21/BufferedReader.java:160)
        at java.io.BufferedReader.implReadLine(java.base@21/BufferedReader.java:370)
        at java.io.BufferedReader.readLine(java.base@21/BufferedReader.java:347)
        at java.io.BufferedReader.readLine(java.base@21/BufferedReader.java:436)
        at com.intellij.rt.execution.application.AppMainV2$1.run(AppMainV2.java:54)

"Notification Thread" #21 [16484] daemon prio=9 os_prio=0 cpu=0.00ms elapsed=22.23s tid=0x000002357cd19240 nid=16484 runnable  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Thread-0" #22 [18592] prio=5 os_prio=0 cpu=0.00ms elapsed=22.23s tid=0x000002357cd1dd40 nid=18592 waiting for monitor entry  [0x00000088906ff000]
   java.lang.Thread.State: BLOCKED (on object monitor)
        at org.example.thread.deadlock.ThreadOne.run(ThreadOne.java:17)
        - waiting to lock <0x0000000711b41730> (a java.lang.Object)
        - locked <0x0000000711b41740> (a java.lang.Object)

"Thread-1" #23 [9940] prio=5 os_prio=0 cpu=0.00ms elapsed=22.23s tid=0x000002357cd1e3b0 nid=9940 waiting for monitor entry  [0x00000088907ff000]
   java.lang.Thread.State: BLOCKED (on object monitor)
        at org.example.thread.deadlock.ThreadTwo.run(ThreadTwo.java:15)
        - waiting to lock <0x0000000711b41740> (a java.lang.Object)
        - locked <0x0000000711b41730> (a java.lang.Object)

"DestroyJavaVM" #24 [15668] prio=5 os_prio=0 cpu=78.12ms elapsed=22.23s tid=0x000002355b183790 nid=15668 waiting on condition  [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"VM Thread" os_prio=2 cpu=0.00ms elapsed=22.30s tid=0x000002357c93c0c0 nid=12400 runnable

"GC Thread#0" os_prio=2 cpu=0.00ms elapsed=22.31s tid=0x000002355d4658c0 nid=17024 runnable

"G1 Main Marker" os_prio=2 cpu=0.00ms elapsed=22.31s tid=0x000002355b24ba50 nid=16820 runnable

"G1 Conc#0" os_prio=2 cpu=0.00ms elapsed=22.31s tid=0x000002355b24cb00 nid=9968 runnable

"G1 Refine#0" os_prio=2 cpu=0.00ms elapsed=22.31s tid=0x000002355d4ce630 nid=3784 runnable

"G1 Service" os_prio=2 cpu=0.00ms elapsed=22.31s tid=0x000002357c7f13c0 nid=17348 runnable

"VM Periodic Task Thread" os_prio=2 cpu=0.00ms elapsed=22.30s tid=0x000002357c9206d0 nid=8756 waiting on condition

JNI global refs: 15, weak refs: 0


Found one Java-level deadlock:
=============================
"Thread-0":
  waiting to lock monitor 0x000002355b1841f0 (object 0x0000000711b41730, a java.lang.Object),
  which is held by "Thread-1"

"Thread-1":
  waiting to lock monitor 0x000002355b184450 (object 0x0000000711b41740, a java.lang.Object),
  which is held by "Thread-0"

Java stack information for the threads listed above:
===================================================
"Thread-0":
        at org.example.thread.deadlock.ThreadOne.run(ThreadOne.java:17)
        - waiting to lock <0x0000000711b41730> (a java.lang.Object)
        - locked <0x0000000711b41740> (a java.lang.Object)
"Thread-1":
        at org.example.thread.deadlock.ThreadTwo.run(ThreadTwo.java:15)
        - waiting to lock <0x0000000711b41740> (a java.lang.Object)
        - locked <0x0000000711b41730> (a java.lang.Object)

Found 1 deadlock.
```


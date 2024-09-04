# SafePoint信息

## 目录

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [SafePoint信息](#safepoint信息)
  - [目录](#目录)
  - [概述](#概述)
    - [Safepoint的定义](#safepoint的定义)
    - [Safepoint的作用](#safepoint的作用)
    - [Safepoint的触发](#safepoint的触发)
    - [Safepoint的挑战](#safepoint的挑战)
    - [总结](#总结)
  - [到达Safepoint耗时较长的原因](#到达safepoint耗时较长的原因)
    - [1. **长时间执行的本地方法（Native Method）**](#1-长时间执行的本地方法native-method)
    - [2. **无Safepoint的长循环**](#2-无safepoint的长循环)
    - [3. **编译优化导致的长时间运行的代码块**](#3-编译优化导致的长时间运行的代码块)
    - [4. **线程处于阻塞或等待状态**](#4-线程处于阻塞或等待状态)
    - [5. **高CPU占用的线程**](#5-高cpu占用的线程)
    - [6. **过度的同步**](#6-过度的同步)
    - [7. **大量线程**](#7-大量线程)
    - [解决策略](#解决策略)
  - [具体例子](#具体例子)
    - [Thread.Sleep](#threadsleep)
      - [具体解释](#具体解释)
    - [Object.wait()](#objectwait)
      - [1. 使用 `java.util.concurrent` 包](#1-使用-javautilconcurrent-包)
        - [1.1 `BlockingQueue`](#11-blockingqueue)
        - [1.2 `Semaphore`](#12-semaphore)
        - [1.3 `CountDownLatch`](#13-countdownlatch)
      - [2. 使用 `java.util.concurrent.locks` 包](#2-使用-javautilconcurrentlocks-包)
        - [2.1 `ReentrantLock` 和 `Condition`](#21-reentrantlock-和-condition)
    - [RPC阻塞IO](#rpc阻塞io)
    - [InputStream的阻塞](#inputstream的阻塞)
      - [解决方案](#解决方案)
      - [1. 使用非阻塞 I/O](#1-使用非阻塞-io)
        - [1.1 `java.nio` 包](#11-javanio-包)
      - [2. 使用 `Socket` 类的超时机制](#2-使用-socket-类的超时机制)
        - [2.1 设置读取超时](#21-设置读取超时)
      - [3. 使用 `CompletableFuture` 进行超时控制](#3-使用-completablefuture-进行超时控制)

<!-- /code_chunk_output -->


## 概述

JVM（Java Virtual Machine）中的Safepoint是JVM执行过程中一个非常关键的概念，尤其是在执行某些全局操作时，比如垃圾回收（Garbage Collection）、栈回溯（Stack Tracing）或重编译（Deoptimization）等操作。

### Safepoint的定义
Safepoint是JVM中的一个特殊位置，在这个位置上，所有线程都可以安全地暂停。为了执行某些需要暂停所有线程的全局操作，JVM必须确保所有的Java线程都到达了一个“安全点”（Safepoint）。在这些点上，线程可以被安全地暂停并恢复，而不会影响程序的正确性。

### Safepoint的作用
Safepoint的主要作用是在执行全局操作（如GC）时，确保所有线程都处于一个可控的状态。具体来说，JVM需要保证所有线程都在Safepoint上暂停后，才能开始执行这些操作。这是因为，在某些操作中，JVM需要对所有线程的状态进行一致性处理（如更新堆内存中的对象引用），如果线程在不受控的情况下继续执行，可能会导致数据不一致或其他问题。

### Safepoint的触发
JVM中并不是所有的地方都可以作为Safepoint，只有在特定位置（如方法调用、循环回边、异常抛出等）才会被设置为Safepoint。当JVM需要所有线程进入Safepoint时，会发出一个全局的“Safepoint请求”，然后各个线程会在达到下一个Safepoint时暂停。

### Safepoint的挑战
1. **延迟问题**：线程在等待进入Safepoint时，会有一定的延迟，特别是当某个线程执行大量不涉及Safepoint的操作时，会导致其他线程需要等待它进入Safepoint，这会引发性能问题。
2. **频率与性能**：设置过多的Safepoint可能会导致性能开销，JVM需要在保证程序运行效率的同时，合理地选择Safepoint的位置。

### 总结
Safepoint是JVM中非常重要的机制，用于协调多线程环境下的全局操作。通过在程序中设置多个Safepoint，JVM可以确保在需要暂停所有线程时，能够安全地进行垃圾回收等全局任务。尽管Safepoint机制带来了一些挑战，但它对于保证JVM内部操作的一致性和程序的正确性至关重要。


## 到达Safepoint耗时较长的原因

导致线程到达Safepoint耗时较长的原因主要与线程的执行状态和代码特性有关。以下是一些常见的会导致到达Safepoint耗时较长的情况：

### 1. **长时间执行的本地方法（Native Method）**
   当Java线程正在执行本地方法（如通过JNI调用的C/C++代码）时，这些代码不会主动检查JVM的Safepoint请求。直到本地方法执行完毕并返回Java代码后，线程才有机会到达Safepoint。因此，如果本地方法执行时间过长，线程进入Safepoint的时间也会相应延长。

### 2. **无Safepoint的长循环**
   如果一个线程在执行没有Safepoint检查的长循环，那么直到该循环结束前，该线程都不会响应Safepoint请求。例如，一些简单的计算循环或条件很少改变的循环中，JVM不会在每次循环迭代时都插入Safepoint检查。这个特性虽然可以提升执行效率，但也可能导致线程无法及时进入Safepoint。（这个已经在JDK11中修改了）

### 3. **编译优化导致的长时间运行的代码块**
   JVM的JIT编译器（Just-In-Time Compiler）在对代码进行优化时，可能会将一些代码块编译成非常紧凑且高效的机器码。如果这些优化后的代码块很大并且没有包含Safepoint检查，那么这些代码段在执行期间不会响应Safepoint请求。

### 4. **线程处于阻塞或等待状态**
   如果线程正处于阻塞状态（例如I/O操作、等待锁、`Thread.sleep()`、`Object.wait()`等），JVM无法立即让这些线程响应Safepoint请求。直到线程从阻塞状态中恢复，才能检查并响应Safepoint请求。

### 5. **高CPU占用的线程**
   如果一个线程占用CPU的时间非常长，并且在其执行过程中没有足够的Safepoint检查，那么其他线程可能会一直等待该线程达到Safepoint。这种情况在高计算密集型的应用中尤其明显。

### 6. **过度的同步**
   线程处于重度同步状态时，由于锁的竞争或持有锁的线程未能及时进入Safepoint，可能导致其他线程被阻塞，最终延长整体进入Safepoint的时间。

### 7. **大量线程**
   当系统中有大量线程时，即使每个线程都能快速响应Safepoint请求，汇集所有线程进入Safepoint也可能需要更长时间，尤其是在高并发场景下。

### 解决策略
为了避免或减少到达Safepoint的延迟，开发者和JVM调优人员可以采取以下措施：
- **避免长时间的本地方法调用**，或在本地方法中定期调用Java代码来检查Safepoint请求。
- **优化长循环中的代码**，可以通过插入一些能触发Safepoint检查的操作，如方法调用。
- **减少无必要的高CPU占用**，通过合理优化代码和线程管理来减少对CPU的过度占用。
- **监控和调优同步操作**，减少锁竞争，提高系统整体的响应速度。

这些策略有助于减少线程响应Safepoint请求的延迟，从而提高系统的整体性能和响应能力。

## 具体例子

### Thread.Sleep

当线程调用 `Thread.sleep()` 时，它进入了休眠状态，这期间不会执行任何Java代码。因此，在 `sleep` 的过程中，该线程不会主动检查或进入Safepoint。如果 `sleep` 的时间很长，那么直到线程被唤醒之前，它都不会响应Safepoint请求。

#### 具体解释

1. **Thread.sleep()的执行**：
   - 当线程调用 `Thread.sleep()` 时，线程进入了操作系统层面的休眠状态。此时，线程不占用CPU资源，也不执行任何Java代码。
   - 在这个状态下，线程不会主动响应JVM的Safepoint请求，因为它没有在执行任何可以触发Safepoint的代码。

2. **JVM如何处理**：
   - JVM在触发Safepoint时，会向所有线程发送暂停请求，但休眠中的线程无法立即响应。
   - JVM必须等待 `Thread.sleep()` 的时间结束，线程被唤醒并恢复执行，才能检查和响应Safepoint请求。

3. **延迟问题**：
   - 如果 `Thread.sleep()` 时间较短，通常不会对Safepoint产生明显的延迟。
   - 但如果 `Thread.sleep()` 时间较长，其他线程可能需要等待这个休眠线程被唤醒后才能进入Safepoint，从而导致JVM执行Safepoint操作的时间延长。

如果要通过较长时间sleep来定时，建议通过ScheduledExecutorService等定时器来实现定时，而不是用sleep


### Object.wait()

Object.wait() 进入等待后，无法进入Safepoint

避免使用 `Object.wait()` 和 `Object.notify()` 进行线程间的协调的替代方案：

#### 1. 使用 `java.util.concurrent` 包

##### 1.1 `BlockingQueue`
`BlockingQueue` 是生产者消费者模式的常用实现，它提供了线程安全的阻塞操作。常见的实现包括 `ArrayBlockingQueue` 和 `LinkedBlockingQueue`。

```java
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;

public class ProducerConsumerExample {
    private static final int CAPACITY = 10;
    private static BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(CAPACITY);

    public static void main(String[] args) {
        Thread producer = new Thread(() -> {
            try {
                for (int i = 0; i < 100; i++) {
                    queue.put(i); // 如果队列满，put()会阻塞
                    System.out.println("Produced: " + i);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        Thread consumer = new Thread(() -> {
            try {
                for (int i = 0; i < 100; i++) {
                    Integer value = queue.take(); // 如果队列空，take()会阻塞
                    System.out.println("Consumed: " + value);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        producer.start();
        consumer.start();
    }
}
```

##### 1.2 `Semaphore`
`Semaphore` 可以用来限制同时访问某些资源的线程数，或者用来实现生产者消费者模式中的信号传递。

```java
import java.util.concurrent.Semaphore;

public class SemaphoreExample {
    private static Semaphore semaphore = new Semaphore(1); // 允许一个线程访问资源

    public static void main(String[] args) {
        Runnable task = () -> {
            try {
                semaphore.acquire(); // 获取许可证
                // 执行任务
                System.out.println(Thread.currentThread().getName() + " acquired semaphore.");
                Thread.sleep(1000);
                System.out.println(Thread.currentThread().getName() + " released semaphore.");
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            } finally {
                semaphore.release(); // 释放许可证
            }
        };

        new Thread(task).start();
        new Thread(task).start();
    }
}
```

##### 1.3 `CountDownLatch`
`CountDownLatch` 用于在多个线程完成某些操作之前，使主线程等待。适用于需要等待多个线程完成某些任务的场景。

```java
import java.util.concurrent.CountDownLatch;

public class CountDownLatchExample {
    private static CountDownLatch latch = new CountDownLatch(3); // 需要等待3个线程完成

    public static void main(String[] args) {
        Runnable task = () -> {
            try {
                Thread.sleep(1000); // 模拟任务
                System.out.println(Thread.currentThread().getName() + " completed.");
                latch.countDown(); // 任务完成，计数器减1
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        };

        for (int i = 0; i < 3; i++) {
            new Thread(task).start();
        }

        try {
            latch.await(); // 等待所有任务完成
            System.out.println("All threads have completed.");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

#### 2. 使用 `java.util.concurrent.locks` 包

##### 2.1 `ReentrantLock` 和 `Condition`
`ReentrantLock` 提供了比 `synchronized` 更灵活的锁机制，可以与 `Condition` 对象一起使用来实现类似 `Object.wait()` 和 `Object.notify()` 的功能。

```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockExample {
    private static ReentrantLock lock = new ReentrantLock();
    private static Condition condition = lock.newCondition();

    public static void main(String[] args) {
        Runnable producer = () -> {
            lock.lock();
            try {
                // 执行任务
                System.out.println("Producer is working.");
                condition.signal(); // 发出信号
            } finally {
                lock.unlock();
            }
        };

        Runnable consumer = () -> {
            lock.lock();
            try {
                condition.await(); // 等待信号
                System.out.println("Consumer received signal.");
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            } finally {
                lock.unlock();
            }
        };

        new Thread(producer).start();
        new Thread(consumer).start();
    }
}
```


### RPC阻塞IO

对于远程通讯，如果使用阻塞IO，在读写的时候，如果时间过长都会阻止线程进入safepoint

为了避免这种问题，需要设置合理的连接和超时时间。例如：

```java
import org.apache.http.client.config.RequestConfig;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.client.HttpComponentsClientHttpRequestFactory;
import org.springframework.web.client.RestTemplate;

public class RestTemplateConfig {
    public static RestTemplate createRestTemplate() {
        RequestConfig requestConfig = RequestConfig.custom()
                .setConnectTimeout(5000)   // 连接超时
                .setSocketTimeout(5000)    // 读取超时
                .setConnectionRequestTimeout(5000) // 请求超时（即连接池中的请求超时）
                .build();

        CloseableHttpClient httpClient = HttpClients.custom()
                .setDefaultRequestConfig(requestConfig)
                .build();

        HttpComponentsClientHttpRequestFactory factory = new HttpComponentsClientHttpRequestFactory(httpClient);
        return new RestTemplate(factory);
    }
}
```

在这个配置中：

- **`setConnectTimeout(5000)`**：设置连接超时为 5000 毫秒（5 秒）。
- **`setSocketTimeout(5000)`**：设置读取超时为 5000 毫秒（5 秒）。
- **`setConnectionRequestTimeout(5000)`**：设置从连接池获取连接的超时。

通过设置这些超时，你可以确保线程在遇到网络问题或响应延迟时不会被无限期地阻塞，进而有助于线程进入 safepoint。


但是我们的超时比较严格，而超时又太宽泛（例如请求超时是300秒，连接超时是15秒）。 导致会出现潜在的无法进入safepoint，从而卡住其他线程，从而引发心跳超时误判。


要们修改合理的超时时间，要么采用异步IO。例如：pring 提供了非阻塞 I/O 的解决方案，通过 WebClient 进行 HTTP 请求，它是 Spring WebFlux 的一部分。WebClient 支持异步和非阻塞的 I/O 操作，这样可以避免使用阻塞 I/O 的 RestTemplate 可能导致的性能问题，包括线程无法进入 safepoint。


Spring 提供了非阻塞 I/O 的解决方案，通过 `WebClient` 进行 HTTP 请求，它是 `Spring WebFlux` 的一部分。`WebClient` 支持异步和非阻塞的 I/O 操作，这样可以避免使用阻塞 I/O 的 `RestTemplate` 可能导致的性能问题，包括线程无法进入 safepoint。

```java
import org.springframework.web.reactive.function.client.WebClient;
import reactor.core.publisher.Mono;

public class WebClientExample {
    private static WebClient webClient = WebClient.create("https://api.example.com");

    public static void main(String[] args) {
        Mono<String> response = webClient.get()
                .uri("/endpoint")
                .retrieve()
                .bodyToMono(String.class);

        response.subscribe(
            result -> System.out.println("Response received: " + result),
            error -> System.err.println("Error occurred: " + error)
        );
    }
}
```

- **异步操作**：`WebClient` 支持异步调用，不会阻塞线程，允许其他操作在等待 I/O 完成时进行。
- **反应式编程**：通过 `Mono` 和 `Flux` 实现反应式编程模型，更好地处理高并发和大量数据。

虽然 `WebClient` 本身是非阻塞的，你依然可以配置连接超时和响应超时：

```java
import org.springframework.http.client.reactive.ReactorClientHttpConnector;
import org.springframework.web.reactive.function.client.WebClient;
import reactor.netty.http.client.HttpClient;
import java.time.Duration;

public class WebClientConfig {
    public static WebClient createWebClient() {
        HttpClient httpClient = HttpClient.create()
                .responseTimeout(Duration.ofSeconds(5))
                .option(io.netty.handler.timeout.HttpClientOptions.CONNECT_TIMEOUT_MILLIS, 5000);

        return WebClient.builder()
                .clientConnector(new ReactorClientHttpConnector(httpClient))
                .build();
    }
}
```

通过使用 `WebClient`，你可以实现非阻塞的 HTTP 请求，这样可以更高效地管理线程，减少因线程阻塞而无法进入 safepoint 的问题。

### InputStream的阻塞

InputStream是阻塞 I/O 的，这意味着在进行读取操作时，如果没有数据可读，线程将被阻塞，直到数据变得可用或发生超时。这种阻塞会影响线程的可调度性，并可能导致线程无法在适当的时间进入 safepoint，从而影响垃圾回收和系统性能。

#### 解决方案

为避免阻塞 I/O 导致的问题，可以考虑以下几种替代方案：

1. **使用非阻塞 I/O**：
   - 在 Java 中，`java.nio` 包提供了非阻塞 I/O 操作，例如 `AsynchronousFileChannel` 和 `AsynchronousSocketChannel`，这些可以用于实现非阻塞的读取和写入操作。

2. **使用反应式编程模型**：
   - 如前所述，`WebClient` 是一个基于反应式编程模型的 Web 客户端，支持非阻塞 I/O 操作。如果你的应用场景涉及 HTTP 请求，可以考虑使用 `WebClient` 替代 `InputStream` 进行数据传输。

3. **设置超时**：
   - 对于 `InputStream`，可以使用带有超时机制的流类，例如 `Socket` 类中的 `setSoTimeout()` 方法，为网络流设置读取超时。

`BufferedReader` 的 `readLine()` 方法无法直接设置超时，如果底层的 `InputStream` 被阻塞，那么 `readLine()` 也会被阻塞。为了避免这种阻塞情况，你可以考虑以下替代方案：

#### 1. 使用非阻塞 I/O

##### 1.1 `java.nio` 包
使用 `java.nio` 的 `AsynchronousSocketChannel` 和 `AsynchronousFileChannel` 实现非阻塞 I/O 操作，这样可以避免 `InputStream` 和 `BufferedReader` 的阻塞问题。

```java
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousSocketChannel;
import java.nio.channels.CompletionHandler;
import java.util.concurrent.CountDownLatch;

public class NonBlockingExample {
    public static void main(String[] args) throws Exception {
        AsynchronousSocketChannel client = AsynchronousSocketChannel.open();
        CountDownLatch latch = new CountDownLatch(1);

        client.connect(new InetSocketAddress("example.com", 80), null, new CompletionHandler<Void, Void>() {
            @Override
            public void completed(Void result, Void attachment) {
                ByteBuffer buffer = ByteBuffer.allocate(1024);
                client.read(buffer, null, new CompletionHandler<Integer, Void>() {
                    @Override
                    public void completed(Integer result, Void attachment) {
                        buffer.flip();
                        while (buffer.hasRemaining()) {
                            System.out.print((char) buffer.get());
                        }
                        latch.countDown();
                    }

                    @Override
                    public void failed(Throwable exc, Void attachment) {
                        exc.printStackTrace();
                        latch.countDown();
                    }
                });
            }

            @Override
            public void failed(Throwable exc, Void attachment) {
                exc.printStackTrace();
                latch.countDown();
            }
        });

        latch.await(); // 等待操作完成
        client.close();
    }
}
```

#### 2. 使用 `Socket` 类的超时机制

##### 2.1 设置读取超时
对于 `InputStream`，可以使用 `Socket` 类的 `setSoTimeout()` 方法来设置超时。

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.net.Socket;

public class SocketTimeoutExample {
    public static void main(String[] args) {
        try (Socket socket = new Socket("example.com", 80)) {
            socket.setSoTimeout(5000); // 设置读取超时为 5000 毫秒
            BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            String line = reader.readLine(); // 可能会阻塞，直到超时
            if (line != null) {
                System.out.println("Received: " + line);
            } else {
                System.out.println("No data received before timeout.");
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 3. 使用 `CompletableFuture` 进行超时控制

通过 `CompletableFuture` 结合 `ExecutorService` 来控制读取操作的超时。

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.net.Socket;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class CompletableFutureTimeoutExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        try (Socket socket = new Socket("example.com", 80);
             BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()))) {

            CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
                try {
                    return reader.readLine();
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }, executor);

            String result = future.get(5, TimeUnit.SECONDS); // 设置超时为 5 秒
            System.out.println("Received: " + result);

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            executor.shutdown();
        }
    }
}
```

通过这些替代方案，你可以避免 `BufferedReader.readLine()` 方法可能引起的阻塞问题，并更好地控制超时。：
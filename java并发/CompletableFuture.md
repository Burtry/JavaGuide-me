## 创建 CompletableFuture



常见的创建 `CompletableFuture` 对象的方法如下：

1. 通过 new 关键字。
2. 基于 `CompletableFuture` 自带的静态工厂方法：`runAsync()`、`supplyAsync()` 。



## runAsync和supplyAsync区别

`runAsync` 用于执行不返回结果的异步任务。它返回一个 `CompletableFuture<Void>`。

`supplyAsync` 用于执行有返回结果的异步任务。它返回一个 `CompletableFuture<T>`，其中 `T` 是返回值的类型。



## 处理异步结算的结果

比较常用的方法有下面几个：

- `thenApply()`
- `thenAccept()`
- `thenRun()`
- `whenComplete()`

### `thenApply()`

- **作用**：在 `CompletableFuture` 完成并且返回结果后，应用一个函数对结果进行转换或处理。
- **参数**：接收一个 `Function<T, U>`，其中 `T` 是前一个任务的结果类型，`U` 是转换后的类型。
- **返回值**：返回一个新的 `CompletableFuture<U>`，它持有转换后的结果。



### . `thenAccept()`

- **作用**：在 `CompletableFuture` 完成并且返回结果后，接收结果进行消费但不返回新的结果。
- **参数**：接收一个 `Consumer<T>`，其中 `T` 是前一个任务的结果类型。
- **返回值**：返回一个新的 `CompletableFuture<Void>`，表示该操作已完成。

### `thenRun()`

- **作用**：在 `CompletableFuture` 完成后，执行一个不依赖于前一个任务结果的操作。
- **参数**：接收一个 `Runnable`，它不接受任何参数，也不返回结果。
- **返回值**：返回一个新的 `CompletableFuture<Void>`，表示该操作已完成。



### `whenComplete()`

- **作用**：在 `CompletableFuture` 完成后，无论任务是否成功或失败，都会执行的操作。它可以查看但不会修改结果。
- **参数**：接收一个 `BiConsumer<T, Throwable>`，其中 `T` 是前一个任务的结果类型，`Throwable` 是异常（如果有的话）。
- **返回值**：返回一个新的 `CompletableFuture<T>`，它的结果与原始 `CompletableFuture` 相同。



### 异常处理

可以通过 `handle()` 方法来处理任务执行过程中可能出现的抛出异常的情况。

##组合 CompletableFuture



可以使用 `thenCompose()` 按顺序链接两个 `CompletableFuture` 对象，实现异步的任务链。它的作用是将前一个任务的返回结果作为下一个任务的输入参数，从而形成一个依赖关系。



比如说，task1 和 task2 都是异步执行的，但 task1 必须执行完成后才能开始执行 task2（task2 依赖 task1 的执行结果）。

和 `thenCompose()` 方法类似的还有 `thenCombine()` 方法， 它同样可以组合两个 `CompletableFuture` 对象。



## thenCompose() 和 thenCombine() 有什么区别



- `thenCompose()` 可以链接两个 `CompletableFuture` 对象，并将前一个任务的返回结果作为下一个任务的参数，它们之间**存在着先后顺序**。

- `thenCombine()` 会在两个任务都执行完成后，把两个任务的结果合并。两个任务是并行执行的，它们之间并**没有先后依赖顺序**。

  ​

## 并行运行多个 CompletableFuture

你可以通过 `CompletableFuture` 的 `allOf()`这个静态方法来并行运行多个 `CompletableFuture` 。

实际项目中，我们经常需要并行运行多个互不相关的任务，这些任务之间没有依赖关系，可以互相独立地运行。





## CompletableFuture 使用建议

### 使用自定义线程池

`CompletableFuture` 默认使用`ForkJoinPool.commonPool()` 作为执行器，这个线程池是全局共享的，可能会被其他任务占用，导致性能下降或者饥饿。因此，建议使用自定义的线程池来执行 `CompletableFuture` 的异步任务，可以提高并发度和灵活性。



```
private ThreadPoolExecutor executor = new ThreadPoolExecutor(10, 10,
        0L, TimeUnit.MILLISECONDS,
        new LinkedBlockingQueue<Runnable>());

CompletableFuture.runAsync(() -> {
     //...
}, executor);
```



### 尽量避免使用 get()

`CompletableFuture`的`get()`方法是阻塞的，尽量避免使用。如果必须要使用的话，需要添加超时时间，否则可能会导致主线程一直等待，无法执行其他任务。



### 正确进行异常处理

使用 `CompletableFuture`的时候一定要以正确的方式进行异常处理，避免异常丢失或者出现不可控问题。



下面是一些建议：

- 使用 `whenComplete` 方法可以在任务完成时触发回调函数，并正确地处理异常，而不是让异常被吞噬或丢失。
- 使用 `exceptionally` 方法可以处理异常并重新抛出，以便异常能够传播到后续阶段，而不是让异常被忽略或终止。
- 使用 `handle` 方法可以处理正常的返回结果和异常，并返回一个新的结果，而不是让异常影响正常的业务逻辑。
- 使用 `CompletableFuture.allOf` 方法可以组合多个 `CompletableFuture`，并统一处理所有任务的异常，而不是让异常处理过于冗长或重复。



### 合理组合多个异步任务

正确使用 `thenCompose()` 、 `thenCombine()` 、`acceptEither()`、`allOf()`、`anyOf()`等方法来组合多个异步任务，以满足实际业务的需求，提高程序执行效率。

实际使用中，我们还可以利用或者参考现成的异步任务编排框架，比如京东的 [asyncTool](https://gitee.com/jd-platform-opensource/asyncTool) 。
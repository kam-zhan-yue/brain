Most computer programs are executed in the same order in which they are written. The first line executes, then the next, and so on. With synchronous programming, when a program encounters an operation that cannot be completed immediately, it will block until the next operation completes. For example, establishing a TCP connection requires an exchange with a peer over the network, which can take a sizeable amount of time. During this time, the thread is blocked.

Through asynchronous programming, operations that cannot complete immediately are suspended to the background. The thread is not blocked, and can continue running other things. Once the operation completes, the task is unsuspended and continues processing from where it left off.

Asynchronous programs can result in faster applications, but often results in much more complicated programs as the programmer has to track all the state necessary to resume work once the asynchronous operation completse.


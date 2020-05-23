# User and Kernel Level Threads


### Kernel-Level Theads

These threads are also known as "OS-level threads" or "native threads". Kernel-level threads are managed by the OS. This means all thread scheduling happens in the kernel. For that to happen, the kernel maintains a thread table that keeps track of all threads in the system. These threads are created by making a system call into the OS.

**Advantages:**

- The scheduler can give more time to a process having a large number of threads rather than to a process having a smaller number of threads. This is possible as it keeps track of all threads in the system.

**Disadvantages:**

- These threads are slow because they need to switch into the kernel mode for scheduling purposes.
- As the kernel is not only responsible for scheduling at the process level but also at the thread level, it increases the kernel complexity.

### User-Level Threads

![User-level threads](https://www.tutorialspoint.com/operating_system/images/user_threads.jpg)

These threads are also known as "green threads". These threads are managed by the user-level runtime library and **not** the OS. The kernel knows nothing about the user-level threads and as far it is concerned, it is only managing a single-threaded process. Most of the thread level activities like creating new threads, switching between threads, syncronization, all happen without kernel involvement. One other thing to keep in mind is that some ***blocking calls*** like reading a file (`data = file.read()`) are converted to ***asynchronous calls*** by the user-level library so other user level threads are not blocked.

**Advantages:**

- Much faster than kernel-level threads as switching of modes betwen user mode to kernel mode is not required for most thread level activities.

**Disadvanges:**

- As the kernel has no information regarding user-level threads, it does not have a process level thread count. Hence process scheduling decisions are not made keeping the thread count.
- A single blocking call by a user-level thread can block the entire process. To overcome this, most OS kernels have a scheduler activations mechanism. Read more about it [here](https://en.wikipedia.org/wiki/Scheduler_activations).

----
#### More References:
 
- [TutorialsPoint](https://www.tutorialspoint.com/operating_system/os_multi_threading.htm)
- [Oracle](https://docs.oracle.com/cd/E19455-01/806-5257/mtintro-54820/index.html)
- [Illinois Institute of Technology](http://www.cs.iit.edu/~cs561/cs450/ChilkuriDineshThreads/dinesh%27s%20files/User%20and%20Kernel%20Level%20Threads.html)
- [Stack Overflow](https://stackoverflow.com/a/15984127/2156877)

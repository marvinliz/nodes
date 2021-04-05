# ThreadLocal 实现

将自己存入Thread#ThreadLocalMap中，这样就能从不同的Thread中获取不同ThreadLocal对象，从而获取到不同的值。

当hash冲突时，会选择下一个空闲的地方。

# WeakReference

ThreadLocalMap是个数组，里面存的是一个weakReference的ThrealLocal对象。为了避免内存泄漏问题，当线程栈中强引用失效后，就可以被垃圾回收掉。当被垃圾回收后，get()会返回null，然后进行清理工作。


# 我对Volatile的理解


# Volatile 
> Java 语言中的 volatile 变量可以被看作是一种 “程度较轻的 `synchronized`”；与 `synchronized` 块相比，volatile 变量所需的编码较少，并且运行时开销也较少，但是它所能实现的功能也仅是 `synchronized` 的一部分。

锁提供了两种主要特性： 互斥（`mutual exclusion`）和 可见性（`visbility`）。互斥即一次只允许一个线程持有某个特定的锁，因此可使用该特性对共享数据的协调访问协议，因此，一次就只有一个线程能够使用该共享数据。可见性，要更加复杂，它必须确定释放锁之前，对共享数据做出的更改，对其他线程是可见的。如果没有同步机制提供的这种可见性，其他线程看到的共享变量可能是修改前的值或不一致的值。

而Volatile变量具有synchronized的可见性特性，但是不具备原子特性。

因此，volatile具备的一种特性就是保证共享变量对所有线程的可见性。声明成volatile后的共享变量，会有一下效应：
1. 当写一个volatile变量是，JMM会吧该线程对应的本地内存中的变量强制刷新到主内存中去；
2. 这个写操作会导致其他线程中对这个变量的缓存无效。





















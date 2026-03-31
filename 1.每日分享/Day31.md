## Day31_Monday_Week6_3.30

1. **死锁-408笔记** -- gzh

   ## 2.4 死锁

   ### 补充：

   1. 系统发生死锁时，必然有两个或两个以上进程处于阻塞态
   2. 银行家算法只能判断系统是否处于安全状态，而不能判断是否处于死锁状态（能判断为不死锁状态即安全状态）
   3. ![image-20241205150622137](../0.TyporaPicture/image-20241205150622137.png?lastModify=1774852216)

   ### 2.4.1 死锁的概念

   ![image-20241016192535807](../0.TyporaPicture/image-20241016192535807.png?lastModify=1774852216)

   

   #### 1.什么是死锁

   ![image-20241016192634082](../0.TyporaPicture/image-20241016192634082.png?lastModify=1774852216)

   

   #### 2.死锁、饥饿、死循环的区别

   ![image-20241016192707028](../0.TyporaPicture/image-20241016192707028.png?lastModify=1774852216)

   

   #### 3.死锁产生的必要条件

   ![image-20241016192724045](../0.TyporaPicture/image-20241016192724045.png?lastModify=1774852216)

   

   #### 4.什么时候会发生死锁

   ![image-20241016192806059](../0.TyporaPicture/image-20241016192806059.png?lastModify=1774852216)

   

   ### 2.4.2 死锁的处理策略

   

   ![image-20241016192856157](../0.TyporaPicture/image-20241016192856157.png?lastModify=1774852216)

   

   #### 1.预防死锁——破坏死锁产生的四个必要条件中的一个或几个

   ![image-20241016192915065](../0.TyporaPicture/image-20241016192915065.png?lastModify=1774852216)

   

   ##### （1）破坏互斥条件

   ![image-20241016193000590](../0.TyporaPicture/image-20241016193000590.png?lastModify=1774852216)

   

   ##### （2）破坏不剥夺条件

   ![image-20241016193014981](../0.TyporaPicture/image-20241016193014981.png?lastModify=1774852216)

   

   ##### （3）破坏请求和保持条件

   ![image-20241016193032967](../0.TyporaPicture/image-20241016193032967.png?lastModify=1774852216)

   

   ##### （4）破坏循环等待条件

   ![image-20241016193051272](../0.TyporaPicture/image-20241016193051272.png?lastModify=1774852216)

   

   #### 2.避免死锁(银行家算法)——用某种方法防止系统进入不安全状态从而避免死锁——银行家算法-是避免不是预防—”安全一定无死锁，死锁一定不安全，不安全不一定死锁“

   **预防死锁**（即接下来不可能产生死锁）**才是破坏了死锁产生的必要条件**，而银行家算法是避免死锁（即死锁随时可能发生，通过手段规避死锁），没有破坏必要条件。

   **本质：** 安全状态：**此时必定无死锁**，能继续进行下去； 不安全状态：**可能(不是必定)发生死锁，只表明存在发生死锁的可能性**，即无法按照**既定计划**进行下去

   当死锁已经发生时肯定是处于不安全状态，因为已经找不到继续进行下去的方案路径了即没有安全序列了，所以一定处于不安全状态；而在死锁未发生之前，无论是处于安全/不安全状态，**接下来**都有可能发生或者不发生死锁，即处于不安全状态时也不意味着现在一定处于死锁状态。唯一能确定瞬时状态的是——**安全时一定无死锁，死锁时一定不安全**

   ##### （1）安全序列、不安全状态及其与死锁的联系——安全性算法（银行家算法的核心）

   ![image-20241016193338412](../0.TyporaPicture/image-20241016193338412.png?lastModify=1774852216)

   

   ##### （2）银行家算法

   ![image-20241016193502463](../0.TyporaPicture/image-20241016193502463.png?lastModify=1774852216)

   ![image-20241016193529933](../0.TyporaPicture/image-20241016193529933.png?lastModify=1774852216)

   

   #### 3.死锁的检测与解除——允许死锁的发生，不过操作系统会负责检测出死锁的发生，然后采取某种措施解除死锁

   ![image-20241016193610409](../0.TyporaPicture/image-20241016193610409.png?lastModify=1774852216)

   

   ##### （1）死锁的检测

   ![image-20241016193708242](../0.TyporaPicture/image-20241016193708242.png?lastModify=1774852216)

   ![image-20241016193908830](../0.TyporaPicture/image-20241016193908830.png?lastModify=1774852216)

   

   ##### （2）死锁的解除

   ![image-20241016193931134](../0.TyporaPicture/image-20241016193931134.png?lastModify=1774852216)

   

   ### 2.4.3 本节小结

   #### 1.为什么会发生死锁？产生死锁有什么条件？

   ![image-20241016194142964](../0.TyporaPicture/image-20241016194142964.png?lastModify=1774852216)

   

   #### 2.有什么办法可以解决死锁问题？

   ![image-20241016194153207](../0.TyporaPicture/image-20241016194153207.png?lastModify=1774852216)

   

   ## 2.5 本章疑难点与补充

   ### 1.进程与程序的区别与练习

   ![image-20241016194658481](../0.TyporaPicture/image-20241016194658481.png?lastModify=1774852216)

   

   ### 2.银行家算法的工作原理

   ![image-20241016194713177](../0.TyporaPicture/image-20241016194713177.png?lastModify=1774852216)

   

   ### 3.进程同步、互斥的区别和联系

   ![image-20241016194734267](../0.TyporaPicture/image-20241016194734267.png?lastModify=1774852216)

2. **标题** -- ywj

   

3. **pthread 库函数及相关工具函数** -- lw

   ### . 线程创建与基础操作

   - `pthread_create` – 创建线程  
   - `pthread_self` – 获取当前线程 ID  
   - `pthread_exit` – 主动退出线程  
   - `pthread_join` – 等待线程结束并回收资源  
   - `pthread_cancel` – 请求取消另一个线程  
   - `pthread_testcancel` – 手动添加取消点

   ---

   ### 2. 线程清理函数

   - `pthread_cleanup_push` – 向清理栈压入一个清理函数  
   - `pthread_cleanup_pop` – 弹出清理栈顶的函数（可选择执行）

   ---

   ### 3. 互斥锁（Mutex）

   - `pthread_mutex_init` – 初始化互斥锁  
   - `pthread_mutex_destroy` – 销毁互斥锁  
   - `pthread_mutex_lock` – 加锁（阻塞）  
   - `pthread_mutex_trylock` – 非阻塞尝试加锁  
   - `pthread_mutex_unlock` – 解锁  

   - `pthread_mutexattr_init` – 初始化互斥锁属性  
   - `pthread_mutexattr_destroy` – 销毁互斥锁属性  
   - `pthread_mutexattr_settype` – 设置互斥锁类型（如普通锁、检错锁、递归锁）

   ---

   ### 4. 自旋锁（Spin Lock）

   - `pthread_spin_init` – 初始化自旋锁  
   - `pthread_spin_lock` – 获取自旋锁（循环等待）  
   - `pthread_spin_unlock` – 释放自旋锁  
   - `pthread_spin_destroy` – 销毁自旋锁

   ---

   ### 5. 读写锁（RWLock）

   - `pthread_rwlock_rdlock` – 加读锁  
   - `pthread_rwlock_wrlock` – 加写锁  
   - `pthread_rwlock_unlock` – 解锁（读或写）  
     （注：读写锁的初始化和销毁函数未在笔记中详列，实际标准库还有 `pthread_rwlock_init` / `pthread_rwlock_destroy`）

   ---

   ### 6. 条件变量（Condition Variable）

   - `pthread_cond_init` – 初始化条件变量  
   - `pthread_cond_destroy` – 销毁条件变量  
   - `pthread_cond_wait` – 等待条件（与互斥锁配合）  
   - `pthread_cond_timedwait` – 带超时的等待  
   - `pthread_cond_signal` – 唤醒一个等待线程  
   - `pthread_cond_broadcast` – 唤醒所有等待线程

   ---

   ### 7. 常用辅助函数（非 pthread 但配套使用）

   - `gettimeofday` – 获取微秒级时间（用于计时）  
   - `sleep` – 秒级睡眠（取消点函数之一）  
   - `printf`、`read`、`write` 等 I/O 函数也是常见的取消点

   ---

   ### 8. 常用宏与常量

   - `PTHREAD_MUTEX_INITIALIZER` – 静态初始化互斥锁  
   - `PTHREAD_COND_INITIALIZER` – 静态初始化条件变量  
   - `PTHREAD_CANCELED` – 线程被取消时 `pthread_join` 返回的值

   

4. **通过pthread_cond_signal唤醒的线程后续发生了什么？**-- lzw

   ​	通过pthread_cond_signal唤醒的线程, 如果无法获取锁, 会立即进入阻塞, 直到锁空闲, 再自动去获取锁, 并持有锁。

   这里理解有两点：

   **1.即使被pthread_cond_signal唤醒也不一定能拿到锁**

   **2.拿不到等一会重新拿**

   对于第一点，因为在线程运行中可能会有其他程序在争抢锁，所以，即使被唤醒了也不定抢得到锁。

   对于第二点，**被唤醒了就意味着一定满足了之前抢到锁，但线程运行条件不足的条件。**只需要等着重新拿锁就行了（睡觉的原因就是首次抢到锁的时候进入了临界区进行了条件判断，发现不满足线程运行条件之后才睡眠。因此不用再担心条件判断的问题）

   1）被 `pthread_cond_signal` 唤醒 = **条件已经满足了**

   - 有人（另一个线程）修改完共享数据
   - **主动发信号**：`条件满足了！你们可以起来干活了！`
   - 所以：**被唤醒的线程，默认就是条件已经 OK 了**

   2）但唤醒后**第一件事不是干活，是抢锁**

   唤醒流程是这样的：

   1. 线程被唤醒
   2. **立刻尝试获取锁**
   3. **拿不到 → 自动阻塞**（内核帮你等，不用你写循环）
   4. **锁一空闲 → 自动拿到锁、持有锁、继续执行**

​		


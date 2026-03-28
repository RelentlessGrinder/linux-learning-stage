## Day30_Saturday_Week5_3.28

1. **pthread_cleanup_push与pthread_cleanup_pop** -- gzh

   ```c
   // 当线程开始执行时，会创建一个清理栈（thread's stack of thread-cancellation），用于存储注册的清理函数和参数。
   // 通过过向这个清理栈中push添加清理函数，以及pop出清理函数执行，从而确保线程的正确终止和资源回收
   
   #include <pthread.h>
   // push thread cancellation clean-up handlers
   void pthread_cleanup_push(
   	void (*routine)(void *),// 指向清理函数的指针，该函数将在线程取消时被调用
   	void *arg // 传递给清理函数的参数
   );
   
   #include <pthread.h>
   // pop thread cancellation clean-up handlers
   void pthread_cleanup_pop(
   	int execute // 设置参数0代表弹出栈顶函数并且'不执行'这个函数， 非0代表代表弹出栈顶函数并且'执行'这个函数
   );
   ```

   - ==pthread_cleanup_push 和 pop 必须在同一个函数内成对出现！即要处于同一个作用域==

   - 如果子线程**被取消（pthread_cancel）**

     **自动执行**所有 `pthread_cleanup_push` 压入的清理函数。（不需要 `pop`，系统自己跑，用来释放资源）

   - 如果子线程**正常运行结束**（没被取消）

     - `pop(1)`：执行清理函数（释放资源）
     - `pop(0)`：不执行清理函数

2. **标题** -- ywj

   

3. **标题** -- lw

   

4. **标题** -- lzw

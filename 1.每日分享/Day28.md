## Day28_Thursday_Week5_3.26

1. **信号相关的函数与结构体** -- gzh

   ==**相关函数**==

   1. **signal**

      用来捕获信号并且指定对应的信号处理行为

      ```c
      #include <signal.h>
      // 定义信号处理函数的类型: int类型的参数(信号编号), void返回值
      typedef void (*sighandler_t)(int);
      
      sighandler_t signal(
          int signum, // 要处理的信号编号(Eg:SIGINT、SIGTERM...)(Eg:2,15...)
          sighandler_t handler // 指向信号处理函数(回调机制): 如上面sighandler_t定义, (另外:SIG_IGN表示忽略信号; SIG_DFL表示恢复信号的默认行为)
      );
      // 返回值: 成功返回关联的指定信号的处理函数的指针; 失败返回SIG_ERR
      ```

   2. **sigaction**

      在**signal**的基础上更精确控制信号处理行为                   			<a id="sigaction back"></a>[跳转到结构体struct sigaction](#sigaction)

      ```c
      #include <signal.h>
      // examine and change a signal action
      int sigaction(
          int signum,                  // 要操作的信号编号(除了不能捕获的SIGKILL和SIGSTOP)
          const struct sigaction *act, // 指定信号的新处理动作(如果非空)
          struct sigaction *oldact     // 获取信号的上一个处理动作(如果非空)
      );
      // 返回值: 成功时返回0，错误时返回-1
      ```

   3. **操作sigset_t类型变量(即struct sigaction中的sa_mask成员)的一系列函数**

      ```c
      sigemptyset(sigset_t *set)：// 初始化信号集，清除所有信号。
      sigfillset(sigset_t *set)：// 添加所有信号到信号集中。
      sigaddset(sigset_t *set, int signo)：// 向信号集添加一个信号。
      sigdelset(sigset_t *set, int signo)：// 从信号集中删除一个信号。
      sigismember(const sigset_t *set, int signo)：// 检查一个特定信号是否在信号集中。
      ```

   4. **sigpending**

      用于检查当前进程的未决信号集，即那些已经发送给进程但由于某种原因（通常是因为被阻塞）尚未被处理的信号。这个函数可以用来确定哪些信号已经被产生并等待处理，但尚未被当前进程捕获或忽略。

      ```c
      #include <signal.h>
      // examine pending signals
      int sigpending(
          sigset_t *set // 接收当前进程的未决信号集
      );
      // 返回值: 成功时返回0，失败返回-1
      ```

   5. **sigprocmask**

      用于在系统中检查和更改进程的信号屏蔽字（即信号掩码mask）。信号掩码确定了哪些信号可以递送给该进程，哪些信号被阻塞。和前面通过sigaction函数设置的sa_mask阻塞机制不同的是, **sigaction**函数设置的sa_mask阻塞是**临时屏蔽**。而**sigprocmask**函数修改信号掩码mask是**全程屏蔽**. (man sigprocmask)

      ```c
      #include <signal.h>
      // examine and change blocked signals
      int sigprocmask(
          int how, // 如何修改信号掩码.SIG_BLOCK:把set内信号添加阻塞;SIG_UNBLOCK:解除set内信号阻塞; SIG_SETMASK:将信号掩码替换为set指定信号
          const sigset_t *set, // 信号集合
          sigset_t *oldset // 当前信号掩码
      );
      // 返回值: 成功返回0, 失败返回-1
      ```

   6. **kill**

      用来给另一个进程**发送信号**

      ```c
      #include <sys/types.h>
      #include <signal.h>
      // send signal to a process
      int kill(
          pid_t pid, //表示进程ID (另外  0:发送信号到与发送进程相同进程组的所有进程;  -1:表示所有可以发送信号的进程发送信号; 小于-1:则根据其绝对值去关闭其作为组长的进程组)
          int sig // 信号数值
      );
      // 返回值: 成功0, 失败-1
      ```

   7. **pause**

      使调用进程挂起（即暂停执行）（==**注意：挂起完全不等于阻塞**==），直到该进程捕获到一个信号。换句话说，**pause函数**让进程休眠，等待任何类型的信号到来；一旦接收到信号，如果有为该信号定义的处理函数，则执行该函数。如果没有为信号定义处理函数（或者信号的行为是默认的），进程会根据信号的默认行为来响应。

      ```c
      #include <unistd.h>
      // wait for signal
      int pause(void);
      ```

   8. **sigsuspend**

      <span style=color:red;background:yellow;font-size:16px>**原子地**</span>更改进程的信号屏蔽字（block mask）并挂起进程执行，直到捕获到一个信号

      - 更改信号掩码和挂起进程之间提供原子操作
      - 在sigsuspend返回后，**进程的信号掩码会自动恢复到调用sigsuspend之前的状态（即mask会回退）**

      ```c
      #include <signal.h>
      // wait for a signal
      int sigsuspend(
          const sigset_t *mask // 指定了在挂起期间要设置的新信号掩码
      );
      ```

   9. **alarm**

      用于设置一个计时器（定时器），该计时器在指定的秒数后到期。当计时器到期时，内核会向该进程发送 `SIGALRM` 信号。如果程序没有捕获或忽略该信号，则其默认行为是终止进程

      ```c
      #include <unistd.h>
      // set an alarm clock for delivery of a signal
      unsigned int alarm(unsigned int seconds);
      ```

   10. **setitimer**													                       		   <a id="itimerval back"></a> [跳转到结构体struct itimerval](#itimerval)

       一个高级定时器接口，相较于**alarm函数**，它提供了更多的灵活性和精度

       ```c
       #include <sys/time.h>
       // set value of an interval timer
       int setitimer(
           int which, // 定时器的类型
           const struct itimerval *new_value, // 指定的新的定时器值
           struct itimerval *old_value // 存储定时器的前一个值
       );
       // 返回值: 成功0, 失败-1
       ```

   11. **getitimer**                                                                                                                                                     [跳转到结构体struct itimerval](#itimerval)

       用来**获取当前进程的间隔定时器状态**：**查看当前定时器还剩多少时间 + 是否在循环触发**

       ```c
       #include <sys/time.h>
       // get value of an interval timer
       int getitimer(
           int which, // 定时器的类型
           struct itimerval *curr_value // 
       );
       ```

   ==**结构体**==

   1. **struct sigaction**										        																<a id="sigaction"></a>[返回sigaction](#sigaction back)

      ```c
      // 是用于定义信号处理的行为
      struct sigaction 
      {
          void     (*sa_handler)(int);// 函数指针:指向一个信号处理函数 (和sa_sigaction选一个即可)
          void     (*sa_sigaction)(int, siginfo_t *, void *);// 函数指针:指向一个接受三个参数的信号处理函数
          sigset_t   sa_mask;// 信号集: 指定当前信号处理函数执行时需要阻塞的额外信号
          int        sa_flags;// 指定信号处理的选项和标志: 
          void     (*sa_restorer)(void);// 过时,暂无用
      };
      ```

      ```c
      siginfo_t 
      {
          //...
          pid_t    si_pid;   /* Sending process ID */
          sigval_t si_value; /* Signal value */
           // ......
      }
      ```

   2. **typedef struct sigset_t(即struct sigaction中的sa_mask成员)**

      ```c
      typedef struct {
      	unsigned long sig[_NSIG_WORDS];
      } sigset_t;
      ```

   3. **struct timeval**

      ```c
      struct timeval
      {
          long tv_sec;  /* seconds */
          long tv_usec; /* microseconds */
      };
      ```

   4. **struct itimerval**     															                                               <a id="itimerval"></a>[返回setitimer](#itimerval back)

      ```c
      struct itimerval 
      {
          struct timeval it_interval;// 间隔时间: 字段被设置为非零值，定时器将变为周期性的
          struct timeval it_value; // 定时器的剩余时间
          // 当定时器的it_value达到0并触发信号后，it_value会被重新设置为 it_interval 的值，然后定时器再次开始计时
      };
      ```

2. **标题** -- cjs

   ```c
   
   ```

3. **alarm和sigsuspend实现sleep以及课堂历史问题辨析** -- xkz

   ```c
   #include <my_header.h>
   
   /* Usage:  */
   void func(int sig_value)
   {
       printf("记得打卡\n");
   }
   void mySleep(int sec)
   {
       signal(SIGALRM, func);
       alarm(sec);
       sigset_t set;
       sigemptyset(&set);
       sigsuspend(&set);
   }
   
   int main(int argc,char* argv[])
   {
       ARGS_CHECK(argc,2);
       int num=0;
       sscanf(argv[1],"%d",&num);
   mySleep(num);
       printf("-- \n");
    
       return 0;
   }
   ```

   关于历史知名争议问题，杨广弑父说   总体的历史逻辑推论和历史证据都偏向于质疑

   1.唐编《隋书》中记载了大量隋炀帝不堪的事迹，但却并未有与杨广弑父显有关联的记载。

   2.隋文帝仁寿元年（600年），杨广 被立为太子，仁寿四年（604年），已总揽朝政，在隋文帝病重，朝堂已形成以太子为核心的新体系，杨广继任皇帝位已经是轻易无法改变的事实，无需通过冒险手段争夺权力，虽然杨素杨广通信的问题导致杨坚暴怒，病情加重，并称要复立废太子，但当时杨素已在杨广的控制下，风险极低。

   历史上公认的子弑父夺皇帝位的有：

   |  发动者  |          原因          |             事件             |     结果     | 时期                   |
   | :------: | :--------------------: | :--------------------------: | :----------: | ---------------------- |
   |  楚穆王  |      楚成王欲改立      |        楚穆王兵围王国        |     夺位     | 楚成王四十六年(前626 ) |
   |  拓跋绍  | 道武帝拓跋珪欲杀贺夫人 | 北魏宫廷政变寒食节袭杀拓跋珪 |     被杀     | 北魏天赐六年 (409)     |
   |   刘绍   |         巫蛊案         |          兵变杀文帝          | 刘骏平反称帝 | 刘宋元嘉三十年(453)    |
   | 李宁令哥 |    没藏黑云设计夺位    |          惊杀李元昊          |  李谅祚称帝  | 西夏大庆二年（1038）   |
   |  李友珪  |       朱温欲改立       |         后梁洛阳兵变         |   兵败被杀   | 后梁乾化二年（912）    |

   

4. **标题** -- zjl

   

5. **标题**-- glh

   

6. **标题**-- zzc

   

7. **标题** -- ywj

   

8. **sleep函数如何保证睡眠** -- lw

   > 首先看sleep睡眠的本质：
   >
   > 1. 主动放弃 CPU
   > 2. 交给内核定时器
   > 3. 时间到自动唤醒
   >
   > 只有信号能提前打断，否则一定睡够。

方法一：可以用循环结构保证睡眠：while (sleep(seconds) != 0);
方法二：使用sigaction函数，设置sa_flag,修改信号处理方式
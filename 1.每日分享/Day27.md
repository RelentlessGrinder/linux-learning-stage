## Day27_Wednesday_Week5_3.25

1. **C与C++对比** -- gzh

   | 维度          | C      | C++    |
   | :------------ | :----- | :----- |
   | 嵌入式/单片机 | ✅ 主流 | ❌ 很少 |
   | 操作系统内核  | ✅ 主流 | ❌ 极少 |
   | 驱动开发      | ✅ 主流 | ⚠️ 少量 |
   | 大型桌面应用  | ⚠️ 少量 | ✅ 主流 |
   | 游戏开发      | ⚠️ 底层 | ✅ 上层 |
   | 高频交易      | ⚠️ 部分 | ✅ 主流 |
   | Web 后端      | ❌ 极少 | ⚠️ 部分 |
   | 学习难度      | 简单   | 极难   |

2. **标题** -- cjs

   

3. **常见信号** -- xkz

   1.**SIGINT**   键盘中断  终止进程

   ctrl c ;  kill -2 [pid]

   2.**SIGQUIT**键盘退出，终止进程  产生转储文件/core/dumped   也可设置ulimit中的core-file-size为0，不生成

   ctrl \;kill -3 [pid]

   3.**SIGKILL**  强制终止进程

   kill -9 [pid]  不可捕获，不可忽略，不可阻塞

   4.**SIGSTOP**  暂停进程

   ctrl z;kill -19[pid]

   5.**SIGCONT** 继续暂停的进程

   kill -18 [pid]  

4. **标题** -- zjl

   STDOUT_FILENO 永远是1

   一会儿往屏幕输出一会儿往文件输出本质上是关闭1 分配1 循环的过程（1是文件描述符）

5. **标题**-- glh

   

6. **标题**-- zzc

   

7. **标题** -- ywj
   `read()/write()`的关键理解，write()的健壮写法

   - 它们操作的是**字节流**

   - 返回值是**实际处理的字节数**，不一定等于请求值

     > [!CAUTION]
     >
     > read函数指定了最大读取的字节数量是count，并通过返回值返回实际读到的字节数量。而**这个返回值，完全可能小于count**。
     >
     > write函数指定了这一次写操作需要输出的字节数量是count，并通过返回值返回实际输出写的字节数量。而这个返回值，**通常都是等于count的**。

   - `read()` 返回 `0` 表示读到文件末尾 EOF

   - 返回 `-1` 表示出错，并设置 `errno`

   ```c
   // 循环读取文件直到文件末尾或出错
   // read_count为0时表示读到文件末尾，read_count为-1时表示read出错，这两种情况都会结束循环 
   while ((read_count = read(fd, buf, sizeof(buf) - 1)) > 0) {
           printf("read_count = %ld, buf = %s\n", read_count, buf);
           memset(buf, 0, sizeof(buf));  // 清空缓冲区以准备下一次读取
       }
       // 检查是否是因为读取出错而退出循环
       ERROR_CHECK(read_count, -1, "read");
   ```

   ```c
   // 健壮写法：循环写完整个缓冲区
   ssize_t total = 0;
   // 为什么需要循环？
   // 1. 管道或 Socket 的内核缓冲区可能已满，导致只能写入一部分。
   // 2. 信号中断（EINTR）可能导致系统调用提前返回。
   // 3. 文件系统配额限制或磁盘空间临近不足。
   while (total < len) {
       ssize_t n = write(fd, buf + total, len - total);
       if (n == -1) {
           perror("write");// 发生错误（如：fd 失效、磁盘坏道等）
           break;
       }
       total += n;
   }
   
   // 循环读取文件直到文件末尾或出错,然后将读到的内容健壮写入fdw
   while ((read_count = read(fdr, buf, sizeof(buf))) > 0) {
        // 如果read成功，但是返回的read_count小于等于0，那么就没有必要继续了
       ssize_t total = 0;
   	while (total < read_count) {
      		ssize_t n = write(fdw, buf + total, read_count - total);
       	// 检查写入状态：
           // n < 0：发生错误（如磁盘满、管道断开）
           // n == 0：通常在写文件时不会发生，但在某些特定设备驱动下可能出现，防止死循环
           if (n <= 0) break;
       	total += n;
   	}
   }
   ```

   

8. **为什么同一个信号量可能有多个值** -- lw

   同一个信号名出现多个值，是因为它在不同 CPU 架构上的编号不同，这是 Linux/Unix 的历史与跨平台兼容设计导致的。
   在 kill -l 或 man 7 signal 里，常见格式是：
   SIGUSR1: 10, 30, 16
   这三个数字分别对应：
   值（10）：x86、x86_64、ARM、PPC 等主流架构（你现在用的电脑 / 服务器基本都是这个）
   值（30）：Alpha、SPARC 等老架
   值（16）：MIPS 架构

   不同架构信号值不一样，是因为：
   硬件中断号不同 + 早期各 UNIX 厂商自定义 + Linux 为了兼容老系统保留了多套编号
   所以敲代码不要直接写数字值

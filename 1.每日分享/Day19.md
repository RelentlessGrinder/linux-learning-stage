## Day19_Monday_Week4_3.16

1. **标题**-- gzh

   | 别名        | 原名           | 占位&说明 |
   | :---------- | :------------- | :-------- |
   | d_ino       | unsigned long  | %lu       |
   | off_t       | long           | %ld       |
   |             | unsigned short | %hu       |
   |             | unsigned char  | %hhu      |
   | mode_t      | usigned int    | %u        |
   | nlink_t     | usigned long   | %lu       |
   | uid_t gid_t | usigned int    | %u        |

2. **标题**-- cjs

   ```bash
   vim 中操作多窗口
   :new test.c -->上下分屏   :sp test.c 也是上下分屏 
   :vnew test.c --->左右分屏 :vsp test.c 也是左右分屏
   多窗口的切换
   ctrl + w,W (连续按两下w)
   
   vim中操作多标签
   :tabnew test.c
   切换多标签
   gt / gT
   ```

   

3. **标题**-- xkz

   ```c
   #include<dirent.h>
   long ret=telldir(DIR *dirp);
   //设置目录流指针的位置，靠long型返回值来进行移动
   ```

   ```c
   #include<dirent.h>
   seekdir(DIR *dirp, long ret);
   //在telldir记录位置后，使用ret返回值进行传送移动
   ```

   

4. **标题**-- zjl

   

5. **标题**-- glh

   

6. **标题**-- zzc

   生成一个可执行程序main

   ~~~makefile
   OUT := main
   SRCS := $(wildcard *.c)
   OBJS := $(patsubst %.c, %.o, $(SRCS))
   
   $(OUT) : $(OBJS)
   	$(CC) $^ -o $@
   %.o : %.c
   	$(CC) -c $^ -o $@
   .PHONY : clean rebuild
   clean : 
   	$(RM) $(OBJS) $(OUT)
   rebuild : clean main
   ~~~

   

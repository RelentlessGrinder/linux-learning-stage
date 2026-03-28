## Day20_Tuseday_Week2_3.17

1. **标题**-- gzh

   | 函数声明(返回值-函数名-参数)                                 | 头文件                                                  | 作用                                                     |
   | :----------------------------------------------------------- | :------------------------------------------------------ | :------------------------------------------------------- |
   | int chmod(const char *pathname, mode_t mode);                | #include <sys/stat.h>                                   | 改变文件的权限                                           |
   | char *getcwd(char *buf, size_t size);                        | #include <unistd.h>                                     | 获取当前工作目录                                         |
   | int chdir(const char *path);                                 | #include <unistd.h>                                     | 改变当前工作目录                                         |
   | int mkdir(const char *pathname, mode_t mode);                | #include <sys/stat.h> #include <sys/types.h>            | 创建目录                                                 |
   | int rmdir(const char *pathname);                             | #include <unistd.h>                                     | 删除目录                                                 |
   | DIR ***opendir**(const char *name); int closedir(DIR *dirp); | #include <dirent.h> #include <sys/types.h>              | 打开目录流 关闭目录流                                    |
   | struct dirent *readdir(DIR *dirp);                           | #include <dirent.h>                                     | 通过目录流读取下一个目录项                               |
   | long telldir(DIR *dirp); void seekdir(DIR *dirp, long loc);  | #include <dirent.h>                                     | 记录目录流指针的位置 返回记录的位置                      |
   | void rewinddir(DIR *dirp);                                   | #include <dirent.h>                                     | 重置目录流的位置到开始处                                 |
   | int stat(const char *path, struct stat *buf);                | #include <sys/stat.h>                                   | 用于获取指定文件的相关详细信息 主要包括各种元数据信息    |
   | **POSIX标准库函数** struct passwd *getpwuid(uid_t uid); struct passwd *getpwuid(uid_t uid); | #include<sys/types.h> #include <pwd.h> #include <grp.h> | 已经有用户UID以及组ID，则 将uid和gid成员转换成对应字符串 |
   | struct tm *localtime(const time_t *timer);                   | #include <time.h>                                       | 处理最后修改时间戳                                       |

2. **标题**-- cjs

   

3. **标题**-- xkz

   ```c
   //d_type
   DT_BLK      // 块文件，对应整数值6
   DT_CHR      // 字符型，对应整数值2
   DT_DIR      // 目录，对应整数值4
   DT_FIFO     // 管道，对应整数值1
   DT_LNK      // 符号链接文件，对应整数值10
   DT_REG      // 普通文件，对应整数值8
   DT_SOCK     // 套接字，对应整数值12
   DT_UNKNOWN  // 未知类型文件，对应整数值0
   ```

   

4. **标题**-- zjl

   

5. **标题**-- glh

   ```c
   #include <my_header.h>
   
   void deepdir(char *pathname, int depth){
       DIR *dirp=opendir(pathname);
       ERROR_CHECK(dirp,NULL,"opendir failed.\n");
       struct dirent *dirent_p=NULL;
       while((dirent_p=readdir(dirp))!=NULL){
           if(strcmp(dirent_p->d_name, ".") == 0 || strcmp(dirent_p->d_name, "..") == 0){
               continue;
           }
           if(dirent_p->d_type==DT_DIR){
               for(int i=0;i<depth;i++){
                   printf("\t");
               }
               printf("├──");
               printf("%s\n",dirent_p->d_name);
               char new_path[256];
               sprintf(new_path, "%s/%s", pathname, dirent_p->d_name);
               deepdir(new_path,depth + 1);
           }
           else{
               for(int i=0;i<depth;i++){
                   printf("\t");
               }
               printf("├──");
               printf("%s\n",dirent_p->d_name);
   
           }
   
       }
       closedir(dirp);
   }
   /* Usage:  */
   int main(int argc, char *argv[]){
       deepdir(".",0);
       return 0;
   }
   ```

   

6. **标题**-- zzc

   

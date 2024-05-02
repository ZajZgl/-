# Concurrent Program

进程概念

> 进程是程序的活跃表现，它体现了程序在计算机中的执行状态；程序则是构成进程的基础，是被动的代码存储形态。
>
> 程序：存放在磁盘上的指令和数据的有序集合（文件），静态的
>
> 进程：执行一个程序所分配的资源的总称，进程是程序的一次执行过程，动态的，包括创建、调度、执行和消亡

## 进程内容：

- **代码段**：代码段通常是指用来存放程序执行代码的一块内存区域。这部分区域的大小在程序运行前就已经确定，在代码段中，也有可能包含一些只读的常数变量，例如字符串常量等。

- **数据段**：数据段通常是指用来存放程序中已初始化的全局变量的一块内存区域。

- **BSS段**：BSS段通常是指用来存放程序中未初始化的全局变量的一块内存区域。BSS是英文Block Started by Symbol的简称。

- **堆（heap）**：堆是用于存放进程运行中被动态分配的内存段，当进程调用<u>malloc等函数分配</u>内存时，新分配的内存就被动态添加到堆上（堆被扩张）；当利用<u>free等函数释放</u>内存时，被释放的内存从堆中被剔除（堆被缩减）

- **栈(stack)**：栈又称堆栈， 是用户存放程序临时创建的<u>局部变量</u>，（但<u>不包括static声明的变量</u>，static意味着在数据段中存放变量）。除此以外，在函数被调用时，其参数也会被压入发起调用的进程栈中，并且待到调用结束后，函数的返回值也会被存放回栈中。由于栈的先进后出特点，所以栈特别方便用来保存/恢复调用现场。从这个意义上讲，我们可以把堆栈看成一个寄存、交换临时数据的内存区

- **进程控制块：**用来记录进程属性，

  - 进程标识PID

  - 进程用户

  - 进程状态、优先级

  - 文件描述符（可储存1024个）

    ---

**进程类型：**

- 交互进程：在shell下启动。以在<u>前台</u>运行，也可以在<u>后台</u>运行
- 批处理进程：和在终端无关，被提交到一个作业队列中以便顺序执行
- 守护进程：和终端无关，一直在<u>后台</u>运行

**进程状态：**

- 运行态：进程正在运行，或者准备运行
- 可中断等待态：进程在等待一个事件的发生或某种系统资源
- 不可中断等待态：同上
- 停止态：进程被中止，收到信号后可继续运行
- 死亡态：已终止的进程，但pcb没有被释放

![image-20240424202921427](./11_Concurrent_Programming.assets/image-20240424202921427.png)

## 进程常用命令

Linux进程命令

### **进程信息**

- $ps   查看系统进程快照
- $top  查看进程动态信息
- $ls /proc 查看进程详细信息

> - ps 命令详细参数：
>   - -e：显示所有进程
>   - -l：长格式显示更加详细的信息
>   - -f 全部列出，通常和其他选项联用
>   - ==例.$ps -elf |grep a.out==
>     - 查看进程a.out
>     - grep作用为检索
> - top 命令详细参数：
>   - shift+ > 后翻页
>   - shift+ < 前翻页
>   - $top -p PID 查看某个进程 

![image-20240424204430884](./11_Concurrent_Programming.assets/image-20240424204430884.png)

**表头含义：**

- F 进程标志，说明进程的权限，常见的标志有两个:
  - 1：进程可以被复制，但是不能被执行；
  - 4：进程使用超级用户权限； 

- S 进程状态,常见的状态有以下几种：
  - -D：不可被唤醒的睡眠状态，通常用于 I/O 情况
  - -R：该进程正在运行
  - -S：该进程处于睡眠状态，可被唤醒
  -  -T：停止状态，可能是在后台暂停或进程处于除错状态
  - -W：内存交互状态（从 2.6 内核开始无效）
  - -X：死掉的进程（应该不会出现）
  - -Z：僵尸进程。进程已经中止，但是部分程序还在内存当中
  - -<：高优先级（以下状态在 BSD 格式中出现）
  - -N：低优先级
  - -L：被锁入内存
  - -s：包含子进程
  - -l：多线程（小写 L）
  - -+：位于后台     

- UID: 运行此进程的用户的 ID
- PID: 进程的 ID
- PPID: 父进程的 ID
- C: 该进程的 CPU 使用率，单位是百分比
- PRI: 进程的优先级，数值越小，该进程的优先级越高，越早被 CPU 执行
- NI:   进程的优先级，数值越小，该进程越早被执行；  
- ADDR:   该进程在内存的哪个位置；  
- SZ: 该进程占用多大内存；
- WCHAN: 该进程是否运行。"-"代表正在运行；
- TTY: 该进程由哪个终端产生；
- TIME: 该进程占用 CPU 的运算时间，注意不是系统时间；
- CMD: 产生此进程的命令名；

### 进程优先级

- $nice  按用户指定的优先级运行进程
  - $nice -n [NI值] [命令]
  - NI 范围是 -20~19。数值越大优先级越低
  - 普通用户调整 NI 值的范围是 0~19，而且只能调整自己的进程。
  - 普通用户只能调高 NI 值，而不能降低。如原本 NI 值为 0，则只能调整为大于 0。
  - 只有 root 用户才能设定进程 NI 值为负值，而且可以调整任何用户的进程。

- $renice  改变正在运行进程的优先级
  -  $renice [优先级] [PID]

### 后台进程

- $jobs  查看后台进程
- $bg [进程序号]  将挂起的进程在后台运行
- $fg [进程序号]  把后台运行的进程放到前台运行
- ctrl+Z 把刚运行的程序转到后台运行
- ./a.out& 把a.out程序后台运行

## 子进程

子进程概念：子进程为由另外一个进程（对应称之为父进程）所创建的进程

### 创建子进程

> **子进程创建-fork**
> \#include <unistd.h>
> pid_t fork(void);
>
> - 创建新的进程，失败时返回-1
> - 成功时父进程(执行fork的进程)返回子进程的进程号，子进程（通过fork产生的进程）中为0
> - 子进程会执行父进程中fork下面的程序
> - 通过fork的返回值区分父进程和子进程

> **示例1**-fork
> 在父进程中打印"pid = 进程号"，在子进程中打印"pid = 0"
>
> ```c
> #include <stdio.h>
> #include <unistd.h>
> int main(int argc, const char *argv[])
> {
> 	pid_t pid;
> 	pid = fork();
> 	printf("pid = %d\n",pid);
> 	return 0;
> }
> ```

> **示例2**
>
> fork的一般用法
>
> ```c
> #include <stdio.h>
> #include <unistd.h>
> int main(int argc, const char *argv[])
> {
> 	pid_t pid;
> 	pid = fork();
> 	printf("pid = %d\n",pid);
> 	
> 	if(pid > 0)//父进程
> 	{
> 		printf("Father\n");
> 	}
> 	else if(pid == 0)//子进程
> 	{
> 		printf("Child\n");
> 	}
> 	else//出错
> 	{
> 		perror("fork\n");
> 		return 0;
> 	}
> 	return 0;
> }
> ```
>
> **注意：**
>
> - 子进程只执行fork之后的代码
> - 父子进程执行顺序是操作系统决定的
> - 子进程继承了父进程的内容
> - 父子进程有独立的地址空间，互不影响
> - 若父进程先结束，子进程成为孤儿进程，被init进程收养，子进程变成后台进程
> - 若子进程先结束，父进程如果没有及时*回收*，子进程变成僵尸进程

> **示例3**
>
> ```c
> #include <stdio.h>
> #include <unistd.h>
> int main(int argc, const char *argv[])
> {
> 	pid_t pid;
> 	int i;
> 	for(i=0; i<3; i++)
> 	{
> 		pid = fork();
> 		if(pid < 0)
> 		{
> 			perror("fork");
> 			return 0;
> 		}
> 		else if(pid == 0)
> 	 	{
> 			printf("%d Child %d\n",i,pid);
> 			sleep(5);
> 		}
> 		else if(pid > 0)
> 		{
> 			printf("%d Father%d\n",i,pid);
> 			sleep(5);
> 		}
> 	}
> 	return 0;
> }
> ```
>
> 生成3个子进程3个孙进程1个曾孙进程
>
> - 子进程：2、3、5 （1 生的）
> - 孙进程：4、6、7  （2、3、5 生的）
> - 曾孙进程：8   （4、6、7 生的）
>
> ![image-20240425213057965](./11_Concurrent_Programming.assets/image-20240425213057965.png)

### 结束进程

- 进程结束 – exit/_exit
  - 头文件
    -  \#include <stdlib.h> 
    -  \#include <unistd.h>
  -  void exit(int status);
    - 结束当前的进程并将status返回
    - exit结束进程时会刷新(流)缓冲区
  -  void _exit(int status);
    - 不刷新(流)缓冲区

**注意：**return 和 exit 的区别
		main函数结束时会隐式地调用exit函数，普通函数return是返回上一级。

示例：

```c
#include <stdio.h>
#include <stdlib.h>
int main(void) 
{
    printf(“before exit”);
    exit(0);
    printf(“after exit”);
}
```

编译运行后只打印“before exit”

### 回收子进程

> 子进程结束时由父进程回收
>
> 孤儿进程由init进程回收
>
> 若没有及时回收会出现僵尸进程

- 进程回收 – wait
  - \#include <sys/wait.h>
  -  pid_t wait(int *status); 
  -  成功时返回回收的子进程的进程号；失败时返回EOF
  -  若子进程没有结束，父进程一直阻塞
  -  若有多个子进程，哪个先结束就先回收
  -  status 指定保存子进程返回值和结束方式的地址
  -  status为NULL表示直接释放子进程PCB,不接收返回值

**读取status参数**

> 子进程通过exit / _exit / return 返回某个值(0-255)
>
> 父进程调用wait(&status) 回收
>
> - 通过调用宏来将返回值解读
>   - WIFEXITED(status)  判断子进程是否正常结束
>   - WEXITSTATUS(status)  获取子进程返回值
>   - WIFSIGNALED(status)  判断子进程是否被信号结束
>   - WTERMSIG(status)  获取结束子进程的信号类型

示例：

```c
#include <stdio.h>
#include <sys/wait.h>
#include <unistd.h>
#include <stdlib.h>

int main(int argc, char** argv)
{
   pid_t pid;
   pid_t rpid;
   pid = fork();
   int status;
   if(pid<0)//出错
   {
      perror("fork");
      return 0;
   }
   else if(pid == 0)//子进程
   {
       sleep(2);
       printf("child 2 \n");
       exit(2);
   }
   else if(pid >0)//父进程
   {
       rpid = wait(&status);
       printf("Get child status=%d\n",WEXITSTATUS(status));
   }
}
```

- 进程回收-waitpid
  - \#include <unistd.h>
  -  pid_t waitpid(pid_t pid, int *status, int option);
- 参数：
  - pid
    - pid>0时，只等待进程ID等于pid的子进程，不管其它已经有多少子进程运行结束退出了，只要指定的子进程还没有结束，waitpid就会一直等下去。
    - pid=-1时，等待任何一个子进程退出，没有任何限制，此时waitpid和wait的作用一模一样。
    - pid=0时，等待同一个进程组中的任何子进程，如果子进程已经加入了别的进程组，waitpid不会对它做任何理睬。
    - pid<-1时，等待一个指定进程组中的任何子进程，这个进程组的ID等于pid的绝对值。
  - options 
    - options提供了一些额外的选项来控制waitpid，目前在Linux中只支持WNOHANG和WUNTRACED两个选项，这是两个常数，可以用"|"运算符把它们连接起来使用
    - WNOHANG ：若由pid指定的子进程未发生状态改变(没有结束)，则waitpid()不阻塞，立即返回0
    - WUNTRACED： 返回终止子进程信息和因信号停止的子进程信息
-  wait(wait_stat) 等价于waitpid(-1,wait_stat,0) 

示例

```c
#include <stdio.h>
#include <sys/wait.h>
#include <unistd.h>
#include <stdlib.h>

int main(int argc, char** argv)
{
   pid_t pid;
   pid_t rpid;
   pid = fork();
   int status;
   if(pid<0)//出错
   {
      perror("fork");
      return 0;
   }
   else if(pid == 0)//子进程
   {
       sleep(10);
       printf("child 2 \n");
       exit(2);
   }
   else if(pid >0)//父进程
   {
       waitpid(pid,&status,0);
       printf("Get child status=%d\n",WEXITSTATUS(status));
   }
}
```

> ### 示例
>
> 创建一个进程链，父进程->子进程->孙进程->重孙进程->重重孙进程
>
> ```c
> #include <stdio.h>
> #include <unistd.h>
> 
> int main(int argc, const char *argv[])
> {
> 	pid_t pid;
> 	int i;
> 
> 	for(i=0; i<5; i++)
> 	{
> 		pid=fork();
> 		if(pid < 0)
> 		{
> 			perror("fork");
> 			return 0;
> 		}
> 		if(pid > 0)
> 		{
> 			printf("father %d\n",i);
> 			break;
> 		}
> 		if(pid == 0)
> 		{
> 			printf("child %d\n",i);
> 		}
> 	}
> 	sleep(20);
> 	wait(0);
> 	return 0;
> }
> ```
>
> 通过ps指令查看
>
> ![266f7c4c09102b8dc83941b477f8ec00](./11_Concurrent_Programming.assets/266f7c4c09102b8dc83941b477f8ec00.png)

## exec函数族

- 进程调用exec函数族执行某个程序
- 进程当前内容被指定的程序替换
- 实现让父子进程执行不同的程序
  - 父进程创建子进程
  - 子进程调用exec函数族
  - 父进程不受影响

### execl / execlp

-  \#include <unistd.h>

-  int execl(const char *path, const char *arg, …);
-  int execlp(const char *file, const char *arg, …);
  - 成功时执行指定的程序；失败时返回EOF
  - path  执行的程序名称，包含路径
  - arg… 传递给执行的程序的参数列表
  - file  执行的程序的名称，在PATH中查找

注意：

1. execlp不需要写文件名全路径
2. 在PATH查找最后一个参数必须用空指针(NULL)作结束
3. ==进程当前内容被指定的程序替换==，但进程号不变
4. ==第0个参数必须要写，虽然它没有使用==

> 示例1：
>
> ```c
> #include <stdio.h>
> #include <unistd.h>
> int main(int argc, const char *argv[])
> {
> 	if(execl("/bin/ls","ls","-a","-l","./",NULL)<0)
> 	{
> 		perror("execl");
> 	}
> 	return 0;
> }
> ```
>
> 编译运行a.out 和 shell命令$ls -a -l 做对比
>
> ![image-20240502141436304](./11_Concurrent_Programming.assets/image-20240502141436304.png)
>
> 运行效果一致

> 示例2：
>
> ```c
> #include <stdio.h>
> #include <unistd.h>
> int main(int argc, const char *argv[])
> {
> 	if(execlp("ls","-a","-l","./",NULL)<0)
> 	{
> 		perror("execl");
> 	}
> 	return 0;
> }
> ```
>
> 运行效果同示例1

### execv / execvp

-  \#include <unistd.h>
-  int execv(const char *path, char *const argv[]);
-  int execvp(const char *file, char *const argv[]);
  - 成功时执行指定的程序；失败时返回EOF
  - **arg… 封装成指针数组的形式**
  - path  执行的程序名称，包含路径
  - file  执行的程序的名称，在PATH中查找

> 示例
>
> ```c
> #include <stdio.h>
> #include <unistd.h>
> int main(int argc, const char *argv[])
> {
>     char *arg[] = {“ls”, “-a”, “-l”, “/etc”, NULL};
>     if  (execv(“/bin/ls”, arg) < 0) {
>      perror(“execv”);
>     }  
>     if  (execvp(“ls”, arg) < 0) {
>      perror(“execvp”);
>     }  
> 	return 0;
> }
> ```
>
> 运行效果同上

### system

-  \#include <stdlib.h>
-  int system(const char *command);
  - 成功时返回命令command的返回值；失败时返回EOF
  - 当前进程等待command执行结束后才继续执行

示例

```c
#include <studio.h>
#include <stdlib.h>
int main()
{
    system("ls -a -l ./");
}
```

## 守护进程

**概念：**

守护进程又叫精灵进程（Daemon Process），它是一个生存期较长的进程，通常独立于控制终端并且周期性地执行某种任务或等待处理某些发生的事件。

**特点：**

始终在后台运行，独立于任何终端，周期性的执行某种任务或等待处理特定事件。

它是个特殊的孤儿进程，这种进程脱离终端，为什么要脱离终端呢？之所以脱离于终端是为了避免进程被任何终端所产生的信息所打断，其在执行过程中的信息也不在任何终端上显示。由于在 Linux 中，每一个系统与用户进行交流的界面称为终端，每一个从此终端开始运行的进程都会依附于这个终端，这个终端就称为这些进程的控制终端，当控制终端被关闭时，相应的进程都会自动关闭

**举例：**

http 服务的守护进程叫 httpd，mysql 服务的守护进程叫 mysqld。

**相关名词：**

- 进程组（Process Group）： 进程集合，每个进程组有一个组长（Leader），其进程 ID 就是该进程组 ID。
- 会话（Session）： 进程组集合，每个会话有一个组长，其进程 ID 就是该会话组 ID。
- 控制终端（Controlling Terminal）：每个会话可以有一个单独的控制终端，与控制终端连接的 Leader 就是控制进程（Controlling Process）。

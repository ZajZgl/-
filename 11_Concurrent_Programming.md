# 11_Concurrent_Programing

# 1.进程概念

在Linux中，进程是**操作系统分配资源和调度运行的基本单位**。

Linux中的进程有以下用处：

1. **提高CPU利用率**：通过进程的并发执行，可以让多个程序同时利用计算机的资源，这样每个用户都可以感觉自己独占CPU，从而提高了CPU的效率。
2. **缩短响应时间**：多进程环境下，用户可以启动多个任务而不互相干扰，系统能够快速响应用户的指令，从而缩短系统的响应时间。
3. **资源分配单位**：进程是操作系统中最小的资源分配单位，它包含了程序执行过程中所需的所有资源，如地址空间、文件描述符等。
4. **提供抽象和管理**：对于操作系统来说，进程是一种抽象，它允许操作系统通过管理进程来提高整个系统的资源利用率和效率。
5. **进程间通信**：进程可以通过系统调用或信号来进行通信和同步，例如创建子进程、共享内存、管道、消息队列等方式。
6. **状态转换**：进程的状态包括运行态、就绪态、阻塞态等，这些状态的转换由操作系统内核进行管理和调度，保证了系统的稳定运行。
7. **实现多任务**：在Linux系统中，进程使得多任务成为可能。用户可以根据需要运行多个程序，而这些程序可以独立地执行，互不干扰。

进程在Linux系统中扮演着至关重要的角色，它们不仅提高了系统的资源利用率，还实现了多任务的并行处理，使得用户可以更加高效地进行工作。

以下是一些关于Linux进程的重要概念：

1. **进程定义**：进程是一个程序的一次执行实例，它拥有一段可执行的程序代码、专用的系统堆栈空间、独立的存储空间以及一个进程控制块（PCB），在Linux中具体实现为`task_struct`结构。
2. **进程要素**：每个进程都需要有一段程序供其运行，这是进程存在的前提。此外，进程还需要有自己的独立存储空间，用于存放变量和数据等。进程控制块则包含了进程的状态、优先级、资源使用情况等信息。
3. **多道程序设计**：Linux是一个多道程序设计系统，这意味着系统中可以有多个彼此独立的进程同时运行。Linux允许进程在运行时创建额外的线程，每个进程都会有一个自己的程序计数器，用来记录下一个需要被执行的指令。
4. **守护进程**：在Linux系统中，即使用户退出登录，仍然会有一些后台进程在运行，这些进程被称为守护进程（daemon）。它们通常负责系统服务或维护任务，如打印服务、系统日志服务等。
5. **进程管理**：为了有效地管理和追踪所有运行着的进程，操作系统提供了一系列的工具和命令。用户可以查看所有运行中的进程、查看进程消耗的资源、定位个别进程并对其执行操作，如改变进程的优先级、杀死指定进程、限制进程可用的系统资源等。
6. **命令工具**：Linux提供了许多命令来帮助用户高效地掌控进程管理，例如`ps`命令用于查看进程状态，`top`命令用于动态监控进程资源占用情况，`kill`命令用于终止指定进程等

## 进程内容

进程的内容通常包括以下几个主要部分：

1. **程序代码**：这是定义进程行为的机器语言指令或高级语言语句的集合。
2. **数据**：进程操作的数据，可能包括变量、常量、数据结构等，它们在进程的地址空间内被存储和管理。
3. **堆栈**：用于维护函数调用和局部变量的存储区域。堆用于动态内存分配，栈用于执行函数调用和返回以及局部变量的存储。
4. **文件描述符**：每个进程都有自己的文件描述符集合，它们是访问文件、管道和网络套接字等资源的引用。
5. **环境变量**：进程运行时环境中的一些参数设置，比如PATH、HOME等，它们影响进程的行为和配置。
6. **信号处理程序**：用于处理接收到的信号的程序，例如中断信号、终止信号等。
7. **寄存器值**：记录了进程最后执行的指令位置等信息的硬件寄存器内容。
8. **进程控制块（PCB）**：操作系统用来表示和管理进程的一个数据结构
   1. 进程标识PID
   2. 进程用户
   3. 进程状态、优先级
   4. 文件描述符（可储存1024个）
9. **用户ID和组ID**：标识运行该进程的用户和用户组的信息，决定了进程的权限和访问控制。
10. **资源分配情况**：包括内存分配、CPU时间片分配和其他资源的占用情况。
11. **上下文信息**：包含进程的执行环境，比如CPU寄存器的内容、程序计数器、堆栈指针等，以便在调度时能够正确地恢复执行。
12. **线程信息**：如果进程支持多线程，还会包含每个线程的相关信息和线程间的同步机制。
13. 这些内容共同组成了进程的全部信息，操作系统通过管理这些信息来调度和管理各个进程，确保系统资源的合理利用和系统的稳定运行。

## 进程类型

1. **交互进程**：在shell下启动。以在<u>前台</u>运行，也可以在<u>后台</u>运行
2. 批处理进程：和在终端无关，被提交到一个作业队列中以便顺序执行
3. **守护进程**：和终端无关，一直在<u>后台</u>运行

## 进程状态

1. 运行态：进程正在运行，或者准备运行
2. 可中断等待态：进程在等待一个事件的发生或某种系统资源
3. 不可中断等待态：同上
4. 停止态：进程被中止，收到信号后可继续运行
5. 死亡态：已终止的进程，但pcb没有被释放

![image-20240424202921427](./11_Concurrent_Programming.assets/image-20240424202921427.png)

# 2.进程常用命令

## 进程信息命令

1. `ps`   查看系统进程快照
2. `top`  查看进程动态信息
3. `pstree` 查看进程树，显示进程的层次结构

### top 命令

>  1. **格式**：`top [options]`
>  2. **功能**：实时显示系统中各个进程的资源占用情况，包括CPU、内存等
>  3. **参数**：
>     -  `options`(可选参数)
>        -  `-d`：设置刷新间隔时间。
>        -  `-p`：监控指定进程ID的进程。
>        -  `-u`：监控指定用户的进程。
>        -  `-n`：设置刷新次数。
>        -  `-b`：以批处理模式运行。
>  4. **示例**：
>     -  shift+ > 后翻页
>     -  shift+ < 前翻页
>     -  `top -p 1234`监控指定进程ID为1234的进程
>     -  `top -u root`监控指定用户为root的进程

### 进程信息表

>  ![image-20240424204430884](./11_Concurrent_Programming.assets/image-20240424204430884.png)
>
>  **表头含义：**
>
>  - F 进程标志，说明进程的权限，常见的标志有两个:
>     - 1：进程可以被复制，但是不能被执行；
>     - 4：进程使用超级用户权限； 
>
>  - S 进程状态,常见的状态有以下几种：
>     - -D：不可被唤醒的睡眠状态，通常用于 I/O 情况
>     - -R：该进程正在运行
>     - -S：该进程处于睡眠状态，可被唤醒
>     - -T：停止状态，可能是在后台暂停或进程处于除错状态
>     - -W：内存交互状态（从 2.6 内核开始无效）
>     - -X：死掉的进程（应该不会出现）
>     - -Z：僵尸进程。进程已经中止，但是部分程序还在内存当中
>     - -<：高优先级（以下状态在 BSD 格式中出现）
>     - -N：低优先级
>     - -L：被锁入内存
>     - -s：包含子进程
>     - -l：多线程（小写 L）
>     - -+：位于后台     
>
>  - UID: 运行此进程的用户的 ID
>  - PID: 进程的 ID
>  - PPID: 父进程的 ID
>  - C: 该进程的 CPU 使用率，单位是百分比
>  - PRI: 进程的优先级，数值越小，该进程的优先级越高，越早被 CPU 执行
>  - NI:   进程的优先级，数值越小，该进程越早被执行；  
>  - ADDR:   该进程在内存的哪个位置；  
>  - SZ: 该进程占用多大内存；
>  - WCHAN: 该进程是否运行。"-"代表正在运行；
>  - TTY: 该进程由哪个终端产生；
>  - TIME: 该进程占用 CPU 的运算时间，注意不是系统时间；
>  - CMD: 产生此进程的命令名；

## 进程优先级命令

1. `nice` 启动进程时调整进程的优先级
2. `renice` 修改已经运行的进程的优先级

### nice 命令

>  1. **格式**：`nice [options] [command]`
>  2. **功能**：==启动进程时==调整进程的优先级，使得CPU资源分配更加合理
>  3. **参数**：
>     -  `options`（可选参数）
>        -  `-n`：设置进程的优先级，范围为-20（最高优先级）到19（最低优先级），默认值为0。
>        -  `--adjust=N`：调整当前进程的优先级，N的取值范围与-n相同。
>     -  `command`:命令，如`ls`、`cd`等
>  4. **示例**：
>     -  `nice command`以默认优先级运行命令
>     -  `nice -n -20 ls`将进程优先级设置为最高（-20）
>

### renice 命令

>  1. **格式**：`renice [options] [NI值] -p [ID]`
>  2. **功能**：修改已经运行的进程的优先级
>  3. **参数**：
>     -  `-n` ：设置进程的优先级，范围为-20（最高优先级）到19（最低优先级），默认值为0。
>     -  `-p`：指定要修改优先级的进程ID。
>     -  `-g`：指定要修改优先级的进程组ID。
>     -  `-u` ：指定要修改优先级的用户ID。
>  4. 注意：
>     -  NI 范围是 -20~19。数值越大优先级越低
>     -  普通用户调整 NI 值的范围是 0~19，而且只能调整自己的进程。
>     -  普通用户只能调高 NI 值，而不能降低。如原本 NI 值为 0，则只能调整为大于 0。
>     -  只有 root 用户才能设定进程 NI 值为负值，而且可以调整任何用户的进程。
>  5. **示例**：
>     -  `sudo renice -n -20 -p 1234`将进程ID为1234的进程优先级设置为最高（-20）
>     -  `sudo renice +5 -p 1234`将进程ID为1234的进程优先级增加5
>

## 后台进程命令

1. `jobs`  查看后台进程
2. `bg [进程号]` 将挂起的进程在后台运行
3. `fg [进程号]` 把后台运行的进程放到前台运行
4. ctrl+z 把刚运行的程序转到后台运行
5. **`&`**: 在命令后面加上 `&` 符号可以将该命令放到后台执行

# 3.子进程

在Linux中，子进程是由父进程创建的进程。当一个进程被创建时，它会自动成为一个新进程的父进程，而新进程则成为子进程。子进程可以通过`fork()`系统调用来创建。

1. **创建子进程的意义**：子进程常用于实现多任务并行处理，提高程序的执行效率。它们可以分担父进程的工作负载，或者执行不同的任务。
2. **父子进程区别**：虽然子进程是父进程的一个拷贝，但它们有不同的内存地址空间。子进程获得与父进程相同的数据和属性的副本，但是有自己的数据段和堆栈段。

## 创建子进程

### fork 函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     pid_t fork(void);
>     ```
>
>  2. **功能**：创建一个新进程，新进程是当前进程的一个副本。新进程从父进程处继承了代码、数据、堆栈等资源，但是它们在不同的内存空间中运行。
>
>  3. **参数**：无
>
>  4. **返回值**：
>
>     -  成功，创建新进程，则在父进程中返回新进程的进程ID（大于0），在子进程中返回0。
>     -  失败，返回-1。
>
>  5. **示例**1：在父进程中打印"pid = 进程号"，在子进程中打印"pid = 0"
>
>     ```c
>     #include <stdio.h>
>     #include <unistd.h>
>     int main(int argc, const char *argv[])
>     {
>     	pid_t pid;
>     	pid = fork();
>     	printf("pid = %d\n",pid);
>     	return 0;
>     }
>     ```
>
>     **示例**2：`fork`的一般用法
>
>     ```c
>     #include <stdio.h>
>     #include <unistd.h>
>     int main(int argc, const char *argv[])
>     {
>     	pid_t pid;
>     	pid = fork();
>     	printf("pid = %d\n",pid);
>     
>     	if(pid > 0)//父进程
>     	{
>     		printf("Father\n");
>     	}
>     	else if(pid == 0)//子进程
>     	{
>     		printf("Child\n");
>     	}
>     	else//出错
>     	{
>     		perror("fork\n");
>     		return 0;
>     	}
>     	return 0;
>     }
>     ```
>
>     **示例**3：使用for循环生成多个子进程
>
>     ```c
>     #include <stdio.h>
>     #include <unistd.h>
>     int main(int argc, const char *argv[])
>     {
>     	pid_t pid;
>     	int i;
>     	for(i=0; i<3; i++)
>     	{
>     		pid = fork();
>     		if(pid < 0)
>     		{
>     			perror("fork");
>     			return 0;
>     		}
>     		else if(pid == 0)
>     	 	{
>     			printf("%d Child %d\n",i,pid);
>     			sleep(5);
>     		}
>     		else if(pid > 0)
>     		{
>     			printf("%d Father%d\n",i,pid);
>     			sleep(5);
>     		}
>     	}
>     	return 0;
>     }
>     ```
>
>     示例3**分析**：
>
>     *生成3个子进程3个孙进程1个曾孙进程*
>
>     - 子进程：2、3、5 （1 生的）
>     - 孙进程：4、6、7  （2、3、5 生的）
>     - 曾孙进程：8   （4、6、7 生的）
>
>     ![image-20240425213057965](./11_Concurrent_Programming.assets/image-20240425213057965.png)
>
>  6. **注意**：
>
>     1. 子进程只执行fork之后的代码
>     2. 父子进程执行顺序是操作系统决定的
>     3. 子进程继承了父进程的内容
>     4. 父子进程有独立的地址空间，互不影响
>     5. 若父进程先结束，子进程成为孤儿进程，被init进程收养，子进程变成后台进程
>     6. 若子进程先结束，父进程如果没有及时回收，子进程变成僵尸进程

## 结束进程

### exit 函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     void exit(int status);
>     ```
>
>  2. **功能**：终止当前进程，刷新(流)缓冲区并将退出状态码`status`传递给操作系统。这个状态码可以被其他进程或父进程获取，以了解子进程的结束状态
>
>  3. **参数**：`status`是一个整数，表示进程的退出状态码
>
>  4. **返回值**：无

### _exit 函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     void _exit(int status);
>     ```
>
>  2. **功能**：与`exit`函数类似，但`_exit`函数不会刷新(流)缓冲区，它直接终止进程，并将退出状态码传递给操作系统。
>
>  3. **参数**：`status`是一个整数，表示进程的退出状态码
>
>  4. **返回值**：无
>
>  ​	**return 和 exit 的区别：**
>  ​	main函数结束时会隐式地调用exit函数，普通函数return是返回上一级。
>
>  5.  示例：
>
>      ```c
>      #include <stdio.h>
>      #include <stdlib.h>
>      int main(void) 
>      {
>          printf(“before exit”);
>          exit(0);
>          printf(“after exit”);
>      }
>      ```
>
>      编译运行后只打印“before exit”

## 回收子进程

回收子进程的必要性：

1. **避免资源浪费**：当一个子进程结束时，它能够释放自己用户区的资源，但无法释放内核空间的资源，如进程控制块（PCB）。如果父进程不回收这些资源，它们将一直占用内存，导致系统资源的浪费。
2. **获取退出信息**：父进程通过回收子进程可以获取到子进程的退出状态，如退出码和执行时间等信息。这对于父进程监控子进程的行为和进行后续处理是非常重要的。
3. **防止僵尸进程**：如果父进程没有及时回收子进程，那么子进程虽然已经结束，但其PCB仍然保留在系统中，这样的进程称为僵尸进程。僵尸进程不执行任何操作，但占用系统资源，如果大量僵尸进程存在，会影响系统性能。
4. **管理孤儿进程**：如果子进程的父进程先于子进程结束，子进程将成为孤儿进程。为了避免孤儿进程无人管理，系统会让init进程（进程号为1的进程）收养它们。init进程会负责回收这些孤儿进程的资源。

回收子进程不仅是为了维护操作系统资源的有效性，也是为了保证程序能够正确获取子进程的执行结果，以及防止产生僵尸进程和妥善管理孤儿进程，从而保证系统的稳定性和程序的可靠性。

>  ### wait 函数
>
>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     pid_t wait(int *status);
>     ```
>
>  2. **功能**：挂起父进程的执行，直到一个子进程结束。一旦有子进程结束，`wait()`函数将返回该子进程的PID，并回收其资源。如果传入了`status`参数，则`wait()`函数还会将子进程的退出状态码写入到status指向的变量中。
>
>  3. **参数**：`status`是一个整数指针，用于存储子进程的退出状态码。
>
>  4. **返回值**：
>
>     -  成功,返回已结束的子进程的PID；
>     -  失败,返回-1，并设置errno为相应的错误码
>
>  5. **注**：
>
>     -  若子进程没有结束，父进程一直阻塞
>     -  若有多个子进程，哪个先结束就先回收
>
>  6. **示例**：
>
>     ```c
>     #include <stdio.h>
>     #include <sys/wait.h>
>     #include <unistd.h>
>     #include <stdlib.h>
>                          
>     int main(int argc, char** argv)
>     {
>        pid_t pid;
>        pid_t rpid;
>        pid = fork();
>        int status;
>        if(pid<0)//出错
>        {
>           perror("fork");
>           return 0;
>        }
>        else if(pid == 0)//子进程
>        {
>            sleep(2);
>            printf("child 2 \n");
>            exit(2);
>        }
>        else if(pid >0)//父进程
>        {
>            rpid = wait(&status);
>            printf("Get child status=%d\n",WEXITSTATUS(status));
>        }
>     }
>     ```
>
>     

### waitpid 函数

>  1. **原型**：
>
>     ```c
>     #include <sys/types.h>
>     #include <sys/wait.h>
>     pid_t waitpid(pid_t pid, int *status, int options);
>     ```
>
>  2. **功能**：等待指定子进程的状态改变。当子进程结束时，该函数会返回子进程的进程ID，并将子进程的退出状态存储在`status`指向的变量中
>
>  3. **参数**：
>
>     -  `pid`：指定要等待的子进程的进程ID。可以是以下值的组合：
>        -  `-1`：等待任意子进程。
>        -  `0`：等待与调用进程属于同一进程组的任何子进程。
>        -  `>0`：等待指定的子进程。
>     -  `status`：指向一个整数变量的指针，用于存储子进程的退出状态。如果不关心退出状态，可以设置为`NULL`。
>     -  `options`：指定额外的选项，可以是以下值的组合：
>        -  `WNOHANG`：如果没有子进程状态改变，立即返回，不阻塞。
>        -  `WUNTRACED`：如果子进程进入暂停状态，也视为状态改变。
>
>  4. **返回值**：
>
>     -  成功时，返回已经停止的子进程的进程ID。
>     -  如果没有任何子进程状态改变，根据`options`参数的设置，可能返回0或-1。
>     -  失败时，返回-1，并设置errno。
>
>  5. **示例**：
>
>     ```c
>     #include <stdio.h>
>     #include <sys/wait.h>
>     #include <unistd.h>
>     #include <stdlib.h>
>                          
>     int main(int argc, char** argv)
>     {
>        pid_t pid;
>        pid_t rpid;
>        pid = fork();
>        int status;
>        if(pid<0)//出错
>        {
>           perror("fork");
>           return 0;
>        }
>        else if(pid == 0)//子进程
>        {
>            sleep(10);
>            printf("child 2 \n");
>            exit(2);
>        }
>        else if(pid >0)//父进程
>        {
>            waitpid(pid,&status,0);
>            printf("Get child status=%d\n",WEXITSTATUS(status));
>        }
>     }
>     ```
>
>     

### 读取status参数

当使用`wait()`或`waitpid()`函数等待子进程结束时，可以通过传入的`int *status`指针来获取子进程的退出状态。这个状态包含两个主要部分：退出代码和信号编号（如果适用）。

通过以下宏来获取`status`的信息

- `WIFEXITED(status)`  判断子进程是否正常结束
- `WEXITSTATUS(status)`  获取子进程返回值
- `WIFSIGNALED(status)`  判断子进程是否被信号结束
- `WTERMSIG(status)`  获取结束子进程的信号类型

## 示例-创建进程链

>  创建一个进程链，父进程->子进程->孙进程->重孙进程->重重孙进程
>
>  ```c
>  #include <stdio.h>
>  #include <unistd.h>
>  
>  int main(int argc, const char *argv[])
>  {
>  	pid_t pid;
>  	int i;
>  
>  	for(i=0; i<5; i++)
>  	{
>  		pid=fork();
>  		if(pid < 0)
>  		{
>  			perror("fork");
>  			return 0;
>  		}
>  		if(pid > 0)
>  		{
>  			printf("father %d\n",i);
>  			break;
>  		}
>  		if(pid == 0)
>  		{
>  			printf("child %d\n",i);
>  		}
>  	}
>  	sleep(20);
>  	wait(0);
>  	return 0;
>  }
>  ```
>
>  通过`ps -elf | grep a.out `指令查看
>
>  ![266f7c4c09102b8dc83941b477f8ec00](./11_Concurrent_Programming.assets/266f7c4c09102b8dc83941b477f8ec00.png)

# 4.exec函数族

exec函数族是一组用于**在进程中启动另一个程序来替换当前进程的函数**。

exec函数族主要用于在当前进程内部执行一个新的程序，而不会创建新的进程。

-  子进程调用exec函数，族父进程不受影响。
-  进程当前内容被指定的程序替换，但进程号不变

exec函数族中的一些主要成员：

1. `execl`：这是exec函数族中最简单的一个，它接受一个可执行文件路径和一个参数列表，列表以NULL结尾。这个函数将新程序的参数一一对应地传递给新程序。
2. `execv`：与`execl`类似，但是参数是通过一个指针数组传递的，而不是直接列出。
3. `execlp` 和 `execvp`：这两个函数会搜索`PATH`环境变量来找到可执行文件，而不需要提供完整的路径名。

## execl 函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     int execl(const char *path, const char *arg, ...);
>     ```
>
>  2. **功能**：`execl`函数用来执行一个文件，并将控制权转交给这个新程序。当调用成功时，原有进程的内容（代码段、数据段和堆栈等）将被新程序的内容取代，`execl`函数不会返回。
>
>  3. **参数**：
>
>     -  `path`：要执行的程序的路径。
>     -  `arg`：传递给新程序的参数列表，最后一个参数需要是NULL，以标识参数列表的结束。
>
>  4. **返回值**：
>
>     -  如果调用成功，`execl`函数不会返回。
>     -  如果调用失败（例如找不到指定的文件或没有足够的内存等），则返回-1，并设置`errno`来指示错误类型。
>
>  5. **注**：`execl`函数后面的字母“l”代表的是“list”，意味着该函数通过参数列表的方式来传递参数
>
>  6. **示例**:
>
>     使用`execl`函数来执行`ls`命令
>
>     >  ```c
>     >  #include <stdio.h>
>     >  #include <unistd.h>
>     >  int main(int argc, const char *argv[])
>     >  {
>     >  	if(execl("/bin/ls","ls","-a","-l","./",NULL)<0)
>     >  	{
>     >  		perror("execl");
>     >  	}
>     >  	return 0;
>     >  }
>     >  ```
>     >
>     >  编译运行a.out 和 shell命令`$ls -a -l` 做对比
>     >
>     >  ![image-20240502141436304](./11_Concurrent_Programming.assets/image-20240502141436304.png)
>     >
>     >  运行效果一致

## execlp 函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     int execlp(const char *file, const char *arg, ...);
>     ```
>
>  2. **功能**：`execlp`函数用于在当前进程中执行指定的可执行文件，并用该新程序替换当前进程的映像。
>
>  3. **参数**：
>
>     -  `file`：要执行的程序的文件名（或路径），不需要带扩展名，因为系统会自动根据文件名查找可执行文件。
>     -  `arg`：传递给新程序的参数列表，最后一个参数需要是NULL，以标识参数列表的结束。
>     -  `...`：可选参数，可以传递多个，每个参数都会按顺序传递给新程序。
>
>  4. **返回值**：
>
>     -  如果调用成功，`execlp`函数不会返回。
>     -  如果调用失败，则返回-1，并设置`errno`来指示错误类型。

## execv 函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     int execv(const char *path, char *const argv[]);
>     ```
>
>  2. **功能**：`execv`函数用来执行一个文件，并将控制权转交给这个新程序。与`execl`相似，当调用成功时，原有进程的内容（代码段、数据段和堆栈等）将被新程序的内容取代，`execv`函数不会返回。
>
>  3. **参数**：
>
>     -  `path`：要执行的程序的路径。
>     -  `argv`：传递给新程序的参数数组，其中`argv[0]`通常是被执行文件的路径，数组的最后一个元素应该是NULL，以标识参数列表的结束。
>
>  4. **返回值**：
>
>     -  如果调用成功，函数不会返回。
>     -  如果调用失败，则返回-1，并设置`errno`来指示错误类型。

## execvp 函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     int execvp(const char *file, char *const argv[]);
>     ```
>
>  2. **功能**：`execvp`函数用于在当前进程中执行指定的可执行文件，并用该新程序替换当前进程的映像。
>
>  3. **参数**：
>
>     -  `file`：要执行的程序的文件名（或路径），不需要带扩展名，因为系统会自动根据文件名查找可执行文件。
>     -  `argv`：传递给新程序的参数数组，其中`argv[0]`通常是被执行文件的路径，数组的最后一个元素应该是NULL，以标识参数列表的结束。
>
>  4. **返回值**：
>
>     -  如果调用成功，`execvp`函数不会返回。
>     -  如果调用失败，则返回-1，并设置`errno`来指示错误类型。
>
>  5. **示例**：
>
>     使用`execv`和`execvp`函数来执行`ls`命令
>
>     ```c
>     #include <stdio.h>
>     #include <unistd.h>
>     int main(int argc, const char *argv[])
>     {
>     char *arg[] = {“ls”, “-a”, “-l”, “/etc”, NULL};
>     if  (execv(“/bin/ls”, arg) < 0) {
>     perror(“execv”);
>     }  
>     if  (execvp(“ls”, arg) < 0) {
>     perror(“execvp”);
>     }  
>     	return 0;
>     }
>     ```
>
>     运行效果同上

## system 函数

>  1. **原型**：
>
>     ```c
>     #include <stdlib.h>
>     int system(const char *command);
>     ```
>
>  2. **功能**：`system`函数用于在当前进程中创建一个子进程，并在子进程中执行一个shell命令。该函数会等待命令执行完成后返回。
>
>  3. **参数**：
>
>     -  `command`：指向以空字符终止的字符串的指针，该字符串包含要在子进程中执行的命令。
>
>  4. **返回值**：如果`system`函数成功执行了指定的命令，它将返回命令的退出状态。如果发生错误或命令无法执行，则返回-1。通常，返回值是shell的退出代码，可以通过`WEXITSTATUS(status)`宏来获取。
>
>  5. **注意**：`system`函数的使用可能会带来安全风险，因为它允许执行任意的shell命令。因此，在安全性要求较高的环境中，应该避免使用`system`函数，或者至少对输入的命令进行严格的检查和限制。
>
>  6. **示例**：
>
>     ```c
>     #include <studio.h>
>     #include <stdlib.h>
>     int main()
>     {
>         system("ls -a -l ./");
>     }
>     ```

# 5.守护进程

**概念：**

守护进程又叫精灵进程（Daemon Process），它是一个生存期较长的进程，通常独立于控制终端并且周期性地执行某种任务或等待处理某些发生的事件。

**特点：**

始终在后台运行，独立于任何终端，周期性的执行某种任务或等待处理特定事件。

它是个特殊的孤儿进程，这种进程脱离终端，为什么要脱离终端呢？之所以脱离于终端是为了避免进程被任何终端所产生的信息所打断，其在执行过程中的信息也不在任何终端上显示。由于在 Linux 中，每一个系统与用户进行交流的界面称为终端，每一个从此终端开始运行的进程都会依附于这个终端，这个终端就称为这些进程的控制终端，当控制终端被关闭时，相应的进程都会自动关闭

**举例：**

http 服务的守护进程叫 httpd，mysql 服务的守护进程叫 mysqld。

**相关名词：**

- **进程组**（Process Group）： 进程集合，每个进程组有一个组长（Leader），其进程 ID 就是该进程组 ID。
- **会话**（Session）： 进程组集合，每个会话有一个组长，其进程 ID 就是该会话组 ID。
- **控制终端**（Controlling Terminal）：每个会话可以有一个单独的控制终端，与控制终端连接的 Leader 就是控制进程（Controlling Process）。

## 创建守护进程

新用到的函数有：

1. `setsid` 于创建一个新的会话，并将当前进程设置为新会话的组长
2. `getsid`  获取当前进程所在会话的==会话ID==
3. `getpid`  获取当前进程的==进程ID==
4. `getpgid`  获取当前进程的==进程组ID==

### setsid 函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     pid_t setsid(void);
>     ```
>
>  2. **功能**：创建一个新的会话，并将当前进程设置为新会话的领导者。
>
>  3. **返回值**：
>
>     -  成功时返回新的会话ID
>     -  失败时返回-1。

### getsid 函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     pid_t getsid(pid_t pid);
>     ```
>
>  2. **功能**：获取指定进程所在的会话ID。
>
>  3. **参数**：`pid` - 要查询的进程ID。如果传入0，则返回当前进程所在的会话ID。
>
>  4. **返回值**：
>
>     -  成功时返回指定进程所在的会话ID
>     -  失败时返回-1。

**getpid 函数**

>  - `pid_t getpid(void);`    
>
>  - 获取进程id

**getpgid 函数**

>  - `pid_t getpgid(pid_t pid);`  
>
>  - 获取进程组id

组长进程不能成为新会话首进程,新会话首进程必定会成为组长进程。

## 创建步骤

### 1.创建子进程，父进程退出

> 创建子进程然后结束父进程，子进程变成孤儿进程，被init进程收养，子进程在后台运行
>
> ```c
> if (fork() > 0)  
> {
> exit(0);
> }
> ```
>
> 查看进程：$ps -ef|grep a.out
> 结束进程：$kill -9 [进程号]
>
> > 更简便的创建后台进程（不建议使用）
> >
> > 通过这条命令运行代码:$ <u>nohub ./a.out &</u>
> > 进程进入后台运行

### 2.子进程创建新会话

> ```c
> if (setsid() < 0)  
> {
> 	exit(-1);
> }
> ```
>
> 子进程成为新的会话组长
> 子进程脱离原先的终端

### 4.更改当前工作目录

>   ```c 
>   chdir(“/”);//更改当前工作目录到根目录
>   ```
>
>   守护进程一直在后台运行，其工作目录不能被卸载
>   重新设定当前工作目录cwd

### 5.重设文件权限掩码

> ```c
> if (umask(0) < 0)  
> {
> 	exit(-1);
> }
> ```
>
> 文件权限掩码设置为0
> 只影响当前进程

### 6.关闭打开的文件描述符

> ```c
> close(0);
> close(1);
> close(2);
> ```
>
> 关闭所有从父进程继承的打开文件
> 已脱离终端，stdin / stdout / stderr无法再使用

### 7.守护进程创建完成

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/stat.h>
int main(int argc, const char *argv[])
{
  pid_t pid;
  pid = fork();
  if(pid<0)
  {
    perror("fork");
    return 0;
  }
    
  //1.结束父进程
  else if(pid>0)
  {
    exit(0);
  }
  printf("Deamon\n");
  printf("sid=%d,pid=%d,pgid=%d\n",
         getsid(getpid()),
         getpid(),
         getpgid(getpid()));
    
  //2.子进程创建新会话
  if(setsid()<0)
  {
    perror("setsid");
    exit(0);
  }
  printf("after sid=%d,pid=%d,pgid=%d\n",
         getsid(getpid()),
         getpid(),
         getpgid(getpid()));
    
  //3.更改当前工作目录到根目录
  chdir("/");
  
  //4.文件权限掩码设置为0
  if(umask(0)<0)
  {
    perror("unmask");
    exit(0);
  }
    
  //5.关闭打开的文件描述符
  close(0);
  close(1);
  close(2);
  printf("after close \n");  
  
  sleep(100);
  return 0；
}
```

## GDB调试多进程程序

> gcc编译.c程序时加入 -g 参数
>
> ```shell
> gcc -g xxx.c
> gdb a.out #进入调试
> ```
>
> ![image-20240503150913582](./11_Concurrent_Programming.assets/image-20240503150913582.png)
>
> 出现上图表示进入了gdb调试

gdb相关命令

> - `run` 全速运行
> - `star` 单步调试
> - `n` 下一步
> - `set follow-fork-mode child/parent` 设置GDB只跟踪 子/父 进程代码
> - `set setach-on-fork on/off` 设置GDB跟踪调试单个进程或多个(默认为on)
>    - `on`: 只调试父进程或子进程的其中一个，(根据`follow-fork-mode`来决定)，这是默认的模式
>    - `off`：父子进程都在gdb的控制之下，其中一个进程正常调试(根据`follow-fork-mode`来决定),另一个进程会被设置为暂停状态。
> - `info inferiors`  显示GDB调试的进程
> - `inferiors [进程序号] (1,2,3....)`切换GDB调试的进程

# 6.线程创建

## 线程概念

**线程和进程在使用资源、创建开销以及通信方式上存在显著差异**。

首先，**进程是系统资源分配的独立单位**，每个进程拥有自己的地址空间，而**线程则共享所隶属进程的地址空间**。这意味着不同进程之间的资源如内存堆、栈是不能直接共享的，而同一进程内的多个线程可以直接访问这些共享资源。

其次，**进程相较于线程有更大的创建和管理开销**。因为进程有独立的地址空间，所以操作系统在创建或销毁进程时需要较大的系统资源开销。而线程作为调度的基本单位，其创建和上下文切换的开销要小得多，这也是为什么线程被称为轻量级进程的原因。

最后，**由于线程间共享内存空间，它们之间的通信和数据共享更为简单直接**。进程间则通常需要借助于进程间通信(IPC)机制，如管道、消息队列、共享内存等来完成数据交换。

### 线程特点

- 通常线程指的是共享相同地址空间的多个任务
- 使用多线程的好处
   - 大大提高了任务切换的效率
   - 避免了额外的TLB & cache的刷新

### 线程共享资源

一个进程中的多个线程共享以下资源：

1. 可执行的指令
2. 静态数据
3. 进程中打开的文件描述符
4. 当前工作目录
5. 用户ID
6. 用户组ID

### 线程私有资源

1. 线程ID (TID)
2. PC(程序计数器)和相关寄存器
3. 堆栈
4. 错误号 (errno)
5. 优先级
6. 执行状态和属性

## 线程创建

### pthread_create 函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine) (void *), void *arg);
>     ```
>
>  2. **功能**：创建一个新的线程，并使其开始执行指定的函数。
>
>  3. **参数**：
>
>     -  `pthread_t *thread`：指向一个`pthread_t`类型的指针，用于存储新创建线程的标识符。
>     -  `const pthread_attr_t *attr`：指向一个`pthread_attr_t`类型的指针，用于设置线程的属性。如果传入NULL，则使用默认属性。
>     -  `void *(*start_routine) (void *)`：指向一个函数指针，该函数将在新线程中执行。函数的返回类型为`void *`，参数为`void *`。
>     -  `void *arg`：传递给`start_routine`函数的参数。
>
>  4. **返回值**：
>
>     -  成功时返回0
>     -  失败时返回错误码。
>
>  5. **注意**：创建线程后，若主进程运行结束，它创建的线程也会随之结束。
>
>     所以，要在主进程预留一段时间等待线程结束，避免线程中断。

### 错误解决

>  使用pthread_create函数时出现的错误：
>
>  >  test_createP.c:(.text+0x4b)：对‘pthread_create’未定义的引用
>  >  collect2: error: ld returned 1 exit status  
>
>  这个为链接错误，因为pthread_create函数的库为<u>动态链接库</u>
>  解决：在编译时加上 -lpthread 
>  例：$gcc test.c -lpthread

## 线程结束

### pthread_exit 函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     void pthread_exit(void *retval);
>     ```
>
>  2. **功能**：结束当前线程，线程私有资源被释放，并将返回值传递给其他线程
>
>  3. **参数**：`void *retval`：指向一个指针，用于存储线程的返回值。该值可以被其他线程通过`pthread_join()`函数获取。

## 查看线程

### pthread_self 函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     pthread_t pthread_self(void);
>     ```
>
>  2. **功能**：获取调用线程(自身所处线程)的标识符。
>
>  3. **返回值**：返回调用线程的标识符，类型为`pthread_t`。


## 线程传参

线程间传参有两种方式，**值传递**和**地址传递**。

> 示例：`arg1`为地址传递，`arg2`为值传递
>
> ```c
> #include <stdio.h>
> #include <pthread.h>
> #include <unistd.h>
> 
> int * test_pth1(void *arg)
> {
> 	printf("p1 tid = %lu\n",pthread_self());//线程ID
> 	printf("p1 pid = %d\n",getpid());//进程ID
> 	printf("p1 arg = %d\n",*(int *)arg);//传的参数
> 	pthread_exit(NULL);//退出线程
> }
> int * test_pth2(void *arg)
> {
> 	printf("p2 tid = %lu\n",pthread_self());//线程ID
> 	printf("p2 pid = %d\n",getpid());//进程ID
> 	printf("p2 arg = %d\n",(int)arg);//传的参数
> 	pthread_exit(NULL);//退出线程
> }
> int main(int argc, const char *argv[])
> {
> 	pthread_t tid;//线程ID
> 	int ret;
> 	int arg1,arg2;
> 	/*创建线程*/
> 
> 	for(arg1=0; arg1<4; arg1++)
> 	{//地址传递
> 		ret = pthread_create(&tid,NULL,(void *)test_pth1,(void *)&arg1);
> 	}
> sleep(1);
> 	for(arg2=0; arg2<4; arg2++)
> 	{//值传递
> 		ret = pthread_create(&tid,NULL,(void *)test_pth2,(void *)arg2);
> 	}
> 	printf("main tid%lu\n",tid);
> 	sleep(1);//等待线程结束
> 	return 0;
> }
> ```
>
> 运行结果：
>
> ![image-20240503235820481](./11_Concurrent_Programming.assets/image-20240503235820481.png)
>
> <u>地址传递</u>arg的值都为4，而<u>值传递</u>的arg值为创建线程时传入的值
>
> 地址传递的值会随着地址指向的数改变而改变，而线程创建的速度要比线程运行的速度要快，四个线程创建完后arg 的值为4，然后线程才开始运行，所以都打印出arg=4。

----

# 7.线程回收

## 两种方法回收线程

1. 使用pthread_join 函数
2. 使线程分离

### 1.pthread_join 函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     int pthread_join(pthread_t thread, void **retval);
>     ```
>
>  2. **功能**：等待一个线程的结束
>
>  3. **参数**：
>
>     -  `thread`：需要等待的线程ID。
>     -  `retval`：指向一个指针，用于存储被等待线程的返回值。如果不关心返回值，可以设置为NULL。
>
>  4. **返回值**：
>
>     -  成功时，返回0；
>     -  失败时，返回一个非零的错误码。
>
>  5. **注意**：`pthread_join` 是阻塞函数，如果回收的线程没有结束，则一直等待
>
>  6. **示例**：
>
>     > 创建了一个子线程，并在子线程中执行一个函数fun。主线程等待子线程结束后，打印子线程的返回值。
>     >
>     > ```c
>     > #include <stdio.h>
>     > #include <pthread.h>
>     > #include <unistd.h>
>     > void *fun(void * arg)
>     > {
>     > 	int i;
>     > 	pthread_detach(pthread_self());
>     > 	for(i=0; i<3; i++)
>     > 	{
>     > 		printf("child %d\n",i);
>     > 		sleep(1);
>     > 	}
>     > 	pthread_exit("fun return");
>     > }
>     > int main(int argc, const char *argv[])
>     > {
>     > 	int i = 0;
>     > 	void *retf;
>     > 	pthread_t tid;
>     > 	pthread_create(&tid,NULL,fun,(void *)i);
>     > 	pthread_join(tid,&retf);//等待线程结束
>     > 	printf("%s\n",(char*)retf);
>     > 	return 0;
>     > }
>     > ```



### 2.线程分离

线程分离是将线程设置为不被其他线程等待或回收的状态，从而在退出时自动释放资源。

- 如果不需要关心线程的返回值，或者不希望等待线程结束，可以将线程设置为分离状态。这样，线程在结束时会自动释放其所占用的资源，无需进行`pthread_join`操作，从而减少了程序的负担。
- **注意**：一旦线程被分离，就不能再被其他线程回收，也就不能调用`pthread_join`。因此，在决定分离线程之前，应该仔细考虑线程的用途和生命周期。

**两种方式使线程分离**

1. 使用`pthread_detach`函数
2. 使用`pthread_attr_init`函数和`pthread_attr_setdetachstate`函数，设置线程属性为分离

#### pthread_detach 函数

1. **原型**：

   ```c
   #include <pthread.h>
   int pthread_detach(pthread_t thread);
   ```

2. **功能**：将一个线程设置为分离状态，线程结束后（不会产生僵尸线程）

3. **参数**：`thread`：需要设置为分离状态的线程ID。

4. **返回值**：

   -  成功时，返回0；
   -  失败时，返回一个非零的错误码

5. **示例**：

   >  
   >
   >  ```c
   >  #include <stdio.h>
   >  #include <pthread.h>
   >  #include <unistd.h>
   >  void *fun(void * arg)
   >  {
   >  	pthread_detach(pthread_self());//分离线程
   >  	printf("child %d\n",(int)arg);
   >  	pthread_exit(NULL);
   >  }
   >  int main(int argc, const char *argv[])
   >  {
   >  	int i;
   >  	pthread_t tid[10];
   >  	for(i=0; i<10; i++)//创建十个线程
   >  	{
   >  		pthread_create(&tid[i],NULL,fun,(void *)i);
   >  	}
   >  	sleep(10);//等待线程运行
   >  	return 0;
   >  }
   >  ```



#### 设置线程属性为分离

##### pthread_attr_init函数

>  1. **原型**：
>
>     ```c
>     int pthread_attr_init(pthread_attr_t *attr);
>     ```
>
>  2. **功能**：初始化一个线程属性对象
>
>  3. **参数**：`attr`：指向要初始化的线程属性对象的指针。
>
>  4. **返回值**：
>
>     -  成功时，返回0；
>     -  失败时，返回一个非零的错误码。

##### pthread_attr_setdetachstate函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     int pthread_attr_setdetachstate(pthread_attr_t *attr, int detachstate);
>     ```
>
>  2. **功能**：设置线程属性对象的分离状态
>
>  3. **参数**：
>
>     -  `attr`：指向要设置分离状态的线程属性对象的指针。
>
>     -  `detachstate`：指定线程的分离状态，可以是PTHREAD_CREATE_JOINABLE或PTHREAD_CREATE_DETACHED。
>
>  4. **返回值**：
>
>     -  成功时，返回0；
>     -  失败时，返回一个非零的错误码。

>  示例：该线程在创建时就是分离的线程
>
>  ```c
>  pthread_attr_t attr;//定义线程属性变量
>  pthread_attr_init(&attr);//初始化该变量
>  pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);//设置属性为分离
>  pthread_create(&tid,&attr,fun,NULL);//创建线程
>  ```

可以通过top命令查看进程占用的内存空间大小，检验线程是否被回收

**注意**：分离后，进程结束，线程也会随之结束。

---

## 取消线程

### pthread_cancel 函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     int pthread_cancel(pthread_t thread);
>     ```
>
>  2. **功能**：取消一个线程的执行
>
>  3. **参数**：`thread`：要取消执行的线程ID。
>
>  4. **返回值**：
>
>     -  成功时，返回0；
>     -  失败时，返回一个非零的错误码。
>
>  5. **注意**：线程的取消要有取消点才可以，不是说取消就取消，线程的取消点主要是==阻塞的系统调用==如sleep();
>
>     若没有取消点，可手动设置一个取消点


### pthread_testcancel 函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     void pthread_testcancel(void);
>     ```
>
>  2. **功能**：在当前线程中创建一个取消点，这样如果该线程收到取消请求时，它可以在这个点上响应并执行相应的取消操作。

### pthread_setcancelstate函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     int pthread_setcancelstate(int state, int *oldstate);
>     ```
>
>  2. **功能**：设置线程的取消状态
>
>  3. **参数**：
>
>     -  `state`：指定线程的取消状态，可以是PTHREAD_CANCEL_ENABLE或PTHREAD_CANCEL_DISABLE。
>
>     -  `oldstate`：指向一个整型变量的指针，用于保存线程原来的取消状态。
>
>  4. **返回值**：
>
>     -  成功时，返回0；
>     -  失败时，返回一个非零的错误码。

### pthread_setcanceltype函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     int pthread_setcanceltype(int type, int *oldtype);
>     ```
>
>  2. **功能**：设置线程的取消类型
>
>  3. **参数**：
>
>     -  `type`：指定线程的取消类型，可以是PTHREAD_CANCEL_DEFERRED（等到取消点才取消）、PTHREAD_CANCEL_ASYNCHRONOUS（目标线程会立即取消）
>
>     -  `oldtype`：指向一个整型变量的指针，用于保存线程原来的取消类型。
>
>  4. **返回值**：
>
>     -  成功时，返回0；
>     -  失败时，返回一个非零的错误码。


>  示例：线程设置为可取消后，被取消，线程结束
>
>  ```c
>  #include <stdio.h>
>  #include <pthread.h>
>  #include <unistd.h>
>  void *fun(void * arg)
>  {
>  	int i;
>  	pthread_setcancelstate(PTHREAD_CANCEL_DISABLE,NULL);//设置线程为不能取消
>  	for(i=0; i<10; i++)
>  	{
>  		printf("child %d\n",i);
>  		sleep(1);
>  	}
>  	pthread_setcancelstate(PTHREAD_CANCEL_ENABLE,NULL);//设置线程为可以取消
>  	for(i=10; i<20; i++)
>  	{
>  		printf("child %d\n",i);
>  		sleep(1);
>  	}
>  	pthread_exit(NULL);
>  }
>  int main(int argc, const char *argv[])
>  {
>  	int i = 0;
>  	pthread_t tid;
>  	pthread_create(&tid,NULL,fun,(void *)i);
>  	sleep(5);//等待五秒	
>  	pthread_cancel(tid);//取消线程
>  
>  	while(1)
>  	{
>  		sleep(1);
>  	}
>  	return 0;
>  }
>  ```

## 线程的清理

当线程非正常终止，需要清理一些资源。

### pthread_cleanup_push函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     void pthread_cleanup_push(void (*routine)(void *), void *arg);
>     ```
>
>  2. **功能**：将一个清理函数压入线程的清理栈中
>
>  3. **参数**：
>
>     -  `routine`：指向要压入清理栈中的清理函数的指针。
>        -  被pthread_cancel取消掉。
>
>        -  执行pthread_exit 
>
>        -  非0参数执行pthread_cleanup_pop()
>
>     -  `arg`：传递给清理函数的参数。

### pthread_cleanup_pop函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     void pthread_cleanup_pop(int execute);
>     ```
>
>  2. **功能**：用来解除之前通过`pthread_cleanup_push`注册的线程清理函数
>
>  3. **参数**：`execute`：一个整数，表示是否执行清理函数。如果为非零值，则执行清理函数；如果为零，则不执行。

>  **注意**：
>
>  1.  ==必须成对使用==，即使pthread_cleanup_pop不会被执行到也必须写上，否则编译错误。
>  2.  pthread_cleanup_pop()被执行且参数为0，pthread_cleanup_push回调函数routine不会被执行.
>
>  3.  pthread_cleanup_push 和pthread_cleanup_pop可以写多对，routine执行顺序正好相反
>
>  4.  线程内的 return 可以结束线程，也可以给pthread_join返回值，但不能触发pthread_cleanup_push里面的回调函数，所以我们结束线程尽量使用pthread_exit退出线程。

---

# 8.线程的同步和互斥

**概念**

1.  临界资源： 一次只允许一个任务(进程、线程)访问的共享资源

2.  临界区：访问临界资源的程序

3.  互斥机制：mutex互斥锁，任务访问临界资源前申请锁，访问完后释放锁

## 互斥锁

### 互斥锁创建

-  动态方式：

   -  `int pthread_mutex_init(pthread_mutex_t *restrict **mutex**,const pthread_mutexattr_t *restrict attr);`

   -  mutexattr：指定互斥锁属性，如果为NULL则使用缺省属性。

-  静态方式：

   -  `pthread_mutex_t **mutex** = PTHREAD_MUTEX_INITIALIZER;`
   -  mutex：锁的名称

### 锁的销毁

-  `int pthread_mutex_destroy(pthread_mutex_t *mutex)`

-  在Linux中，互斥锁并不占用任何资源，因此LinuxThreads中的 pthread_mutex_destroy()除了检查锁状态以外（锁定状态则返回EBUSY）没有其他动作。

### 锁的使用

-  `#include <pthread.h>`
-  申请锁：

   -  `int pthread_mutex_lock(pthread_mutex_t *mutex);`
      -  `pthread_mutex_lock` 如果无法获得锁，任务阻塞
   -  `int pthread_mutex_trylock(pthread_mutex_t *mutex);`
      -  `pthread_mutex_trylock` 如果无法获得锁，返回EBUSY而不是挂起等待
   -  执行完临界区要及时释放锁
-  释放锁：

   -  `int pthread_mutex_unlock(pthread_mutex_t *mutex);`
   -  成功时返回0，失败时返回错误码
   -  `mutex` 指向要初始化的互斥锁对象

### 示例

使用互斥锁，用两个线程写文件

```c
#include <pthread.h>
#include <stdio.h>
#include <unistd.h>
#include <string.h>

pthread_mutex_t mutex1 = PTHREAD_MUTEX_INITIALIZER;//互斥锁创建锁
FILE *fp;
void *fun1(void *arg)
{
	int i;
	char c = 'A';
	pthread_detach(pthread_self());//分离线程
	printf("fun1...\n");
	i=0;
	pthread_mutex_lock(&mutex1);//互斥锁上锁
	while(i<26)
	{
		printf("%d\n",i);
		fputc(c+i,fp);
		usleep(1);
		i++;
	}
    fputc('\n',fp);
	fflush(fp);//刷新流	
	pthread_mutex_unlock(&mutex1);//互斥锁解锁
	pthread_exit("fun1 exit");
}
void *fun2(void *arg)
{
	int i;
	char c = 'a';
	pthread_detach(pthread_self());//分离线程
	printf("fun2...\n");
	i=0;
	pthread_mutex_lock(&mutex1);//互斥锁上锁
	while(i<26)
	{
		printf("%d\n",i);
		fputc(c+i,fp);
		usleep(1);
		i++;
	}
    fputc('\n',fp);
	fflush(fp);//刷新流	
	pthread_mutex_unlock(&mutex1);//互斥锁解锁
	pthread_exit("fun2 exit");
}
int main()
{
	pthread_t tid1,tid2;
	void *retv;
	int i;
	fp = fopen("1.txt","a+");//读写方式打开文件
	if(fp==NULL)
	{
		perror("fopen");
		return 0;
	}
	pthread_create(&tid1,NULL,fun1,NULL);
	pthread_create(&tid2,NULL,fun2,NULL);
    sleep(5);
    return 0;
}
```

### man手册找不到

pthread_mutex_xxxxxxx （提示No manual entry for pthread_mutex_xxx）的解决方法：

 输入命令：sudo apt-get install manpages-posix-dev

---

## 读写锁

​	提高线程执行效率

### 特性

>  写者：写者使用写锁，如果当前没有读者，也没有其他写者，写者立即获得写锁；否则写者将等待，直到没有读者和写者。
>
>  读者：读者使用读锁，如果当前没有写者，读者立即获得读锁；否则读者等待，直到没有写者。

### 注意

>  同一时刻只有一个线程可以获得写锁，同一时刻可以有多个线程获得读锁。
>
>  读写锁出于写锁状态时，所有试图对读写锁加锁的线程，不管是读者试图加读锁，还是写者试图加写锁，都会被阻塞。
>
>  读写锁处于读锁状态时，有写者试图加写锁时，之后的其他线程的读锁请求会被阻塞，以避免写者长时间的不写锁

### 相关函数

>   #include <pthread.h>
>
>   -  pthread_rwlock_init  初始化一个读写锁
>      -  int pthread_rwlock_init(pthread_rwlock_t *restrict rwlock,const pthread_rwlockattr_t *restrict attr);
>   -  pthread_rwlock_rdlock  读锁定读写锁
>      -  int pthread_rwlock_rdlock(pthread_rwlock_t *rwlock);
>   -  pthread_rwlock_tryrdlock  非阻塞读锁定
>      -  int pthread_rwlock_tryrdlock(pthread_rwlock_t *rwlock);
>   -  pthread_rwlock_wrlock  写锁定读写锁
>      -  int pthread_rwlock_wrlock(pthread_rwlock_t *rwlock);
>   -  pthread_rwlock_trywrlock  非阻塞写锁定
>      -  int pthread_rwlock_trywrlock(pthread_rwlock_t *rwlock);
>   -  pthread_rwlock_unlock  解锁读写锁
>      -  int pthread_rwlock_unlock(pthread_rwlock_t *rwlock);
>   -  pthread_rwlock_destroy  释放读写锁
>      -  int pthread_rwlock_destroy(pthread_rwlock_t *rwlock);

### 示例

创建两个读进程两个写进程，利用读写锁让它们互不干扰

>   ```c
>   #include <pthread.h>
>   #include <stdio.h>
>   #include <unistd.h>
>   #include <string.h>
>   
>   pthread_rwlock_t rwlock;//创建读写锁
>   FILE *fp;
>   void *fun1(void *arg)
>   {
>   	int i;
>   	char c = 'A';
>   	pthread_detach(pthread_self());//分离线程
>   	printf("fun1...\n");
>   	i=0;
>   	pthread_rwlock_wrlock(&rwlock);//写锁上锁
>   	while(i<26)
>   	{
>   		fputc(c+i,fp);
>   		usleep(1);
>   		i++;
>   	}
>   	fputc('\n',fp);
>   	fflush(fp);//刷新流	
>   	pthread_rwlock_unlock(&rwlock);//解锁
>   	pthread_exit("fun1 exit");
>   }
>   void *fun2(void *arg)
>   {
>   	int i;
>   	char c = 'a';
>   	pthread_detach(pthread_self());//分离线程
>   	printf("fun2...\n");
>   	i=0;
>   	pthread_rwlock_wrlock(&rwlock);//写锁上锁
>   	while(i<26)
>   	{
>   		fputc(c+i,fp);
>   		usleep(1);
>   		i++;
>   	}
>   	fputc('\n',fp);
>   	fflush(fp);//刷新流	
>   	pthread_rwlock_unlock(&rwlock);//解锁
>   	pthread_exit("fun2 exit");
>   }
>   
>   void * fun3_read(void *arg)//读进程
>   {
>   	pthread_detach(pthread_self());//分离线程
>   	printf("fun3_read...\n");
>   	char buf[32]={0};
>   	while(1)
>   	{
>   		pthread_rwlock_rdlock(&rwlock);//读锁上锁
>   		while(fgets(buf,32,fp)!=NULL)
>   		{
>   			printf("%d,rd=%s\n",(int)arg,buf);
>   			usleep(1000);
>   		}
>   		pthread_rwlock_unlock(&rwlock);//读锁解锁
>   		sleep(1);
>   	}
>   	pthread_exit("fun3 exit");
>   }
>   void * fun4_read(void *arg)//读进程
>   {
>   	pthread_detach(pthread_self());//分离线程
>   	printf("fun4_read...\n");
>   	char buf[32]={0};
>   	while(1)
>   	{
>   		//rewind(fp);
>   		pthread_rwlock_rdlock(&rwlock);//读锁上锁
>   		while(fgets(buf,32,fp)!=NULL)
>   		{
>   			printf("%d,rd=%s\n",(int)arg,buf);
>   			usleep(1000);
>   		}
>   		pthread_rwlock_unlock(&rwlock);//读锁解锁
>   		sleep(1);
>   	}
>   	pthread_exit("fun4 exit");
>   }
>   
>   int main()
>   {
>   	pthread_t tid1,tid2,tid3,tid4;
>   	void *retv;
>   	int i;
>   	fp = fopen("1.txt","a+");//读写方式打开文件
>   	if(fp==NULL)
>   	{
>   		perror("fopen");
>   		return 0;
>   	}
>   	pthread_rwlock_init(&rwlock,NULL);//初始化读写锁
>   	pthread_create(&tid1,NULL,fun1,NULL);
>   	pthread_create(&tid2,NULL,fun2,NULL);
>   	pthread_create(&tid3,NULL,fun3_read,(void *)3);
>   	pthread_create(&tid4,NULL,fun4_read,(void *)4);
>    sleep(5);
>   	return 0;
>   }
>   ```

---

## 死锁

死锁是指**两个或多个进程在执行过程中，因争夺资源而造成的一种相互等待的永久阻塞状态**。

死锁通常发生在多任务环境中，当两个或多个进程各自持有一部分资源，同时又互相等待对方释放另一部分资源时，就会产生死锁。这种现象在操作系统、数据库系统以及并发程序中都有可能出现。

**产生死锁需要满足以下四个必要条件：**

>  1. **互斥条件**：资源至少有一个是不能被共享的，即一次只能由一个进程使用。
>  2. **占有和等待条件**：一个进程至少已经持有一个资源，且正在请求额外的资源，而该资源可能被其他进程持有。
>  3. **非抢占条件**：已经分配给进程的资源在未使用完之前不能被其他进程强行夺走，只能被占有它的进程释放。
>  4. **循环等待条件**：存在一种进程资源的循环等待链，每个进程持有下一个进程所需的至少一个资源。

**解决和避免死锁的方法包括：**

>  1. **按照顺序加锁**：对资源进行编号，并规定所有进程按照固定的顺序请求资源，以避免循环等待。
>  2. **设置获取锁的超时时间**：限制进程等待资源的时间，若在指定时间内无法获取所需资源，则放弃已占有的资源或者回退操作。
>  3. **死锁检测与恢复**：通过系统工具定期检测系统的资源分配情况，一旦发现死锁，采取措施解除死锁，如中断其中一个进程或者回收资源等

---

# 9.条件变量使用

条件变量是一种线程同步机制，允许一个或多个线程等待直到另一个线程发出信号通知条件已满足。

以下是条件变量的一些关键特性和应用：

1. **线程间通信**：条件变量允许线程之间进行通信，当某个条件成立时，可以唤醒等待该条件的线程。
2. **与互斥锁结合**：为了保护共享资源并防止竞态条件，条件变量通常与互斥锁一起使用。线程在等待条件时会释放互斥锁，允许其他线程访问共享资源；而当条件满足时，线程会重新获取互斥锁以继续执行。
3. **等待和通知机制**：条件变量提供了一种机制，使得线程可以在条件不满足时等待，而在条件满足时被通知。这种机制提高了程序的效率，因为线程不需要循环检查条件是否满足。
4. **生产者消费者模型**：条件变量常用于实现生产者消费者模型，其中生产者线程在生成数据时发出信号，消费者线程在数据可用时被唤醒进行处理。
5. **避免饥饿问题**：通过合理地使用条件变量和互斥锁，可以避免线程饥饿问题，确保所有线程都有机会执行。
6. **接口和函数**：在C语言中，条件变量的操作通常涉及`pthread_cond_wait`和`pthread_cond_signal`函数。`pthread_cond_wait`使线程等待某个条件，而`pthread_cond_signal`用于唤醒等待该条件的线程。

## 相关函数

### pthread_cond_wait函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     int pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex);
>     ```
>
>  2. **功能**：等待条件变量，等待前互斥锁要上锁
>
>  3. **参数**：
>
>     -  cond：指向要等待的条件变量的指针。
>     -  mutex：指向与条件变量关联的互斥锁的指针。在调用pthread_cond_wait之前，线程必须已经锁定了该互斥锁。
>
>  4. **返回值**：
>
>     -  成功时，返回0；
>     -  失败时，返回一个非零错误码。

### pthread_cond_timedwait函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     int pthread_cond_timedwait(pthread_cond_t *cond, pthread_mutex_t *mutex, const struct timespec *abs_timeout);
>     ```
>
>  2. **功能**：等待条件变量一定时间，等待前互斥锁要上锁
>
>  3. **参数**：
>
>     -  cond：指向要等待的条件变量的指针。
>     -  mutex：指向与条件变量关联的互斥锁的指针。在调用pthread_cond_timedwait之前，线程必须已经锁定了该互斥锁。
>     -  abs_timeout：指向一个timespec结构体，表示绝对超时时间。如果为NULL，则表示无限等待。
>
>  4. **返回值**：
>
>     -  成功时，返回0；
>     -  失败时，返回一个非零错误码；
>     -  超时时，返回ETIMEDOUT。

### pthread_cond_signal函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     int pthread_cond_signal(pthread_cond_t *cond);
>     ```
>
>  2. **功能**：当调用时，如果有线程正在等待该条件变量，那么**其中一个**将得到唤醒。如果没有线程在等待，信号会被保留，直到有线程开始等待。
>
>  3. **参数**：cond：指向要发送信号的条件变量的指针。
>
>  4. **返回值**：
>
>     -  成功时，返回0；
>     -  失败时，返回一个非零错误码。
>
>  5. **注意**：在使用时需要注意避免**惊群效应**，即多个线程被同时唤醒但只有一个或少数几个能实际执行。这通常通过结合使用while循环和条件变量来避免。

### pthread_cond_broadcast函数

>  1. **原型**：
>
>     ```c
>     #include <pthread.h>
>     int pthread_cond_broadcast(pthread_cond_t *cond);
>     ```
>
>  2. **功能**：用于唤醒等待在指定条件变量上的所有线程。当有多个线程在等待同一个条件变量时，所有线程都会被唤醒。
>
>  3. **参数**：cond：指向要发送信号的条件变量的指针。
>
>  4. **返回值**：
>
>     -  成功时，返回0；
>     -  失败时，返回一个非零错误码。

## 使用步骤

### 生产者线程

>  1.  初始化
>
>      ```c
>      //1.初始化条件变量
>          /*静态初始化*/
>          pthread_cond_t  cond = PTHREAD_COND_INITIALIZER;   
>          /*动态初始化*/
>          //pthread_cond_init(&cond);
>      //2.初始化互斥锁变量
>      	pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
>      ```
>
>  2.  ```c
>      pthread_mutex_lock(&lock1);//上锁
>      ```
>
>  3.  生成资源
>
>  4.  ```c
>      pthread_cond_signal(&hasProduct);//转为有资源状态，通知一个消费线程
>      ```
>
>  5.  ```c
>      pthread_cond_signal(&hasProduct);//转为有资源状态，通知一个消费线程
>      //pthread_cond_broadcast(&hasProduct);//转为有资源状态，通知多个消费线程
>      ```
>
>  6.  ```c
>      pthread_mutex_unlock(&lock1);//解锁
>      ```

### 消费者线程

>  1.  ```c
>      pthread_mutex_lock(&lock1);//上锁
>      ```
>
>  2.  ```c
>      pthread_cond_wait(&hasProduct,&lock1);//等待有资源
>      ```
>
>  3.  消费资源
>
>  4.  ```c
>      pthread_mutex_unlock(&lock1);//解锁
>      ```
>
>  >  [!NOTE]
>  >
>  >  1.  `pthread_cond_wait`(&cond, &lock1);是在没有资源等待是是先unlock休眠，等资源到了，再lock。所以`pthread_cond_wait` 和 `pthread_mutex_lock` 必须配对使用。
>  >  2.  如果`pthread_cond_signal` 或者 `pthread_cond_broadcast` 早于 `pthread_cond_wait` ，则有可能会丢失信号。 
>  >  3.  `pthead_cond_broadcast` 信号会被多个线程收到，这叫线程的**惊群效应**。所以需要加上判断条件`while`循环。

## 示例

一个消费线程，一个生产线程

```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>
#include <stdlib.h>
pthread_cond_t hasProduct=PTHREAD_COND_INITIALIZER;//条件变量
pthread_mutex_t lock1 = PTHREAD_MUTEX_INITIALIZER;//互斥锁
struct product//产品结构体
{
	struct product *next;
	int num;
};
struct product *head = NULL;//头结点

void *fun1_make(void *arg)
{
	printf("fun1_make...\n");
	pthread_detach(pthread_self());//分离线程
	struct product *px;
	int i=1;
	while(1)
	{
		px = malloc(sizeof(struct product));
		px->num = i++;
		printf("product %d ok\n",px->num);
		pthread_mutex_lock(&lock1);//上锁
		/*生产资源*/
		px->next = head;
		head = px;

		pthread_cond_signal(&hasProduct);//转为有资源状态，通知一个消费线程
		//pthread_cond_broadcast(&hasProduct);//转为有资源状态，通知多个消费线程
		pthread_mutex_unlock(&lock1);//解锁
		sleep(1);
	}
}
void *fun2_buy(void *arg)
{
	printf("fun2_buy...\n");
	pthread_detach(pthread_self());
	struct product *px;
	while(1)
	{
		pthread_mutex_lock(&lock1);//上锁
		while(head == NULL)//防止惊群效应
		{
			pthread_cond_wait(&hasProduct,&lock1);//等待有资源
		}
		/*消费资源*/
		px = head;
		head = px->next;

		printf("arg=%d,buy over%d\n",(int)arg,px->num);
		free(px);
		pthread_mutex_unlock(&lock1);//解锁
	}
	pthread_exit(0);
}
int main(int argc, const char *argv[])
{
	pthread_t tid1,tid2,tid3,tid4;
	pthread_create(&tid1,NULL,fun1_make,NULL);
	sleep(1);
	pthread_create(&tid2,NULL,fun2_buy,(void *)2);
	//pthread_create(&tid3,NULL,fun2_buy,(void *)3);
	//pthread_create(&tid4,NULL,fun2_buy,(void *)4);
	
	while(1)
		sleep(1);
	return 0;
}
```

# 10.线程池

通俗的讲就是一个线程的池子，可以循环的完成任务的一组线程集合

>  **必要性**：
>
>  我们平时创建一个线程，完成某一个任务，等待线程的退出。但当需要创建大量的线程时，假设T1为创建线程时间，T2为在线程任务执行时间，T3为线程销毁时间，当 T1+T3 > T2，这时候就不划算了，使用线程池可以降低频繁创建和销毁线程所带来的开销，任务处理时间比较短的时候这个好处非常显著。

>  线程池的**基本结构**：
>
>  1.  任务队列，存储需要处理的任务，由工作线程来处理这些任务
>  2.  线程池工作线程，它是任务队列任务的消费者，等待新任务的信号

## 线程池实现步骤

1.  创建线程池的基本结构：

    ```c
    typedef struct Task;  //任务队列链表
    typedef struct ThreadPool;  //线程池结构体
    ```

2.  线程池的初始化：

    ```c
    pool_init()
    {
    	/*创建一个线程池结构*/
    	/*实现任务队列互斥锁和条件变量的初始化*/
    	/*创建n个工作线程*/
    }
    ```

3.  线程池添加任务：

    ```c
    pool_add_task
    {
    	/*判断是否有空闲的工作线程*/
    	/*给任务队列添加一个节点*/
    	/*给工作线程发送信号newtask*/
    }
    ```

4.  实现工作线程：

    ```c
    workThread
    {
        while(1)
        {
           /*等待newtask任务信号*/
           /*从任务队列中删除节点*/
           /*执行任务*/
        }
    }
    ```

5.  线程池的销毁

    ```c
    pool_destory
    {
        /*删除任务队列链表所有节点，释放空间*/
        /*删除所有的互斥锁条件变量*/
        /*删除线程池，释放空间*/
    }
    ```

## 示例

20个任务，4个工作线程

```c
#include <pthread.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>

#define POOL_NUM 4//线程池内工作线程数量
/*1.基本结构*/
typedef struct Task//任务链表结构体
{
	void *(*func)(void *arg);//任务（函数指针）
	void *arg;//函数参数
	struct Task *next;//下一个任务节点
}Task;

typedef struct ThreadPool//线程池结构体
{
	pthread_mutex_t taskLock;//任务互斥锁
	pthread_cond_t newTask;//条件变量

	pthread_t tid[POOL_NUM];//线程ID
	Task *queue_head;//任务链表头
	int busywork;//忙碌线程数
}ThreadPool;

ThreadPool *pool;//线程池变量

void *realwork(void *arg);
void pool_init();
void pool_add_task(int arg);
void *workThread(void *arg);
void pool_destory();

/*2.初始化*/
void pool_init()//线程池初始化
{
	int i;
	pool = malloc(sizeof(ThreadPool));//申请空间
	pthread_mutex_init(&pool->taskLock,NULL);//互斥锁初始化
	pthread_cond_init(&pool->newTask,NULL);//条件变量初始化
	pool->queue_head = NULL;//任务链表头节点
	pool->busywork=0;//忙碌线程数
    
	//创建线程
	for(i=0;i<POOL_NUM;i++)
	{
		pthread_create(&pool->tid[i],NULL,workThread,NULL);
	}
}

/*3.添加任务*/
void *realwork(void *arg)//“任务”
{
	printf("work%d\n",(int)arg);
}
void pool_add_task(int arg)//添加任务节点
{
	Task *newTask;//定义新任务节点

	pthread_mutex_lock(&pool->taskLock);//1上锁
    
    //等待任务链表空位
	while(pool->busywork>=POOL_NUM)//没有空闲线程
	{
		pthread_mutex_unlock(&pool->taskLock);//1解锁
		usleep(10000);//等待10ms
		pthread_mutex_lock(&pool->taskLock);//2上锁
	}
	pthread_mutex_unlock(&pool->taskLock);//2解锁
	
    //创建新任务节点
	newTask = malloc(sizeof(Task));//新任务节点申请空间
	newTask->func =  realwork;//新任务节点的任务
	newTask->arg = (void *)arg;//新任务节点的参数

    //将新节点从链表尾部加入
	pthread_mutex_lock(&pool->taskLock);//3上锁
	Task *member = pool->queue_head;
	if(member==NULL)//如果头节点为空
		pool->queue_head = newTask;
	else//如果头节点不为空
	{
		while(member->next!=NULL)//找到节点尾部
		{
			member=member->next;
		}
		member->next = newTask;//将新节点加入链表
	}
	pool->busywork++;
	pthread_cond_signal(&pool->newTask);//条件变量通知有资源
	pthread_mutex_unlock(&pool->taskLock);//3解锁
}

/*4.实现工作线程*/
void *workThread(void *arg)//工作线程函数
{
	while(1)
	{
		pthread_mutex_lock(&pool->taskLock);//上锁
		pthread_cond_wait(&pool->newTask,&pool->taskLock);//等待新任务
		//从链表头部取出任务节点
		Task *ptask = pool->queue_head;
		pool->queue_head = pool->queue_head->next;
		pthread_mutex_unlock(&pool->taskLock);//解锁

        //执行任务
		ptask->func((void *)ptask->arg);//通过节点取出任务函数，执行
		
        //任务结束
        pool->busywork--;
	}
}
/*5.线程池销毁*/
void pool_destory()
{
	Task *head;
	while(pool->queue_head!=NULL)
	{
		head = pool->queue_head;
		pool->queue_head = pool->queue_head->next;
		free(head);//释放节点空间
	}
	pthread_mutex_destroy(&pool->taskLock);//销毁互斥锁
	pthread_cond_destroy(&pool->newTask);//销毁条件变量
	free(pool);//释放线程池空间
}
int main()
{
	int i;
    
    //启动线程池
	pool_init();
	sleep(2);
    
    //添加任务
	for(i=1;i<=20;i++)
	{
		pool_add_task(i);
	}
	sleep(2);
	pool_destory();
}
```

---

## GDB调试多线程程序

>  gcc编译.c程序时加入 -g 参数
>
>  ```shell
>  gcc -g xxx.c
>  gdb a.out #进入调试
>  ```

相关指令：

-  (gdb) info thread
   -  显示线程
-  (gdb) thread [id]
   -  切换线程
-  (gdb) break location thread [id]
   -  为特定线程设置断点
-  (gdb) b [行号]
   -  在行号处设置断点
-  (gdb) b [行号] thread [id]
   -  
-  (gdb) set scheduler-locking on/off
   -  设置线程锁
   -  on：其他线程会暂停。可以单独调试一个线程
-  (gdb) p threadName
   -  显示当前线程

# 11.进程间通信

（IPC，InterProcess Communication）进程和进程之间交换信息。

-  常用通信方式
   -  无名管道（pipe）
   -  有名管道 （fifo）
   -  信号（signal）
   -  共享内存(mmap)
   -  套接字（socket）
-  过时的IPC通信方式
   -  System V IPC
   -  共享内存（share memory）
   -  消息队列（message queue）
   -  信号灯集（semaphore set）

## 无名管道

无名管道（也称为匿名管道）是UNIX和类UNIX系统下进程间通信的一种机制：

​	==数据读取完后自动删除不会留存==

1. **半双工通信**：数据只能在管道的一个方向上流动，具有固定的读端和写端。这意味着在一个时间点上，数据只能在一个方向上被传输，无法同时进行双向通信。
2. **亲缘关系进程间通信**：无名管道通常用于具有亲缘关系的进程之间，如父子进程或兄弟进程之间的通信。一个进程创建管道后，可以通过`fork`()创建子进程，然后父子进程可以通过这个管道进行数据交换。
3. **特殊文件形式**：无名管道可以看作是一种特殊的文件，对于它的读写可以使用普通的`read`、`write`等函数。但它并不属于任何文件系统，不支持如lseek()操作，且只存在于内存中。

### 读写特性

>  -  读管道： 
>     -  管道中有数据
>        -  `read`返回实际读到的字节数。
>     -  管道中无数据：
>        -  管道写端被全部关闭，`read`返回0 (好像读到文件结尾)
>        -  写端没有全部被关闭，`read`阻塞等待(不久的将来可能有数据递达，此时会让出cpu
>  -  写管道： 
>     -  管道读端全部被关闭
>        -  进程异常终止(也可使用捕捉SIGPIPE信号，使进程不终止)
>     -  管道读端没有全部关闭
>        -  管道已满，`write`阻塞。（管道大小64K）
>        -  管道未满，`write`将数据写入，并返回实际写入的字节数。

### pipe函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     int pipe(int pipefd[2]);
>     ```
>
>  2. **功能**：`pipe`函数创建一个管道，用于在两个文件描述符之间进行单向数据传输。一个文件描述符用于读操作，另一个用于写操作。
>
>  3. **参数**：
>
>     -  `int pipefd[2]`: 一个包含两个整数的数组。
>        -  `pipefd[0]`是管道的读端，
>
>        -  `pipefd[1]`是管道的写端。
>
>  4. **返回值**：
>
>     -  成功时，返回0，并在`pipefd`中设置两个文件描述符。
>     -  失败时，返回-1，并设置errno以指示错误类型。
>
>  5. **注意**：
>
>     -  只能用于亲缘关系的进程间通信（父子进程，兄弟进程）
>     -  管道通信是单工的，一端读，一端写（程序实现设计好）。
>     -  数据自己读不能自己写
>     -  管道可以用于大于2个进程共享

### 示例

：一个父进程写，三个子进程读

```c
#include <stdio.h>
#include <unistd.h>
#include <string.h>
int main(){
	int pfd1[2];//文件描述符
	int re,i;
	char buf1[20]={0};
	char buf2[20]={0};
	pid_t pid;
    
    //创建无名管道
	re = pipe(pfd1);
	if(re<0)
	{
		perror("pipe");
		return 0;
	}
    
    //创建三个子进程
	for(i=0; i<3; i++)
	{
		pid = fork();//创建子进程
		if(pid<0)
		{
			perror("fork");
			return 0;
		}
		else if(pid == 0)//如果是子进程就退出循环
			break;
	}

	if(i==3)//父进程
	{
		//close(pfd1[0]);
		while(1)
		{
			strcpy(buf1,"hello world");
			write(pfd1[1],buf1,strlen(buf1));//写入管道

			sleep(1);
		}
	}
	else if(i==0)//子进程0
	{
		//close(pfd1[1]);        
		while(1)
		{
			re=read(pfd1[0],buf2,20);//读取管道数据
			if(re>0)
			{
				printf("0read pfd=%s\n",buf2);
			}    
		}
	}
	else if(i==1)//子进程1
	{
		//close(pfd1[1]);        
		while(1)
		{
			re=read(pfd1[0],buf2,20);//读取管道数据
			if(re>0)
			{
				printf("1read pfd=%s\n",buf2);
			}    
		}
	}
	else if(i==2)//子进程2
	{
		//close(pfd1[1]);        
		while(1)
		{
			re=read(pfd1[0],buf2,20);//读取管道数据
			if(re>0)
			{
				printf("2read pfd=%s\n",buf2);
			}    
		}
	}
}
```



![image-20240506231209071](./11_Concurrent_Programming.assets/image-20240506231209071.png)

## 有名管道（命名管道）

有名管道，也称为命名管道或FIFO（First In First Out），是一种特殊类型的管道，它允许不相关的进程通过文件系统进行通信。与无名管道相比，有名管道的一个显著特点是提供了一个路径名与之关联，从而可以在任意两个进程之间进行通信。

​	==数据读取完后自动删除不会留存==

以下是有名管道的读写特性：

-  **引用计数影响**：当所有指向管道写端的文件描述符都关闭时（即写端引用计数为0），如果有进程尝试从读端读取数据，那么管道中剩余的数据被读取后，再次尝试读取会返回0，类似于读到文件末尾的情况。如果写端的引用计数大于0，即仍有文件描述符指向写端且没有写入数据，那么读端在读取完剩余数据后，再次读取会阻塞，直到有新的数据写入。
-  **信号通知**：当所有指向管道读端的文件描述符都关闭时（即读端引用计数为0），如果有进程试图向管道中写入数据，该进程会收到一个SIGPIPE信号，通常这会导致进程异常终止。
-  **特殊文件形式**：有名管道在文件系统中以特殊文件的形式存在，这意味着它可以通过路径名被访问，并且可以由任何有相应权限的进程使用。这与无名管道只能在亲缘关系的进程间通信不同。
-  **半双工通信**：尽管有名管道允许任意进程间通信，但它仍然是一种半双工通信方式，意味着数据只能在一个方向上流动，不能同时进行读写操作。

**特点：**

1.  有名管道可以使**非亲缘的两个进程互相通信**

2.  通过路径名来操作，在文件系统中可见，但内容存放在内存中

3.  文件IO来操作有名管道

4.  遵循先进先出规则

5.  不支持leek操作

6.  单工读写

### mkfifo函数

>  1. **原型**：
>
>     ```c
>     #include <sys/types.h>
>     #include <sys/stat.h>
>     int mkfifo(const char *pathname, mode_t mode);
>     ```
>
>  2. **功能**：`mkfifo`函数创建一个命名管道（FIFO），该管道存在于文件系统中，可以用于不同进程之间的通信。
>
>  3. **参数**：
>
>     -  `const char *pathname`: 管道的路径名。指定创建的命名管道的路径和文件名。
>
>     -  `mode_t mode`: 指定文件的权限位，如读、写、执行权限。通常使用宏定义，例如 `S_IRUSR | S_IWUSR | S_IRGRP | S_IROTH` 等。
>
>  4. **返回值**：
>
>     -  

### open函数

>  1. **原型**：
>
>     ```c
>     #include <fcntl.h>
>     #include <sys/types.h>
>     #include <sys/stat.h>
>     #include <unistd.h>
>     int open(const char *pathname, int flags, ...);
>     ```
>
>  2. **功能**：`open`函数用于打开一个文件，如果成功，则返回一个文件描述符，该文件描述符可用于后续的读写操作。
>
>  3. **参数**：
>
>     -  `const char *pathname`: 文件的路径名。指定要打开的文件路径。
>     -  `int flags`: 文件打开模式和标志位。常用的标志包括：
>        -  `O_RDONLY`: 只读模式
>        -  `O_WRONLY`: 只写模式
>        -  `O_RDWR`: 读写模式
>        -  `O_CREAT`: 如果文件不存在则创建文件
>        -  `O_NONBLOCK`（非阻塞）可以用|与其他模式连接，不加这个选项表示阻塞
>        -  `O_EXCL`: 与 `O_CREAT` 一起使用，如果文件已存在则返回错误
>        -  `O_TRUNC`: 如果文件存在且可写，则将其长度截断为0
>        -  `O_APPEND`: 每次写操作都会追加到文件的末尾
>     -  `mode_t mode`: 可选参数，仅在使用 `O_CREAT` 标志时需要。指定文件的权限位，例如 `S_IRUSR`（用户读权限）、`S_IWUSR`（用户写权限）、`S_IRGRP`（组读权限）等。
>
>  4. **返回值**：
>
>     -  成功时，返回文件描述符。
>     -  失败时，返回-1，并设置`errno`以指示错误类型。

### 注意事项

1.  **程序不能以O_RDWR(读写)模式打开**FIFO文件进行读写操作，而其行为也未明确定义，因为如一个管道以读/写方式打开，进程可以读回自己的输出，同时我们通常使用FIFO只是为了单向的数据传递

2.  第二个参数中的**选项O_NONBLOCK**，选项O_NONBLOCK表示非阻塞，加上这个选项后，表示open调用是非阻塞的，如果没有这个选项，则表示open调用是阻塞的

3.  **对于以只读方式（O_RDONLY）**打开的FIFO文件，如果open调用是阻塞的（即第二个参数为O_RDONLY），除非有一个进程以写方式打开同一个FIFO，否则它不会返回；如果open调用是非阻塞的的（即第二个参数为O_RDONLY | O_NONBLOCK），则即使没有其他进程以写方式打开同一个FIFO文件，open调用将成功并立即返回。

    对于以只写方式（O_WRONLY）打开的FIFO文件，如果open调用是阻塞的（即第二个参数为O_WRONLY），open调用将被阻塞，直到有一个进程以只读方式打开同一个FIFO文件为止；如果open调用是非阻塞的（即第二个参数为O_WRONLY | O_NONBLOCK），open总会立即返回，但如果没有其他进程以只读方式打开同一个FIFO文件，open调用将返回-1，并且FIFO也不会被打开。 

4.  **数据完整性**,如果有多个进程写同一个管道，使用O_WRONLY方式打开管道，如果写入的数据长度小于等于PIPE_BUF（4K），那么或者写入全部字节，或者一个字节都不写入，系统就可以确保数据决不会交错在一起。

### 示例

**：两个没有亲缘关系的进程通信**

write.c

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <fcntl.h>
#include <string.h>
int main()
{
    int re;
    int fd;
    char buf[32];
    
    //创建管道文件
    re = mkfifo("/myfifo",0666);
    if(re<0)
    {
        perror("mkfifo");
        return 0;
    }
    
    //打开管道文件
    fd = open("/myfifo",O_WRONLY|O_NONBLOCK);//只写非阻塞方式打开
    if(fd<0)
    {
        perror("open");
        return 0;
    }
    printf("after open\n");
    
    //写入管道数据
    while(1)
    {
        fgets(buf,32,stdin);//读取键盘输入
        write(fd,buf,strlen(buf));
    }
}
```

read.c

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <fcntl.h>
#include <string.h>
int main()
{
    int re;
    int fd;
    char buf[32];
    fd = open("/myfifo",O_RDONLY);//只读阻塞方式打开管道文件
    if(fd<0)
    {
        perror("open");
        return 0;
    }
    printf("after open\n");
    
    //读取管道数据
    while(1)
    {     
        re=read(fd,buf,32);
        if(re>0)
            printf("read fifo=%s\n",buf);        
        else if(re==0)//
            exit(0);
    }
}
```

# 12.内存映射

使一个磁盘文件与内存中的一个缓冲区相映射，进程可以像访问普通内存一样对文件进行访问，不必再调用read,write，更加高效。

## 用到的函数

### mmap函数

>  1. **原型**：
>
>     ```c
>     #include <sys/mman.h>
>     void* mmap(void* start, size_t length, int prot, int flags, int fd, off_t offset);
>     ```
>
>  2. **功能**：创建共享内存映射
>
>  3. **参数**：
>
>     -  `addr`：指定要映射的内存地址，一般设置为 NULL 让操作系统自动选择合适的内存地址。
>
>     -  `length`：必须>0。映射地址空间的字节数，它从被映射文件开头 `offset` 个字节开始算起。
>
>     -  `prot`：指定共享内存的访问权限。可取如下几个值的可选：
>
>        -  `PROT_READ`（可读）
>
>        -  `PROT_WRITE`（可写）
>
>        -  `PROT_EXEC`（可执行）
>
>        -  `PROT_NONE`（不可访问）
>
>     -  `flags`：由以下几个常值指定：
>
>        1.  `MAP_SHARED`（共享映射） 
>
>        2.  `MAP_PRIVATE`（私有映射）
>
>        3.  `MAP_FIXED`（表示必须使用 start 参数作为开始地址，如果失败不进行修正）
>
>        4.  `MAP_ANONYMOUS`（匿名映射，用于血缘关系进程间通信）
>
>            其中，`MAP_SHARED` , `MAP_PRIVATE`必选其一，而 `MAP_FIXED` 则不推荐使用。
>
>     -  `fd`：表示要映射的文件句柄。如果匿名映射写-1。
>
>     -  `offset`：表示映射文件的偏移量，一般设置为 0 表示从文件头部开始映射。
>
>  4. **返回值**：
>
>     -  成功返回创建的映射区首地址
>     -  失败返回`MAP_FAILED`，设置errno值

### lseek函数

>  1. **原型**：
>
>     ```c
>     #include <sys/types.h>
>     #include <unistd.h>
>     off_t lseek(int fd, off_t offset, int whence);
>     ```
>
>  2. **功能**：用于在文件中定位文件指针的位置
>
>  3. **参数**：
>
>     -  `fd`：文件描述符，通常是通过`open`或`fopen`函数获得的。
>
>     -  `offset`：相对于`whence`的偏移量，以字节为单位。
>
>     -  `whence`：表示参考点的位置，可以是以下值之一：
>
>        ：表示参考点的位置，可以是以下值之一：
>
>        -  `SEEK_SET`：文件开头
>        -  `SEEK_CUR`：文件当前位置
>        -  `SEEK_END`：文件结尾
>
>  4. **返回值**：
>
>     -  成功时，返回新的文件指针位置。
>     -  失败时，返回-1，并设置`errno`

## 示例1

利用内存映射实现两个无亲缘关系的线程间的通信

write.c

```c
#include <sys/mman.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
int main(int argc, const char *argv[])
{
	void *addr;
	int fd;
	fd = open("test",O_RDWR);
	if(fd<0)
	{
		perror("open");
		return 0;
	}

	//获取文件大小
	int fileSize = lseek(fd, 0, SEEK_END);
	lseek(fd, 0, SEEK_SET); // 将文件指针重置到文件开头

	//映射文件到内存
	addr = mmap(NULL,2000,PROT_READ|PROT_WRITE,MAP_SHARED,fd,0);
	if(addr == MAP_FAILED)
	{
		perror("mmap");
		close(fd);
		return 0;
	}

	//写入文件
	int i = 0;
	printf("%d\n",fileSize);
	while(i < 2000)
	{
		printf("%d\n",i);
		memcpy(addr+i,"5",1);
		sleep(1);
		i++;
	}
	
	return 0;
}
```

read.c

```c
#include <sys/mman.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
int main(int argc, const char *argv[])
{
	void *addr;
	int fd;
	fd = open("test",O_RDWR);
	if(fd<0)
	{
		perror("open");
		return 0;
	}

	//获取文件大小
	int fileSize = lseek(fd, 0, SEEK_END);
	lseek(fd, 0, SEEK_SET); // 将文件指针重置到文件开头

	//映射文件到内存
	addr = mmap(NULL,2000,PROT_READ|PROT_WRITE,MAP_SHARED,fd,0);
	if(addr == MAP_FAILED)
	{
		perror("mmap");
		close(fd);
		return 0;
	}

	//读取
	int i = 0;
	printf("%d\n",fileSize);
	while(i < 2000)
	{
		printf("%s\n",(char *)addr);
		sleep(1);
	}
	
	return 0;
}
```

## 示例2匿名映射

利用内存映射==匿名映射==实现两个亲缘线程间的通信

```c
#include <sys/mman.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <sys/wait.h>

int main(){

    void *addr;
    
    //匿名映射
    addr = mmap(NULL,2048, PROT_READ|PROT_WRITE, MAP_SHARED|MAP_ANONYMOUS, -1, 0);
    if(addr == MAP_FAILED)
    {
        perror("mmap");
        return 0;
    }

    //创建子进程
    pid_t pid;
    pid = fork();
    if(pid<0)
    {
        perror("fork");
        return 0;
    }

    //父进程
    else if(pid>0)
    {
        memcpy(addr,"1234567890",10);
        wait(NULL);//回收子进程
    }

    //子进程
    else 
    {
        sleep(1);
        printf("read father val=%s\n",(char *)addr);
    }
    munmap(addr,2048);//释放内存
}
```

## 注意事项

1.  创建映射区的过程中，隐含着一次对映射文件的读操作，将文件内容读取到映射区。

2.  当MAP_SHARED时，要求：映射区的权限应 <=文件打开的权限(出于对映射区的保护)，如果不满足报非法参数（Invalid argument）错误。

    当MAP_PRIVATE时候，mmap中的权限是对内存的限制，只需要文件有读权限即可，操作只在内存有效，不会写到物理磁盘，且不能在进程间共享。

3.  映射区的释放与文件关闭无关，只要映射建立成功，文件可以立即关闭。

4.  ==用于映射的文件大小必须>0==，当映射文件大小为0时，指定非0大小创建映射区，访问映射地址会报总线错误，指定0大小创建映射区，报非法参数错误（Invalid argument）

5.  文件偏移量必须为0或者4K的整数倍（不是会报非法参数Invalid argument错误）

6.  映射大小可以大于文件大小，但只能访问文件page的内存地址，否则报总线错误 ，超出映射的内存大小报段错误

    ![image-20240508193643371](./11_Concurrent_Programming.assets/image-20240508193643371.png)

7.  ==映射内存大小为4K的整数倍==，若申请映射大小不为4K的整数倍，则会自动向上补齐。例如申请2k，实际可以操作的内存大小为4K。

## System V共享内存

-  共享内存是一种最为高效的进程间通信方式，进程可以直接读写内存，而不需要任何数据的拷贝
-  共享内存在内核空间创建，可被进程映射到用户空间访问，使用灵活
-  由于多个进程可同时访问共享内存，因此需要同步和互斥机制配合使用

**共享内存使用步骤：**

1. 生成key
2. 创建/打开共享内存
3. 映射共享内存，即把指定的共享内存映射到进程的地址空间用于访问
4. 读写共享内存
5. 撤销共享内存映射
6. 删除共享内存对象

![image-20240508194003602](./11_Concurrent_Programming.assets/image-20240508194003602.png)

### ftok函数

>  1. **原型**：
>
>     ```c
>     #include <sys/types.h>
>     #include <sys/ipc.h>
>     key_t **ftok**(const char *pathname, int proj_id);
>     ```
>
>  2. **功能**：生成一个**唯一**的与文件相关的键值（key），这个键值通常用于IPC（进程间通信）中，比如创建共享内存或信号量等。
>
>  3. **参数**：
>
>     -  `pathname`：必须是存在的且可访问的文件路径。它可以是一个普通文件或者目录，但该路径下的某个文件需要具有相应的权限，以便其他进程可以通过该路径访问到相同的键值。
>     -  `proj_id`：是一个8位的整数（即范围在0到255之间）。它与文件的索引节点号结合生成最终的键值。
>
>  4. **返回值**：
>
>     -  如果函数执行成功，会返回一个`key_t`类型的键值
>     -  如果执行失败，则返回-1
>
>  5. **注意**：
>
>     -  确保`pathname`指定的文件存在且可访问。
>     -  `proj_id`的值虽然作为`int`类型传递，但实际上只有8个比特被使用，因此其取值范围是0到255。
>
>     -  生成的键值是由`proj_id`的后8个比特、文件所在设备的最后两位和文件的索引节点号的最后四位组成的

### shmget函数

>  1. **原型**：
>
>     ```c
>     #include <sys/shm.h>
>     int shmget(key_t key, size_t size, int shmflg);
>     ```
>
>  2. **功能**：获取或创建一个共享内存段，并返回一个共享内存标识符。
>
>  3. **参数**：
>
>     -  `key`：共享内存的键值，可以是0（IPC_PRIVATE）以创建一个新的共享内存对象，或者是大于0的32位整数。如果`shmflg`中包含了`IPC_CREAT`标志，且提供的键值已经存在，则会返回现有的共享内存标识符。
>     -  `size`：指定需要共享的内存容量，以字节为单位。
>     -  `shmflg`：权限标志，与`open`函数的`mode`参数类似。如果希望在键值指定的共享内存不存在时创建它，可以与`IPC_CREAT`进行或操作。
>
>  4. **返回值**：
>
>     -  如果成功，`shmget`函数返回一个共享内存标识符，该标识符可以在后续的共享内存相关操作中使用，如`shmat`、`shmdt`和`shmctl`等。
>     -  如果失败，返回-1。
>
>  5. **注意**：
>
>     -  不同进程可以通过`shmget`返回的共享内存标识符访问同一块共享内存。
>     -  当写数据到共享内存时，需要注意同步问题，即在进程间访问共享内存时要加锁，以防止数据竞争。
>     -  在使用共享内存时，应确保结构体中的缓冲区已经声明了足够的大小，而不是仅使用一个指针并在需要时通过`malloc`分配内存，因为这样分配的地址其他进程无法访问

### ipcs 命令

>  1. **格式**：`ipcs [options]`
>  2. **功能**：`ipcs` 命令用于提供系统中进程间通信设施的信息，包括消息队列、共享内存段和信号量。
>  3. **参数**：
>     -  `-a` 或 `--all`：显示所有类型的 IPC 资源信息。
>     -  `-b` 或 `--broad`：显示 IPC 资源的宽泛信息，包括最大允许的尺寸等。
>     -  `-m` 或 `--shmems`：仅显示共享内存段的信息。
>     -  `-q` 或 `--queues`：仅显示消息队列的信息。
>     -  `-s` 或 `--semaphores`：仅显示信号量的信息。
>     -  `-i <id>` 或 `--identifier <id>`：显示特定 IPC 资源 ID 的详细信息。
>     -  `-l` 或 `--limits`：显示系统限制信息。
>     -  `-u` 或 `--summary`：显示 IPC 资源的摘要信息。
>     -  `-p` 或 `--pid`：显示创建 IPC 资源的进程 ID。
>     -  `-t` 或 `--time`：显示最后操作 IPC 资源的时间。
>     -  `-c` 或 `--creator`：显示 IPC 资源的创建者信息。
>     -  `-h` 或 `--human`：以人类可读的格式显示大小。
>     -  `-v` 或 `--version`：显示 `ipcs` 命令的版本信息。
>     -  `-V` 或 `--help`：显示帮助信息。
>  4. **示例**：
>     -  `ipcs -a`：显示所有类型的 IPC 资源信息。
>     -  `ipcs -m`：仅显示共享内存段的信息。
>     -  `ipcs -q`：仅显示消息队列的信息。
>     -  `ipcs -s`：仅显示信号量的信息。
>     -  `ipcs -u`：显示 IPC 资源的摘要信息。
>     -  `ipcs -i 1234`：显示 ID 为 1234 的 IPC 资源的详细信息。

### shmat函数

>  1. **原型**：
>
>     ```c
>     #include <sys/shm.h>
>     void *shmat(int shmid, const void *shmaddr, int shmflg);
>     ```
>
>  2. **功能**：将创建好的共享内存段连接到某个进程，并指定内存空间
>
>  3. **参数**：
>
>     -  `shmid`：是通过`shmget`函数返回的共享内存标识符，它唯一标识了一个共享内存段。
>     -  `shmaddr`：是一个可选参数，用于指定共享内存映射到进程地址空间中的起始地址。如果传递`NULL`，则由系统自动选择一个地址进行映射。
>     -  `shmflg`：是一组标志位，用于控制共享内存的访问权限和其他属性。常见的标志包括`SHM_RDONLY`（只读访问）和`SHM_WRONLY`（只写访问）。
>
>  4. **返回值**：
>
>     -  成功，返回一个指向共享内存段在进程地址空间中映射的起始位置的**指针**。这个指针可以用于访问共享内存中的数据。
>     -  失败，返回-1

### shmdt函数

>  1. **原型**：
>
>     ```c
>     #include <sys/shm.h>
>     int shmdt(const void *shmaddr);
>     ```
>
>  2. **功能**：从进程的地址空间中分离已经附加的共享内存段
>
>  3. **参数**：`shmaddr`是一个指向共享内存段的指针，这个指针通常是通过`shmat`函数返回的。
>
>  4. **返回值**：
>
>     -  成功，`shmdt`函数返回0
>     -  失败，返回 -1，并设置 `errno` 以指示错误原因。

### shmctl函数

>  1. **原型**：
>
>     ```c
>     #include <sys/shm.h>
>     int shmctl(int shmid, int cmd, struct shmid_ds *buf);
>     ```
>
>  2. **功能**：控制共享内存段，包括删除和修改权限等操作
>
>  3. **参数**：
>
>     -  `shmid`：是通过`shmget`函数返回的共享内存标识符，唯一标识了一个共享内存段。
>     -  `cmd`：是控制命令，用于指定要执行的操作
>        -  IPC_STAT（获取共享内存的状态）
>        -  IPC_SET（设置共享内存的状态）
>        -  IPC_RMID（删除共享内存段）
>     -  `buf`：是一个指向`shmid_ds`结构体的指针，用于存储或接收共享内存的状态信息。在某些命令下，如IPC_STAT和IPC_SET，需要提供这个参数。
>
>  4. **返回值**：
>
>     -  成功，`shmctl`函数返回0；
>     -  出错，返回 -1，并设置 `errno` 以指示错误原因

### 示例

创建和使用共享内存

write.c

```c
#include <sys/types.h>
#include <sys/ipc.h>
#include <stdio.h>
#include <sys/shm.h>
#include <string.h>

int main() {
    key_t key;  // 定义一个键值变量
    int shmid;  // 定义一个共享内存标识符变量
    char *buf;  // 定义一个指向共享内存的指针变量

    key = ftok("keytest", 100);  // 生成一个唯一的键值
    if (key < 0) 
    {
        perror("ftok");  
        return 0;
    }
    printf("key=%x\n", key);  // 打印生成的键值

    shmid = shmget(key, 512, IPC_CREAT | 0666);  //创建并获取共享内存段
    if (shmid < 0) 
    {
        perror("shmget");  
        return 0;
    }

    printf("shmid=%d\n", shmid);  // 打印共享内存标识符

    buf = shmat(shmid, NULL, 0);  // 将共享内存段附加到进程的地址空间中
    if (buf < 0) 
    {
        perror("shmat");  
        return 0;
    }

    strcpy(buf, "hello world");  // 将字符串"hello world"复制到共享内存中
}

```

read.c

```c
#include <sys/types.h>
#include <sys/ipc.h>
#include <stdio.h>
#include <sys/shm.h>
#include <string.h>

int main() {
    key_t key;  // 定义一个键值变量
    int shmid;  // 定义一个共享内存标识符变量
    char *buf;  // 定义一个指向共享内存的指针变量

    key = ftok("keytest", 100);  // 生成一个唯一的键值
    if (key < 0) 
    {
        perror("ftok");  
        return 0;
    }
    printf("key=%x\n", key);  // 打印生成的键值

    shmid = shmget(key, 512, 0666);  // 获取共享内存段
    if (shmid < 0) 
    {
        perror("shmget");  
        return 0;
    }

    printf("shmid=%d\n", shmid);  // 打印共享内存标识符

    buf = shmat(shmid, NULL, 0);  // 将共享内存段附加到进程的地址空间中
    if (buf < 0) 
    {
        perror("shmat");  
        return 0;
    }

    printf("read = %s\n",buf);
      
    if(shmdt(buf) == -1)//共享内存段从进程的地址空间中分离
        perror("shmdt");
    if(shmctl(shmid, IPC_RMID, NULL) == -1)//删除共享内存段
        perror("shmctl");
    
    return 0;
}
```

# 13.信号机制

**概念**：

**信号机制是Unix、类Unix以及其他POSIX兼容的操作系统中的一种进程间通讯方式，它允许进程在发生特定事件时接收通知**。

信号机制是操作系统中的一个重要概念，它提供了一种异步的通知机制，用于在进程之间传递消息。信号可以被看作是一种软中断，它们可以在任何时间被发送给一个进程，以通知该进程发生了某个特定的事件。信号的本质是在软件层次上模拟硬件中断的行为，但它完全由软件控制，因此被称为“软中断”。

信号的处理过程通常涉及以下几个步骤：

1. **信号的产生**：当某个事件发生时，如用户按下Ctrl+C，或者程序访问了非法内存地址，操作系统会产生一个信号。
2. **信号的传递**：产生的信号会被操作系统传递给目标进程。这个过程是异步的，意味着信号可以在任何时间点到达。
3. **信号的接收**：进程通过注册信号处理函数来接收和处理信号。当信号到达时，如果进程已经为该信号注册了处理函数，操作系统会调用该函数。
4. **信号的处理**：在信号处理函数中，进程可以决定如何处理信号。常见的处理方式包括忽略信号、采取默认行为（如终止进程）或执行自定义的操作。
5. **信号的屏蔽**：进程可以选择暂时屏蔽某些信号，这样即使在信号产生时也不会被立即处理。这通常用于避免在某些关键操作中被信号中断。

在Linux系统中，信号机制是通过内核实现的。内核负责管理信号的发送和接收，并通过软中断的方式通知进程。进程可以通过系统调用来设置信号处理函数，从而定义对不同信号的响应方式。

## 常用信号

| 信号名    | 代号  | 含义                                                         | 默认操作 |
| --------- | ----- | ------------------------------------------------------------ | -------- |
| SIGHUP    | 1     | 该信号在用户终端关闭时产生，通常是发给和该  终端关联的会话内的所有进程 | 终止     |
| SIGINT    | 2     | 该信号在用户键入INTR字符(Ctrl-C)时产生，内核发送此信号送到当前终端的所有前台进程 | 终止     |
| SIGQUIT   | 3     | 该信号和SIGINT类似，但由QUIT字符(通常是  Ctrl-\)来产生       | 终止     |
| SIGILL    | 4     | 该信号在一个进程企图执行一条非法指令时产生                   | 终止     |
| SIGSEV    | 5     | 该信号在非法访问内存时产生，如野指针、缓  冲区溢出           | 终止     |
| SIGPIPE   | 13    | 当进程往一个没有读端的管道中写入时产生，代  表“管道断裂”     | 终止     |
| SIGKILL   | 9     | 该信号用来结束进程，并且==不能被捕捉和忽略==                 | 终止     |
| SIGSTOP   | 19    | 该信号用于暂停进程，并且==不能被捕捉和忽略==                 | 暂停进程 |
| SIGTSTP   | 20    | 该信号用于==暂停进程==，用户可键入SUSP字符(  通常是Ctrl-Z)发出这个信号 | 暂停进程 |
| SIGCONT   | 18    | 该信号让进程==进入运行态==                                   | 继续运行 |
| SIGALRM   | 14    | 该信号用于通知进程定时器时间已到                             | 终止     |
| SIGUSR1/2 | 10/12 | 该信号保留给用户程序使用                                     | 终止     |
| SIGCHLD   | 17    | 是子进程状态改变发给父进程的。                               | 忽略     |

`kill -l` 命令查看所有信号

![image-20240509163800152](./11_Concurrent_Programming.assets/image-20240509163800152.png)

## 用到的命令

### Kill 命令

>  1. **格式**：`kill [参数] [进程号]`
>  2. **功能**：Linux中的kill命令的功能是向指定进程发送信号以终止该进程的运行
>  3. **参数**：
>     -  -[信号名或代号]，例 `kill -9 pid` 结束进程pid
>     -  **`-l`（小写L）**：列出所有可用的信号名称。如果不加信号编号，使用此参数会显示全部信号。
>     -  **`-s`**：指定要发送的信号的名称或编号。这允许用户选择发送不同的信号到进程。
>     -  **`-a`**：当处理当前进程时，不限制命令名和进程号的对应关系。这在批量脚本中尤其有用。
>     -  **`-p`**：指定kill命令只打印相关进程的进程号，而不发送任何信号。
>     -  **`-u`**：指定用户。这个参数用来限定只向特定用户的进程发送信号
>

### killall 命令

>  1. **格式**：**killall [参数] [进程名]**
>  2. **功能**：**结束所有与给定名称匹配的运行中的进程**。这可以简化操作，因为用户不需要先查找进程ID（PID），再使用kill命令来终止进程。
>  3. **参数**：
>     -  **`-e | --exact`**：要求进程名与指定名称完全匹配。
>     -  **`-I | --ignore-case`**：在匹配进程名时忽略大小写。
>     -  **`-g | --process-group`**：结束整个进程组而不仅仅是单个进程。
>     -  **`-i | --interactive`**：在结束进程前询问用户确认，实现交互式操作。
>     -  **`-l | --list`**：列出所有已知的信号名称。
>     -  **`-q | --quiet`**：在进程没有结束时不输出任何信息。
>     -  **`-s | --signal`**：发送指定的信号到进程，默认为SIGTERM。
>     -  **`-v | --verbose`**：报告信号是否成功发送到每个匹配的进程。
>     -  **`-w | --wait`**：等待每个被发送信号的进程终止
>

## 发送信号的函数

### kill 函数

>  1. **原型**：
>
>     ```c
>     #include <signal.h>
>     int kill(pid_t pid, int sig)
>     ```
>
>  2. **功能**：指定的进程或进程组发送信号
>
>  3. **参数**：
>     -  `pid`：指定要接收信号的进程或进程组
>        -  `pid > 0`：信号将被发送到进程ID为`pid`的进程。
>        -  `pid = 0`：信号将被发送到与调用`kill()`的进程属于同一个进程组的所有进程。
>        -  `pid = -1`：信号将被广播发送到系统中所有调用进程有权发送信号的进程，除了进程1（init）。
>        -  `pid < -1`：信号将被发送到以`-pid`为进程组标识的所有进程。
>     -  `int sig`：指定要发送的信号的编号。不同的信号编号代表不同的信号，例如SIGTERM代表终止信号，SIGKILL代表强制终止信号。
>
>  4. **返回值**：
>     -  成功，返回0
>     -  失败，返回非零数
>

### alarm 函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     unsigned int alarm(unsigned int seconds);
>     ```
>
>  2. **功能**：设置一个**定时器**，在定时器到期时向调用进程发送SIGALRM信号
>
>  3. **参数**：`seconds`：指定定时器的**秒**数。如果`seconds`为0，则取消之前设置的定时器，并返回剩余的时间片。如果`seconds`非零，则设置一个新的定时器，定时器到期时，将向调用`alarm`函数的进程发送SIGALRM信号。
>
>  4. **返回值**：
>     -  上一个定时器的剩余时间（以秒为单位）
>     -  如果没有设置过定时器，则返回值为0
>
>  5. **注意**：如果在定时器到期前再次调用`alarm`函数设置了新的定时器，那么原来的定时器会被取消，新的定时器将从当前时间开始计时。
>

### pause 函数

>  1. **原型**：
>
>     ```c
>     #include <stdlib.h>
>     int pause(void);
>     ```
>
>  2. **功能**：**暂停程序**执行，直到接收到一个信号
>
>  3. **参数**：无
>
>  4. **返回值**：
>     -  如果成功，则返回-1
>     -  如果发生错误，则返回-1并设置errno为相应的错误代码
>

### ualarm 函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     int ualarm(unsigned int seconds, int interval);
>     ```
>
>  2. **功能**：设置一个定时器，在定时器到期时向进程发送SIGALRM信号
>
>  3. **参数**：
>
>     -  `seconds`：指定定时器的秒数。如果`seconds`为0，则取消之前设置的定时器。
>     -  `interval`：指定间隔时间（以jiffies为单位）。如果`interval`为0或负数，则表示只执行一次定时器。
>
>  4. **返回值**：
>     -  成功，返回0
>     -  失败，返回-1
>
>  5. **注意**：`ualarm`函数已经被标记为已废弃（deprecated），因为它的行为在不同的系统和平台上可能不一致。在新的代码中，建议使用`setitimer`函数来代替`ualarm`函数，以实现更可靠和可移植的定时器功能。
>

### setitimer函数

>  1. **原型**：
>
>     ```c
>     #include <sys/time.h>
>     int setitimer(int which, const struct itimerval *new_value, struct itimerval *old_value);
>     ```
>
>  2. **功能**：设置一个定时器，该定时器在到期时会向调用进程发送一个SIGALRM信号
>
>  3. **参数**：
>
>     -  `which`：指定定时器类型，可以是以下三种之一：
>        -  `ITIMER_REAL`：基于真实的时间间隔（Real timer），即按照日历时间来计算。
>        -  `ITIMER_VIRTUAL`：基于虚拟的时间间隔（Virtual timer），即按照进程在用户态消耗的CPU时间来计算。
>        -  `ITIMER_PROF`：基于进程时间（Profiling timer），即按照进程在用户态和内核态消耗的CPU时间来计算。
>     -  `new_value`：指向`struct itimerval`结构的指针，该结构定义了定时器的间隔时间和总时间。如果这个指针为NULL，则定时器被取消。
>     -  `old_value`：指向`struct itimerval`结构的指针，用于存储之前的定时器设置。如果这个指针为NULL，则不保存之前的定时器设置。
>
>  4. **结构体**：
>
>     -  ```c
>        struct itimerval {
>            struct timeval it_interval; /* 定时器触发的时间间隔 */
>            struct timeval it_value;    /* 第一次触发定时器的时间 */
>        };
>        ```
>
>     -  ```c
>        struct timeval {
>            time_t tv_sec; // 秒数
>            suseconds_t tv_usec; // 微秒数
>        };
>        ```
>
>  5. **返回值**：
>
>     -  成功，返回0
>     -  失败，返回-1
>
>  6. **注**：可以通过调用`gettimeofday(&t_start, NULL)`来获取当前时间，并将其存储在`timeval`结构体变量`t_start`中。
>

## 捕获信号

### 捕获流程

![image-20240509205244211](./11_Concurrent_Programming.assets/image-20240509205244211.png)

### signal函数

>  1. **原型**：
>
>     ```c
>     #include <signal.h>
>     typedef void (*sighandler_t)(int);` 
>     sighandler_t signal(int signum, sighandler_t handler);
>     ```
>
>  2. **功能**：设置指定信号的处理函数
>
>  3. **参数**：
>
>     -  `signum`：指定要处理的信号的编号。
>     -  `handler`：指向信号处理函数的指针。如果设置为SIG_IGN，则忽略该信号；如果设置为SIG_DFL，则采用系统默认处理方式。
>
>  4. **返回值**：返回先前为指定信号设置的处理函数指针，或者如果之前没有设置处理函数，则返回SIG_DFL
>
>  5. 注：`typedef void (*sighandler_t)(int);` 
>
>     -  `(*sighandler_t)` 表示定义了一个名为 `sighandler_t` 的函数指针类型。这个函数指针类型指向的函数返回值为 `void`，即没有返回值，同时接受一个整型参数 `int`，通常用于传递信号编号
>
>  6. 示例
>
>     捕捉`SIGINT`信号，打印"I cath the SIGINT \n"
>
>     程序执行后按**ctrl +c** 打印“I cath the SIGINT \n”后信号功能复原，再按一次**ctrl +c**，程序退出
>
>     ```c
>     #include <signal.h>
>     #include <stdio.h>
>     #include <unistd.h>
>     #include <stdlib.h>
>     #include <string.h>
>     #include <linux/posix_types.h>
>           
>     typedef void (*sighandler_t)(int);
>           
>     sighandler_t oldact;
>           
>     void handle(int sig){
>         printf("I cath the SIGINT \n");
>         signal(SIGINT,oldact);//回复信号原来的功能
>     }
>           
>     int main(){
>           
>         oldact = signal(SIGINT,handle);//设定信号执行的函数，并将原来的处理信号的函数指针赋值给oldact
>         while(1){
>             sleep(1);
>         }
>           
>     ```
>

### sigaction函数

>  1. **原型**：
>
>     ```c
>     #include <signal.h>
>     int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);
>     ```
>
>  2. **功能**：
>
>     -  `sigaction` 函数用于设置指定信号 `signum` 的信号处理函数、信号集和标志。
>     -  它允许更细致的控制信号的行为，比 `signal` 函数提供了更多的选项。
>
>  3. **参数**：
>
>     -  `signum`：要处理的信号编号。
>     -  `act`：指向 `sigaction` 结构体的指针，该结构体定义了新的信号处理行为。
>     -  `oldact`：指向 `sigaction` 结构体的指针，用于存储原来的信号处理行为。如果不需要保存旧的行为，可以设置为 `NULL`。
>
>  4. **返回值**：
>
>     -  成功时返回 0
>     -  失败时返回 -1
>
>  5. **`sigaction` 结构体**：
>
>     ```c
>     struct sigaction {
>         void (*sa_handler)(int);      // 信号处理函数
>         void (*sa_sigaction)(int, siginfo_t *, void *); // 带有额外信息的信号处理函数（POSIX标准）
>         sigset_t sa_mask;             // 信号集，指定在处理信号时阻塞哪些信号
>         int sa_flags;                 // 影响信号处理行为的标志
>     };
>     ```
>
>     -  `sa_flags` 标志
>        -  `SA_NOCLDSTOP`：如果子进程被终止，父进程不会成为停止状态。
>        -  `SA_ONSTACK`：使用用户栈中的额外空间执行信号处理函数。
>        -  `SA_RESTART`：在信号处理完成后，重启被中断的系统调用。
>        -  `SA_RESETHAND`：在信号处理结束后，将信号处理函数重置为默认行为。
>        -  `SA_SIGINFO`：使用 `sa_sigaction` 成员而不是 `sa_handler` 作为信号处理函数，以提供额外的信号信息。
>
>  6.  **注意**：
>
>      -  不同的操作系统可能对信号的支持不同，因此在使用前应检查操作系统的文档。
>      -  在多线程环境中，信号处理可能会被任何线程接收，因此信号处理函数应设计为异步安全的。
>      -  当一个信号被捕获时，它的默认行为会被禁止，除非在 `sa_handler` 或 `sa_sigaction` 中明确指定。
>

### sigemptyset函数

>  1. **原型**：
>
>     ```c
>     #include <signal.h>
>     int sigemptyset(sigset_t *set);
>     ```
>
>  2. **功能**：初始化或**清空信号集**
>
>  3. **参数**：
>     -  `set`：指向要清空的信号集的指针。
>
>  4. **返回值**：
>     -  成功时返回0
>     -  失败时返回-1
>

## 示例

**使用信号回收子进程**

-  信号 17）`SIGCHLD` 是子进程状态改变发给父进程的。  

-  `SIGCHLD`的产生条件
1. 子进程终止时
   
2. 子进程接收到SIGSTOP信号停止时
   
3. 子进程处在停止态，接受到SIGCONT后唤醒时

```c
#include <stdio.h>
#include <signal.h>
#include <sys/wait.h>
#include <unistd.h>
#include <stdlib.h>

// 信号处理函数
void handle(int sig){
    wait(NULL); // 等待子进程结束
    printf("Get sig =%d\n",sig); // 输出接收到的信号编号
}

int main()
{
    pid_t pid; // 用于存储fork()函数的返回值
    struct sigaction myact; // 用于存储信号处理结构体
    myact.sa_handler = handle; // 设置信号处理函数为handle
    myact.sa_flags = 0; // 设置信号处理标志为0

    sigemptyset(&myact.sa_mask); // 清空信号集

    pid = fork(); // 创建子进程

    // 父进程部分
    if(pid>0)
    {
        //wait(NULL);
        sigaction(SIGCHLD,&myact,NULL); // 设置SIGCHLD信号的处理函数为handle
        while(1)
        {
            printf("this is father process\n"); // 输出信息
            sleep(1); // 休眠1秒
        }
    }

    // 子进程部分
    else if(pid==0)
    {
        sleep(5); // 休眠5秒
        exit(0); // 退出子进程
    }
}
```

---

## 信号阻塞和信号集

有时候不希望在接到信号时就立即停止当前执行，去处理信号，同时也不希望忽略该信号，而是延时一段时间去调用信号处理函数。这种情况可以通过阻塞信号实现。

**概念**：信号的”阻塞“是一个开关动作，指的是**阻止**信号被处理，但不是阻止信号产生。

信号的状态：

-  信号递达（Delivery ）：实际信号执行的处理过程(3种状态：忽略，执行默认动作，捕获)
-  信号未决（Pending）：从产生到递达之间的状态

### 信号集

信号集是一组用于表示信号的集合，它包含了一组信号的状态，通常用来表示这些信号是否已经发生或被处理

### 信号集操作函数

1. `sigset_t set;` 自定义信号集。 是一个32bit 64bit 128bit的数组。
2. `sigemptyset(sigset_t *set);`   清空信号集
3. `sigfillset(sigset_t *set);`  全部置1 
4. `sigaddset(sigset_t *set, int signum);`    将第signum个信号添加到集合中
5. `sigdelset(sigset_t *set, int signum);`    将第signum个信号从集合中移除
6. `sigismember（const sigset_t *set，int signum);` 判断一个信号是否在集合中。

### 信号屏蔽函数

#### sigprocmask函数

>  1. **原型**：
>
>     ```c
>     #include <signal.h>
>     int sigprocmask(int how, const sigset_t *set, sigset_t *oldset);
>     ```
>
>  2. **功能**：设定对信号集内的信号的处理方式(阻塞或不阻塞)
>
>  3. **参数**：
>
>     -  how：指定函数的行为，可以是以下值之一：
>
>        -  `SIG_BLOCK`：将参数`set`中的信号加入到进程的信号屏蔽字中。
>
>        -  `SIG_UNBLOCK`：从进程的信号屏蔽字中移除参数`set`中的信号。
>
>        -  `SIG_SETMASK`：将参数`set`设置为进程的信号屏蔽字。
>
>        -  `SIG_MASK`：清空信号集，然后加入原来的信号屏蔽字。
>
>        -  `SIG_BLOCKSET`：类似于`SIG_BLOCK`，但不会改变原来的信号屏蔽字。
>
>        -  `SIG_UNBLOCKSET`：类似于`SIG_UNBLOCK`，但不会改变原来的信号屏蔽字。
>
>     -  `set`：指向`sigset_t`类型的指针，包含了要添加到信号屏蔽字中的信号集合。
>
>     -  `oldset`：指向`sigset_t`类型的指针，用于存放调用前的信号屏蔽字。
>
>  4. **返回值**：
>
>     -  成功时返回0，
>     -  失败时返回-1，并设置`errno`为错误码。
>

### 示例

`SIGINT`信号的处理和屏蔽

在刚开始运行时的5秒内按下`ctrl+c`终端没有反应，五秒结束后信号屏蔽解除，终端打印`"I get sig=%d\n"`

```c
#include <signal.h>
#include <stdio.h>   
#include <stdlib.h>
#include <unistd.h>

void handle(int sig)  // 定义一个信号处理函数，参数为接收到的信号值
{
    printf("I get sig=%d\n", sig);  // 打印接收到的信号值
}

int main()  // 主函数
{
    struct sigaction act;  // 定义一个sigaction结构体变量act
    act.sa_handler = handle;  // 设置信号处理函数为handle
    sigemptyset(&act.sa_mask);  // 初始化清空信号集
    act.sa_flags = 0;  // 设置标志位为0
    sigaction(SIGINT, &act, NULL);  // 将SIGINT信号的处理方式设置为act

    sigset_t set;  // 定义一个信号集变量set
    sigemptyset(&set);  // 初始化清空信号集
    sigaddset(&set, SIGINT);  // 将SIGINT信号添加到信号集中

    sigprocmask(SIG_BLOCK, &set, NULL);  // 阻塞信号集set中的信号（即禁止接收SIGINT信号）
    sleep(5);  // 暂停执行5秒
    sigprocmask(SIG_UNBLOCK, &set, NULL);  // 解除对信号集中的信号的阻塞（即允许接收SIGINT信号）

    while (1) 
    {
        sleep(1);  
    }
}
```

### 信号阻塞函数

#### pause函数

>  1. **原型**：
>
>     ```c
>     #include <unistd.h>
>     int pause(void);
>     ```
>
>  2. **功能**：`pause`函数使当前进程进入睡眠状态，直到收到一个信号为止。在睡眠期间，进程不会占用CPU资源。
>
>  3. **返回值**：
>     -  如果成功，则返回-1，并将errno设置为EINTR（表示被中断）。
>     -  如果失败，则返回-1，并将errno设置为错误码。
>

#### sigsuspend函数

>  1. **原型**：
>
>     ```c
>     #include <signal.h>
>     int sigsuspend(const sigset_t *mask);
>     ```
>
>  2. **功能**：
>     -  `sigsuspend`函数使当前进程进入睡眠状态，直到收到一个信号为止。在睡眠期间，进程不会占用CPU资源。
>     -  与`pause`函数不同，`sigsuspend`函数可以指定要阻塞的信号集，即在等待信号时，哪些信号应该被阻塞。
>
>  3. **参数**：
>     -  `mask`：指向`sigset_t`类型的指针，表示**要阻塞的信号集**。如果为NULL，则不阻塞任何信号
>
>  4. **返回值**：
>     -  如果成功，则返回-1，并将errno设置为EINTR（表示被中断）
>     -  如果失败，则返回-1，并将errno设置为错误码
>

### 示例

>  **展示两个方法阻塞信号**
>
>  -  法一：通过sigprocmask和pause实现
>  -  法二：通过sigsuspend实现
>
>  ```c
>  #include <signal.h>  // 引入信号处理相关的头文件
>  #include <stdio.h>   // 引入标准输入输出相关的头文件
>  #include <stdlib.h>  // 引入标准库函数相关的头文件
>  #include <unistd.h>  // 引入Unix系统调用相关的头文件
>  
>  void handle(int sig)  // 信号处理函数
>  {
>      printf("I get sig=%d\n", sig);  // 打印接收到的信号值
>  }
>  
>  void mytask()  // 任务函数
>  {
>      printf("My task start\n");  // 打印任务开始信息
>      sleep(3);  // 暂停3秒
>      printf("My task end\n");  // 打印任务结束信息
>  }
>  
>  int main()  // 主函数
>  {
>      struct sigaction act;  // 定义一个sigaction结构体变量act
>      act.sa_handler = handle;  // 设置信号处理函数为handle
>      act.sa_flags = 0;  // 设置标志位为0
>      sigemptyset(&act.sa_mask);  // 初始化清空信号集
>      sigaction(SIGINT, &act, NULL);  // 将SIGINT信号的处理方式设置为act
>      sigaction(SIGHUP, &act, NULL);  // 将SIGHUP信号的处理方式设置为act
>  
>      sigset_t set, set2;  // 定义两个信号集变量set和set2
>      sigemptyset(&set2);  // 初始化清空set2信号集
>      sigaddset(&set, SIGHUP);  // 将SIGHUP信号添加到信号集set中
>      sigaddset(&set, SIGINT);  // 将SIGINT信号添加到信号集set中
>  
>      pause();  // 暂停执行，等待信号的到来
>  
>      while (1)  // 无限循环
>      {
>          sigprocmask(SIG_BLOCK, &set, NULL);  // 阻塞信号集中的信号（即禁止接收SIGHUP和SIGINT信号）
>          mytask();  // 执行自定义的任务函数
>          /*法一*/
>          //sigprocmask(SIG_UNBLOCK,&set,NULL);  // 解除对信号集中的信号的阻塞（即允许接收SIGHUP和SIGINT信号）
>          //pause();//暂停执行，等待信号的到来
>          /*法二*/
>          sigsuspend(&set2);  // 挂起进程，直到收到信号为止
>      }
>  
>      printf("After pause\n");  // 打印"After pause"信息
>  
>      while (1)  // 无限循环
>      {
>          sleep(1);  // 每次循环暂停执行1秒
>      }
>  }
>  ```
>
>  具体分析：
>
>  -  **`sigprocmask`**：这个函数用于修改进程的信号屏蔽字，即它可以阻止某些信号的传递。当设置了信号屏蔽字后，指定的信号将不会传递给进程，直到取消屏蔽。这种方法的缺点是，如果在取消屏蔽和调用`pause`之间有信号发生，那么这个信号可能会丢失。
>  -  **`pause`**：这个函数会使进程进入睡眠状态，直到收到一个信号。如果在使用`sigprocmask`解除信号屏蔽之后调用`pause`，在这两个调用之间的时间窗口内发生的信号可能会导致`pause`永远挂起，因为`pause`只有在接收到信号后才会返回。
>  -  **`sigsuspend`**：这个函数结合了`sigprocmask`和`pause`的功能，它在解除信号屏蔽的同时使进程进入睡眠状态。这是一个原子操作，意味着它保证了在信号解除屏蔽和等待信号之间不会有时间窗口，从而避免了信号丢失的问题。因此，如果需要等待某个信号，建议使用`sigsuspend`而不是单独使用`sigprocmask`和`pause`。
>
>  总的来说，`sigprocmask`主要用于改变信号屏蔽字，`pause`用于等待任何信号的到来，而`sigsuspend`则是在等待特定信号时使用的更为安全的方法，因为它可以保证在等待期间不会错过任何信号

# 14.消息队列

消息队列（Message Queue）是一种**通信机制，用于在分布式系统中传递和管理消息的队列型数据结构**。

-  消息队列通常是一个先进先出（FIFO）的数据结构，它允许多个进程或线程之间以异步方式进行通信。
-  它可以被看作是一个系统内核中的内部链表，其中发送进程将消息添加到队列中，接收进程从队列中读取消息进行处理。
-  这种通信机制传递的数据通常是结构化的，而不是简单的字节流。

![img](./11_Concurrent_Programming.assets/clip_image002.jpg)

## 消息队列使用步骤

-  发送端：
   1. 申请Key ftok
   2. 打开/创建消息队列  msgget
   3. 向消息队列发送消息  msgsnd
-  接收端：
   1. 打开/创建消息队列  msgget
   2. 从消息队列接收消息  msgrcv
   3. 控制（删除）消息队列  msgctl

## 发送端

### 1.申请Key

>  **ftok函数**
>
>  1. **原型**：`key_t ftok(const char *pathname, int proj_id);`
>  2. **功能**：生成一个唯一的键值，通常用于创建共享内存或消息队列等
>  3. **参数**：
>     -  `pathname`：一个已经存在的文件路径，通常是程序中已经打开的文件。
>     -  `proj_id`：一个整数，用于与pathname组合生成唯一的键值。
>  4. **返回值**：
>     -  成功，返回一个key_t类型的键值，用于后续的系统调用（如shmget、msgget等）
>     -  失败，返回-1

### 2.打开/创建消息队列  

>  **msgget函数**
>
>  1. **原型**：`int msgget(key_t key, int msgflg);`
>  2. **功能**：创建或打开一个消息队列
>  3. **参数**：
>     -  `key`：一个唯一的键值，通常由ftok函数生成。
>     -  **`msgflg`**：消息队列的访问权限和创建标志,IPC_CREAT、IPC_EXCL、IPC_NOWAIT等
>        1. `IPC_CREAT`
>           -  若消息队列不存在，则创建一个新的消息队列。
>        2. `IPC_EXCL`
>           -  当与 `IPC_CREAT` 同时使用时，如果已经存在与 `key` 相关联的消息队列，`msgget` 会失败并返回 -1。
>           -  如果没有同时指定 `IPC_CREAT`，`IPC_EXCL` 将被忽略。
>        3. `IPC_NOWAIT`
>           -  如果消息队列不能立即创建（例如，由于资源限制），`msgget` 会立即返回 -1，而不是等待直到可以创建为止。
>           -  如果没有指定 `IPC_NOWAIT`，系统会等待直到可以创建消息队列为止。
>        4. `IPC_PRIVATE`
>           -  在 Linux 中，`IPC_PRIVATE` 是一种特殊的键值，它允许不使用 `ftok` 来生成唯一的 `key`。
>           -  使用 `IPC_PRIVATE` 作为 `key` 时，系统会为调用者分配一个私有的、唯一的消息队列标识符。
>        5. `IPC_RMID`
>           -  这是一个较新的选项，用于删除所有关联的消息队列，而不仅仅是与给定 `key` 关联的那个。
>        6. `0666` 或 `0777` 等权限位
>           -  这些数字代表消息队列的访问权限。通常，它们被设置为八进制数，其中每个数字对应于用户、组和其他人的读/写权限。
>  4. **返回值**：
>     -  成功，返回消息队列的标识符（非负整数）
>     -  失败，返回-1
>  5. 向消息队列发送消息  **msgsnd函数**
>     1. **原型**：`int msgsnd(int msqid, const struct msgbuf *msgp, int msgsz, int msgflg);`
>     2. **功能**：向消息队列发送一个消息
>     3. **参数**：
>        -  `msqid`：消息队列的标识符，通常由msgget函数返回。
>        -  `msgp`：指向要发送的消息结构的指针，该结构包含消息类型和消息正文。
>        -  `msgsz`：消息的大小（以字节为单位）。
>        -  `msgflg`：指定消息发送的标志，可以是`IPC_NOWAIT`等,同`msgget`函数中的`msgflg`参数。
>     4. **返回值**：
>        -  成功，返回0
>        -  失败，返回-1

### 示例

发送一条消息到队列

```c
#include <stdio.h> 
#include <sys/ipc.h> 
#include <sys/msg.h> 
#include <string.h> 

typedef struct // 定义一个结构体类型
{
    long msg_type; // 消息类型，必须是long型
    char buf[128]; // 消息内容缓冲区，用于存储消息内容
}msgT;    

#define MSGLEN  (sizeof(msgT)-sizeof(long)) // 定义消息长度宏，减去长整型变量的大小

int main() // 主函数
{
    key_t key; // 定义一个key_t类型的变量key，用于存储生成的键值
    int msgid; // 定义一个整型变量msgid，用于存储消息队列的标识符
    int ret;   // 定义一个整型变量ret，用于存储函数调用的返回值
    msgT msg;  // 定义一个msgT类型的结构体变量msg，用于存储要发送的消息
    
	//1.生成一个键值
    key = ftok(".",100); // "."表示当前目录，100是一个任意的整数
    if(key<0) // 如果生成键值失败
    {
        perror("ftok"); // 打印错误信息并返回0
        return 0;
    }
    
    //2.函数创建
    msgid = msgget(key,IPC_CREAT|0666); // key为键值，IPC_CREAT表示如果不存在则创建，0666表示权限设置为可读写
    if(msgid<0) // 如果获取消息队列失败
    {
        perror("msgget"); // 打印错误信息并返回0
        return 0;
    }
	
    //3.发送消息
    msg.msg_type = 1; // 设置消息类型为1
    strcpy(msg.buf,"this msg type 1"); // 将消息内容复制到buf中
    ret = msgsnd(msgid,&msg,MSGLEN,0);//0表示不等待
    if(ret<0) // 如果发送消息失败
    {
        perror("msgsnd"); // 打印错误信息并返回0
        return 0;
    }
}
```

运行程序后，在终端输入`ipcs -qc`查看消息队列

![image-20240510183418056](./11_Concurrent_Programming.assets/image-20240510183418056.png)

这就是刚刚发送到消息队列的信息

## 接收端

1. 打开/创建消息队列  **msgget函数**（发送端用过）

### 从消息队列接收消息  

>  **msgrcv函数**
>
>  1. **原型**：`ssize_t msgrcv(int msqid, void *msgp, size_t msgsz, long msgtyp, int msgflg);`
>  2. **功能**：从消息队列中接收一个消息
>  3. **参数**：
>     -  `msqid`：消息队列的标识符，通常由msgget函数返回。
>     -  `msgp`：指向要接收的消息结构的指针，该结构包含消息类型和消息正文。
>     -  `msgsz`：消息的大小（以字节为单位）。
>     -  `msgtyp`：**指定要接收的消息类型**。
>        -  msgtyp=0：收到的第一条消息，任意类型。
>        -  msgtyp>0：收到的第一条 msg_typ类型的消息。
>        -  msgtyp<0：接收类型等于或者小于msgtyp绝对值的第一个消息。
>     -  `msgflg`：
>        -  `0`：阻塞式接收消息
>        -  `IPC_NOWAIT`：如果没有返回条件的消息调用立即返回，此时错误码为ENOMSG
>        -  `MSG_EXCEPT`：与msgtyp配合使用返回队列中第一个类型不为msgtyp的消息
>  4. **返回值**：
>     -  成功时返回实际接收到的消息大小（以字节为单位）
>     -  失败时返回-1
>  5. 控制（删除）消息队列  **msgctl函数**
>     1. **原型**：`int msgctl(int msqid, int cmd, struct msqid_ds *buf);`
>     2. **功能**：控制消息队列，包括获取和设置消息队列的属性
>     3. **参数**：
>        -  `msqid`：消息队列的标识符，通常由`msgget`函数返回。
>        -  `cmd`：指定要执行的命令，可以是`IPC_STAT`、`IPC_SET`等。
>        -  `buf`：指向一个`msqid_ds`结构体的指针，用于存储或设置消息队列的属性。
>     4. **返回值**：
>        -  成功时返回0
>        -  失败时返回-1

### 示例

>  接收消息队列的一条消息
>
>  ```c
>  #include <stdio.h> 
>  #include <sys/ipc.h> 
>  #include <sys/msg.h> 
>  #include <string.h> 
>  
>  // 定义消息结构体
>  typedef struct
>  {
>      long msg_type; // 消息类型
>      char buf[128]; // 消息内容缓冲区
>  }msgT;    
>  
>  #define MSGLEN  (sizeof(msgT)-sizeof(long)) // 计算消息长度，减去长整型成员的长度
>  int main()
>  {
>      int msgid; // 消息队列ID
>      key_t key; // 消息队列关键字
>      msgT msg; // 消息结构体实例
>      int ret; // 返回值
>      key = ftok(".",100); // 生成消息队列关键字
>      if(key<0)
>      {
>          perror("ftok"); // 如果生成失败，打印错误信息
>          return 0;
>      }
>  
>      // 1.创建或获取消息队列ID
>      msgid = msgget(key,IPC_CREAT|0666); 
>      if(msgid<0)
>      {
>          perror("msgget"); // 如果获取失败，打印错误信息
>          return 0;
>      }
>  
>      // 2.从消息队列中接收消息
>      ret = msgrcv(msgid,&msg,MSGLEN,0,0); 
>      if(ret<0)
>      {
>          perror("msgrcv"); // 如果接收失败，打印错误信息
>          return 0;
>      }
>      
>      printf("receiv msg type=%d,buf=%s\n",(int)msg.msg_type,msg.buf); // 打印接收到的消息类型和内容
>  
>       // 3.删除消息队列
>      ret = msgctl(msgid,IPC_RMID,NULL);
>      if(ret<0)
>      {
>          perror("msgctl"); // 如果删除失败，打印错误信息
>          return 0;
>      }
>  }
>  ```
>



# 15.信号灯(信号量)

Linux中的==信号灯即信号量==**是一种用于进程间同步或互斥的机制**，它主要用于控制对共享资源的访问。

在Linux系统中，信号灯作为一种进程间通信（IPC）的方式，与其他如管道、FIFO或共享内存等IPC方式不同，信号灯主要用于同步或互斥控制，以确保多个进程之间能够协调地访问共享资源。信号灯可以看作是内存中的一个标志，**进程可以通过检查信号灯的状态来决定是否可以安全地访问某些共享资源**。

## 要点

1. **创建和操作**：要在Linux中使用信号灯，首先需要创建一个信号灯，并指定其初始值。对于二值信号灯，这个初始值通常是1，表示资源一开始是可用的。
2. **等待和释放**：进程可以执行等待操作来测试信号灯的值，如果信号灯的值大于0，则进程可以继续执行并将信号灯的值减1；如果值为0，则进程必须等待直到信号灯的值变为正数。
3. **共享资源控制**：信号灯提供了一种机制，使得进程能够根据信号灯的状态来判断是否可以访问某些共享资源。这类似于互斥锁，确保了在任何时候只有一个进程可以访问临界区。
4. **进程间通信**：除了同步和互斥，信号灯还可以作为进程间通信的一种方式。它们存在于内核空间，与共享内存和消息队列一起构成了Linux中主要的IPC通信方式。
5. **==有名信号灯==**： 可以通过路径名在进程间共享，因此不同进程可以通过已知的路径名来访问同一个有名信号灯。 适用于需要在不同进程间进行同步的场景。
6. **==无名信号灯==**： 只能存在于内存中，因此使用无名信号灯的进程必须能够访问相同的内存区域。通过共享内存的方式创建，不依赖于文件系统中的路径名。适用于单进程内多线程间的同步或在已经映射相同内存内容的多个进程之间的同步。
7. **==System V信号灯==**：Linux支持System V的信号灯，这是一种传统的信号灯实现，用于在同一系统内的进程间进行同步和互斥。

**互斥和同步** 是信号灯通常用于解决并发中的两个主要问题：

1. **互斥**：确保当一个进程使用共享资源时，其他进程不能同时访问该资源。例如，打印设备只能由一个进程使用，其他尝试访问打印设备的进程必须等待，直到当前进程完成打印任务。

2. **同步**：确保进程间的执行顺序符合特定的依赖关系。例如，一个进程生成数据，另一个进程消费这些数据，消费者进程需要等待生产者进程生成数据后才能继续执行。

## PV操作

**信号灯的工作原理基于PV操作**，其中P操作用于请求资源（减少信号量的值），而V操作用于释放资源（增加信号量的值）。

- **P操作**“proberen”（尝试）：如果信号量的值为正，则将其减一，允许进程继续执行。如果信号量的值为0或负，则进程被阻塞，直到信号量的值变为正数。

- **V操作**“verhogen”（释放）：将信号量的值加一，如果有其他进程因等待该信号量而被阻塞，则其中一个进程会被唤醒。

**POSIX**是一组用于确保操作系统间**可移植性的IEEE标准**，主要针对Unix系统。POSIX标准为操作系统提供了一套共通的规则，使得软件开发更加高效，同时也让用户能够在不同的系统之间无缝地迁移和运行应用程序。

## 有名信号灯

用到的函数主要有：

1. `sem_open`  有名信号灯打开
2. `sem_close` 有名信号灯关闭
3. `sem_unlink` 有名信号灯的删除
4. `sem_wait`  信号灯P操作，申请资源
5. `sem_post`  信号灯V操作，释放资源

### sem_open 函数

>  1. **原型**：
>
>     ```c
>     #include <fcntl.h>
>     #include <sys/stat.h>
>     #include <semaphore.h>
>     sem_t *sem_open(const char *name, int oflag, mode_t mode, unsigned int value);
>     ```
>
>  2. **功能**：创建或打开一个有名信号量（named semaphore）的函数
>
>  3. **参数**：
>
>     -  `name`：信号量的名称，用于标识信号量。
>     -  `oflag`：打开选项，可以是以下值的组合：
>        -  `O_CREAT`：如果信号量不存在，则创建一个新的信号量。
>        -  `O_EXCL`：与`O_CREAT`一起使用，表示如果信号量已存在，则返回错误。
>        -  `O_RDWR`：允许对信号量进行读写操作。
>     -  `mode`：设置信号量的权限，通常设置为0644。
>     -  `value`：信号量的初始值。
>
>  4. **返回值**：
>
>     -  成功时，返回一个指向信号量的指针。
>     -  失败时，返回SEM_FAILURE（通常是NULL）。
>

### sem_close 函数

>  1. **原型**：
>
>     ```c
>     #include <semaphore.h>
>     int sem_close(sem_t *sem);
>     ```
>
>  2. **功能**：用于关闭一个信号量的函数
>
>  3. **参数**：
>
>     -  `sem`：指向要关闭的信号量的指针
>
>  4. **返回值**：
>
>     -  成功时，返回0。
>     -  失败时，返回-1，并设置errno。
>

### sem_unlink 函数

>  1. **原型**：
>
>     ```c
>     #include <semaphore.h>
>     int sem_unlink(const char *name);
>     ```
>
>  2. **功能**：删除一个命名信号量
>
>  3. **参数**：
>
>     -  `name`：要删除的信号量的名称
>
>  4. **返回值**：
>
>     -  成功时，返回0
>     -  失败时，返回-1，并设置errno
>

### sem_wait 函数

>  1. **原型**： 
>
>     ```c
>     #include <semaphore.h>
>     int sem_wait(sem_t *sem);
>     ```
>
>  2. **功能**：”**P操作**“等待一个信号量。当信号量的值大于0时，该函数会将信号量的值减1，并立即返回。如果信号量的值为0，则该函数会阻塞当前线程，直到信号量的值大于0为止。
>
>  3. **参数**：
>
>     -  `sem`：指向要等待的信号量的指针。
>
>  4. **返回值**：
>
>     -  成功时，返回0。
>     -  失败时，返回-1，并设置errno。
>

### sem_post 函数

>  1. **原型**：
>
>     ```c
>     #include <semaphore.h>
>     int sem_post(sem_t *sem);
>     ```
>
>  2. **功能**：”**V操作**“增加一个信号量的值。当信号量的值大于0时，如果有其他线程正在等待该信号量，则`sem_post`函数会唤醒其中一个等待的线程。如果没有线程在等待，那么信号量的值简单地增加，它与`sem_wait`函数相对应.
>
>  3. **参数**：
>
>     -  `sem`：指向要操作的信号量的指针。
>
>  4. **返回值**：
>
>     -  成功时，返回0。
>     -  失败时，返回-1，并设置errno。
>

### 示例-有名信号灯使用

>  使用信号量和共享内存实现的简单进程间通信（IPC）示例
>
>  一个简单的命令行界面，用户可以在命令行中输入字符串，并将其存储到共享内存中。通过信号量来实现进程间的同步，确保数据的读写操作不会发生冲突
>
>  tes_semw.c 写程序
>
>  ```c
>  #include <fcntl.h>
>  #include <sys/stat.h>
>  #include <semaphore.h>
>  #include <stdio.h>
>  #include <unistd.h>
>  #include <stdlib.h>
>  #include <sys/ipc.h>
>  #include <sys/shm.h>
>  #include <signal.h>
>  
>  void delsemfile(int sig)
>  {
>    sem_unlink("mysem_w");  // 删除名为"mysem_w"的信号量
>    exit(0);                // 退出程序
>  }
>  
>  int main()
>  {
>   sem_t *sem_r,*sem_w;     // 定义两个信号量指针，分别用于读和写操作
>   key_t key;               // 定义键值，用于生成共享内存标识符
>   int shmid;               // 定义共享内存标识符
>   char *shmaddr;           // 定义共享内存地址指针
>  
>   struct sigaction act;    // 定义信号处理结构体
>   act.sa_handler = delsemfile;  // 设置信号处理函数为delsemfile
>   act.sa_flags = 0;        // 设置信号处理标志为0
>   sigemptyset(&act.sa_mask);   // 清空信号集
>  
>   sigaction(SIGINT,&act,NULL); // 注册信号处理函数，当接收到SIGINT信号时调用delsemfile函数
>  
>   key = ftok(".",100);      // 生成键值，用于创建共享内存标识符
>   if(key<0)
>   {
>       perror("ftok");      // 如果生成失败，打印错误信息
>       return 0;            
>   }
>  
>   shmid = shmget(key,500,0666|IPC_CREAT);  // 创建共享内存段，大小为500字节，权限为0666，如果不存在则创建
>   if(shmid<0)
>   {
>       perror("shmget");    // 如果创建失败，打印错误信息
>       return 0;           
>   }
>  
>   shmaddr = shmat(shmid,NULL,0);  // 将共享内存段附加到进程的地址空间，并获取共享内存地址指针
>  
>   sem_r = sem_open("mysem_r",O_CREAT|O_RDWR,0666,0);  // 创建名为"mysem_r"的信号量，初始值为0
>   sem_w = sem_open("mysem_w",O_CREAT|O_RDWR,0666,1);  // 创建名为"mysem_w"的信号量，初始值为1
>  
>   while(1)
>   {
>        sem_wait(sem_w);    // 等待名为"mysem_w"的信号量变为非零值
>        printf(">");        // 输出提示符">"
>        fgets(shmaddr,500,stdin);  // 从标准输入读取一行字符串，存储到共享内存中
>        sem_post(sem_r);    // 增加名为"mysem_r"的信号量的值，表示数据已写入共享内存
>   }
>  }
>  ```
>
>  tes_semr.c 读程序
>
>  ```c
>  #include <fcntl.h>
>  #include <sys/stat.h>
>  #include <semaphore.h>
>  #include <stdio.h>
>  #include <unistd.h>
>  #include <stdlib.h>
>  #include <sys/ipc.h>
>  #include <sys/shm.h>
>  #include <signal.h>
>  
>  void delsemfile(int sig)
>  {
>    sem_unlink("mysem_r");  // 删除名为"mysem_r"的信号量
>    exit(0);                // 退出程序
>  }
>  
>  int main(){
>  
>   sem_t *sem_r,*sem_w;     // 定义两个信号量指针，分别用于读和写操作
>   key_t key;               // 定义键值，用于生成共享内存标识符
>   int shmid;               // 定义共享内存标识符
>   char *shmaddr;           // 定义共享内存地址指针
>   struct sigaction act;    // 定义信号处理结构体
>   act.sa_handler = delsemfile;  // 设置信号处理函数为delsemfile
>   act.sa_flags = 0;        // 设置信号处理标志为0
>   sigemptyset(&act.sa_mask);   // 清空信号集
>  
>   sigaction(SIGINT,&act,NULL); // 注册信号处理函数，当接收到SIGINT信号时调用delsemfile函数
>  
>   key = ftok(".",100);      // 生成键值，用于创建共享内存标识符
>   if(key<0)
>   {
>       perror("ftok");      // 如果生成失败，打印错误信息
>       return 0;            
>   }
>  
>   shmid = shmget(key,500,0666|IPC_CREAT);  // 创建共享内存段，大小为500字节，权限为0666，如果不存在则创建
>   if(shmid<0)
>   {
>       perror("shmget");    // 如果创建失败，打印错误信息
>       return 0;            
>   }
>  
>   shmaddr = shmat(shmid,NULL,0);  // 将共享内存段附加到进程的地址空间，并获取共享内存地址指针
>  
>   sem_r = sem_open("mysem_r",O_CREAT|O_RDWR,0666,0);  // 创建名为"mysem_r"的信号量，初始值为0
>   sem_w = sem_open("mysem_w",O_CREAT|O_RDWR,0666,1);  // 创建名为"mysem_w"的信号量，初始值为1
>  
>   while(1)
>   {
>        sem_wait(sem_r);    // 等待名为"mysem_r"的信号量变为非零值
>        printf("%s\n",shmaddr);  // 输出共享内存中的字符串
>        sem_post(sem_w);    // 增加名为"mysem_w"的信号量的值，表示数据已读取完成
>   }
>  }
>  ```
>
>  ![image-20240511164306551](./11_Concurrent_Programming.assets/image-20240511164306551.png)
>
>  运行效果如图，实现了两个进程通讯
>
>  可以通过命令`ls /dev/shm`查看信号量文件;^在Linux系统中，/dev/shm目录是用于存放共享内存和信号量文件的特殊目录^
>
>  `ctrl + c`退出程序后，捕获到`SIGINT`信号后执行`delsemfile()`删除信号量文件

## 无名信号灯

用到的函数有:

1. `sem_init`  初始化无名信号量
2. `sem_destory`  销毁无名信号量
3. `sem_wait`  信号灯P操作，申请资源
4. `sem_post`  信号灯V操作，释放资源

### sem_init 函数

>  1. **原型**：
>
>     ```c
>     #include <semaphore.h>
>     int sem_init(sem_t *sem, int pshared, unsigned int value);
>     ```
>
>  2. **功能**：初始化一个无名信号量
>
>  3. **参数**：
>
>     -  `sem`：指向要初始化的信号量的指针。
>     -  `pshared`：指定信号量的类型，如果为0，则表示该信号量是进程私有的；如果为非0值，则表示该信号量是进程间共享的。
>     -  `value`：指定信号量的初始值。
>
>  4. **返回值**：
>
>     -  成功时，返回0；
>     -  失败时，返回-1，并设置`errno`。
>

### sem_destory 函数

>  1. **原型**：
>
>     ```c
>     #include <semaphore.h>
>     int sem_destroy(sem_t *sem);
>     ```
>
>  2. **功能**：销毁一个无名信号量
>
>  3. **参数**：
>
>     -  `sem`：指向要销毁的信号量的指针。
>
>  4. **返回值**：
>
>     -  成功时，返回0；
>     -  失败时，返回-1，并设置`errno`。
>
>  5. **注意**：只有当信号量的引用计数变为0时，才能安全地销毁它。如果还有其他线程或进程正在等待该信号量，那么销毁操作将失败，并且`errno`将被设置为`EBUSY`。在这种情况下，需要确保所有使用该信号量的线程或进程都已经结束，然后再尝试销毁它。
>

### 示例-无名信号灯使用

>  使用信号量和共享内存实现的简单进程间通信（IPC）示例。主要功能是在一个进程中输入字符串，另一个进程中读取并打印这些字符串。
>
>  ```c
>  #include <fcntl.h>
>  #include <sys/stat.h> 
>  #include <semaphore.h>
>  #include <stdio.h>
>  #include <unistd.h>
>  #include <stdlib.h>
>  #include <sys/ipc.h>
>  #include <sys/shm.h>
>  #include <signal.h>
>  #include <pthread.h>
>  
>  sem_t sem_r,sem_w; // 定义两个信号量，用于读写同步
>  char *shmaddr; // 共享内存地址指针
>  
>  void destroysem(int sig) // 信号处理函数，用于销毁信号量并退出程序
>  {
>  sem_destroy(&sem_r); // 销毁读信号量
>  sem_destroy(&sem_w); // 销毁写信号量
>  exit(0); // 退出程序
>  }
>  
>  void *readmem(void *arg) // 读取共享内存中的字符串并打印的线程函数
>  {
>  while(1)
>  {
>      sem_wait(&sem_r); // 等待读信号量
>      printf("%s\n",shmaddr); // 打印共享内存中的字符串
>      sem_post(&sem_w); // 释放写信号量
>  }
>  }
>  
>  int main()
>  {
>  key_t key; // 键值
>  int shmid; // 共享内存标识符
>  
>  struct sigaction act; // 信号处理结构体
>  act.sa_handler = destroysem; // 设置信号处理函数
>  act.sa_flags = 0; // 设置信号处理标志
>  sigemptyset(&act.sa_mask); // 清空信号集
>  
>  sigaction(SIGINT,&act,NULL); // 注册信号处理函数
>  
>  key = ftok(".",100); // 生成键值
>  if(key<0)
>  {
>     perror("ftok"); // 输出错误信息
>     return 0;
>  }
>  
>  shmid = shmget(key,500,0666|IPC_CREAT); // 创建共享内存
>  if(shmid<0)
>  {
>     perror("shmget"); // 输出错误信息
>     return 0;
>  }
>  
>  shmaddr = shmat(shmid,NULL,0); // 将共享内存映射到进程地址空间
>  
>  sem_init(&sem_r,0,0); // 初始化读信号量，初始值为0
>  sem_init(&sem_w,0,1); // 初始化写信号量，初始值为1
>  
>  pthread_t tid; // 线程标识符
>  pthread_create(&tid,NULL,readmem,NULL); // 创建线程，执行readmem函数
>  
>  while(1)
>  {
>      sem_wait(&sem_w); // 等待写信号量
>      printf(">"); // 提示用户输入
>      fgets(shmaddr,500,stdin); // 从标准输入读取字符串并存储到共享内存中
>      sem_post(&sem_r); // 释放读信号量
>  }
>  }
>  ```
>
>  ![image-20240511174606167](./11_Concurrent_Programming.assets/image-20240511174606167.png)
>
>  运行效果如图
>
>  在父进程中，它循环接收用户输入的数据并将其写入共享内存中。在子进程中，它循环读取共享内存中的数据并打印出来。

## System V 信号灯

System V 信号灯是一种用于进程间同步和互斥的机制，它允许多个进程共享资源而不会产生冲突。

1. **信号灯集合**：System V 信号灯可以是一个或多个计数信号灯的集合。这意味着可以同时操作集合中的任意多个信号灯，以协调进程间的访问顺序。
2. **信号灯操作**：System V 信号灯的操作通常涉及三个步骤：初始化（创建）、P 操作（等待资源）和 V 操作（释放资源）。P 操作用于请求资源，如果资源不可用则进程将等待；V 操作用于释放资源，使得其他等待该资源的进程可以继续执行
3. **避免死锁**：通过申请多个资源时使用 System V 信号灯，可以减少死锁的风险。死锁是指两个或多个进程在互相等待对方释放资源时无法继续执行的状态。

System V 信号灯是一种重要的同步机制，它在多进程编程中扮演着关键角色，确保了资源的有序访问和数据的一致性。

用到的函数有：

1. `semget`  创建/打开信号灯
2. `semop`  对信号灯集合中的信号量进行P - V操作
3. `semctl`  信号灯集合的控制（初始化/删除）

### semget 函数

>  1. **原型**：
>
>     ```c
>     #include <sys/sem.h>
>     int semget(key_t key, int nsems, int semflg);
>     ```
>
>  2. **功能**：获取一个信号量集的标识符
>
>  3. **参数**：
>
>     -  `key`：是一个键值，用于唯一标识一个信号量集。通常使用`ftok()`函数生成。
>     -  `nsems`：指定需要创建或获取的信号量数量。
>     -  `semflg`：设置信号量集的访问权限和创建标志。可以是以下值的组合：
>        -  `IPC_CREAT`：如果信号量集不存在，则创建一个新的信号量集。
>        -  `IPC_EXCL`：与`IPC_CREAT`一起使用，表示如果信号量集已经存在，则返回错误。
>        -  `0`：表示不设置任何特殊标志。
>
>  4. **返回值**：
>
>     -  如果成功，返回信号量集的标识符（非负整数）。
>     -  如果失败，返回-1，并设置`errno`为相应的错误码。
>

### semop 函数

>  1. **原型**：
>
>     ```c
>     #include <sys/sem.h>
>     int semop(int semid, struct sembuf *sops, unsigned nsops);
>     ```
>
>  2. **功能**：改变信号量的值,对信号灯集合中的信号量进行P - V操作
>
>  3. **参数**：
>
>     -  `semid`：是一个信号量集的标识符，由`semget()`函数返回。
>     -  `sops`：是一个指向`struct sembuf`结构体数组的指针，该数组定义了要执行的操作。
>     -  `nsops`：指定`sops`数组中操作的数量。
>
>     >   `struct sembuf`结构体：
>     >
>     >   ```c
>     >   struct sembuf {
>     >      unsigned short sem_num;  // 信号量编号
>     >      short sem_op;            // 操作类型
>     >      short sem_flg;           // 操作标志
>     >   };
>     >   ```
>     >
>     >   各个字段的含义如下：
>     >
>     >   -  `sem_num`：指定要操作的信号量的编号。如果设置为0，则表示对整个信号量集进行操作。
>     >   -  `sem_op`：指定要对信号量执行的操作类型。
>     >      -  当`sem_op`> 0，它表示进程释放控制的资源，即信号量的值将增加`sem_op`的数量。
>     >      -  当`sem_op`= 0，如果没有设置IPC_NOWAIT标志，调用进程将进入睡眠状态直到信号量的值为0；如果设置了该标志且信号量值不为0，则进程不会进入睡眠，而是直接返回EAGAIN。
>     >      -  当`sem_op`< 0，它表示尝试获取资源使用权，信号量的值将增加`sem_op`的绝对值。如果此时信号量的值小于或等于`sem_op`的绝对值，操作将会阻塞，直到信号量的值大于或等于`sem_op`的绝对值。
>     >   -  `sem_flg`：指定操作的标志。可以是以下值的组合：
>     >      -  `IPC_NOWAIT`：非阻塞模式，如果无法立即执行操作，则立即返回。
>     >      -  `SEM_UNDO`：撤销之前的操作。
>     >      -  `0`：不设置任何特殊标志。
>
>  4. **返回值**：
>
>     -  如果成功，返回0。
>     -  如果失败，返回-1，并设置`errno`为相应的错误码。
>

### semctl 函数

>  1. **原型**：
>
>     ```c
>     #include <sys/sem.h>
>     int semctl(int semid, int semnum, int cmd, ...);
>     ```
>
>  2. **功能**：控制信号量集
>
>  3. **参数**：
>
>     -  `semid`：是一个信号量集的标识符，由`semget()`函数返回。
>     -  `semnum`：指定要操作的信号量的编号。如果设置为0，则表示对整个信号量集进行操作。
>     -  `cmd`：指定要执行的命令。可以是以下值之一：
>        -  `IPC_RMID`：删除信号量集。(常用)
>        -  `IPC_SET`：设置信号量集的属性。(常用)
>        -  `IPC_STAT`：获取信号量集的状态信息。(常用)
>        -  `SETVAL`：设置信号灯的值，需要用到第四个参数：共用体。(常用)
>        -  `IPC_INFO`：获取系统支持的信号量集的最大数量和当前使用的数量。
>        -  `SEM_STAT`：获取指定信号量的状态信息。
>        -  `GETALL`：获取所有信号量的值。
>        -  `GETNCNT`：获取等待某个信号量变为非零值的进程数。
>        -  `GETPID`：获取最后一个操作指定信号量的进程ID。
>        -  `GETVAL`：获取指定信号量的值。
>        -  `GETZCNT`：获取等待某个信号量变为零值的进程数。
>        -  `SETALL`：设置所有信号量的值。
>     -  `...`：根据`cmd`的不同，可能需要传递额外的参数。
>
>  4. **返回值**：
>
>     -  如果成功，返回相应的结果或状态信息。
>     -  如果失败，返回-1，并设置`errno`为相应的错误码
>



### 示例-System V信号灯使用

>  基于信号量和共享内存的进程间通信（IPC）示例。它创建了一个父子进程，并使用信号量和共享内存进行数据传递。
>
>  ```c
>  #include <semaphore.h>
>  #include <stdio.h>
>  #include <unistd.h>
>  #include <stdlib.h>
>  #include <string.h>
>  #include <sys/ipc.h>
>  #include <sys/shm.h>
>  #include <sys/types.h>
>  #include <sys/sem.h>
>  
>  #define SEM_READ   0
>  #define SEM_WRITE  1
>  
>  //semctl函数用到的参数
>  union semun {
>   int val;
>  };               
>  
>  // P操作函数，用于对信号量进行减一操作
>  void Poperation(int semid,int semindex)
>  {
>   struct sembuf sbuf;
>   sbuf.sem_num =  semindex;
>   sbuf.sem_op = -1;
>   sbuf.sem_flg = 0;
>  
>   semop(semid,&sbuf,1);
>  }
>  
>  // V操作函数，用于对信号量进行加一操作
>  void Voperation(int semid,int semindex)
>  {
>   struct sembuf sbuf;
>   sbuf.sem_num =  semindex;
>   sbuf.sem_op = 1;
>   sbuf.sem_flg = 0;
>  
>   semop(semid,&sbuf,1);
>  }
>  
>  int main()
>  {
>    key_t key; // 定义键值key
>    char *shmaddr; // 共享内存地址指针
>    int semid,shmid; // 信号量和共享内存的ID
>    key = ftok(".",100); // 生成键值
>    if(key<0)
>    {
>        perror("ftok"); // 如果生成失败，输出错误信息
>        return 0;
>    }
>  
>    semid = semget(key,2,IPC_CREAT |0666); // 创建信号量集合
>    if(semid<0)
>    {
>        perror("semget"); // 如果创建失败，输出错误信息
>        return 0;
>    }
>    shmid = shmget(key,500,IPC_CREAT |0666); // 创建共享内存
>    shmaddr = shmat(shmid,NULL,0); // 将共享内存映射到进程地址空间
>    union semun mysem; // 定义信号量联合体
>    mysem.val = 0; // 初始化读信号量的值为0
>    semctl(semid,SEM_READ,SETVAL,mysem); // 设置读信号量的值
>    mysem.val = 1; // 初始化写信号量的值为1
>    semctl(semid,SEM_WRITE,SETVAL,mysem); // 设置写信号量的值
>  
>    pid_t pid; // 定义进程ID
>    pid = fork(); // 创建子进程
>    if(pid<0)
>    {
>        perror("fork"); // 如果创建失败，输出错误信息
>        shmctl(shmid,IPC_RMID,NULL); // 删除共享内存
>        semctl(semid,0,IPC_RMID); // 删除信号量集合
>        exit(-1);
>    }else if(pid == 0)
>    {
>        // 子进程循环读取共享内存中的数据并打印
>        while(1)
>        {
>            Poperation(semid,SEM_READ); // 对读信号量进行P操作
>            printf("%s\n",shmaddr); // 打印共享内存中的数据
>  
>            Voperation(semid,SEM_WRITE); // 对写信号量进行V操作
>        }
>    }
>    else
>    {
>        // 父进程循环接收用户输入的数据并写入共享内存
>        while(1)
>        {
>            Poperation(semid,SEM_WRITE); // 对写信号量进行P操作
>            printf(">"); // 提示用户输入数据
>            fgets(shmaddr,32,stdin); // 从标准输入读取数据并写入共享内存
>  
>            Voperation(semid,SEM_READ); // 对读信号量进行V操作
>        }
>    }
>  }
>  ```
>
>  ![image-20240511210820236](./11_Concurrent_Programming.assets/image-20240511210820236.png)
>
>  运行结果如图
>
>  在父进程中，它循环接收用户输入的数据并将其写入共享内存中。在子进程中，它循环读取共享内存中的数据并打印出来。

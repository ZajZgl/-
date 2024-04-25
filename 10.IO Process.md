#  IO进程

- Linux IO进程学习目的：学习编写linux应用程序（APP）

- Linux文件的种类 :

  -   常规文件

  -   目录文件

  -   字符文件

  -   块文件

  -   链接文件（相当于windows快捷方式）

-  IO的概念

  -   I input  输入设备 比如键盘鼠标都是Input设备

  -   O output  输出设备 比如显示器

  -   优盘，网口，既是输入也是输出

- 系统调用和库函数

  - 系统调用就是操作系统提供的接口函数.

  - 如果我们把系统调用封装成库函数就可以起到隔离的作用，提供程序的可移植性。

  - Printf就是库函数然后调用了系统调用才在显示器上显示字符。

- 流的概念
  - 就是数据的流，在程序中就是一个结构体。

- Windows 和linux的换行符区别

  -   Windows是\r\n 

  -   Linux 是\n

- 缓冲区的概念

  -   为了减少操作IO设备的次数，提高运行效率，在内存里面设置的缓冲区，

  -   全缓冲：缓冲区满才输出

  -   行缓冲：遇到换行符输出

  - 程序正常结束会刷新缓冲区。

-  三种标准IO ：

| 标准输入流（键盘）   | 0    | STDIN_FILENO  | stdin  |
| -------------------- | ---- | ------------- | ------ |
| 标准输出流（显示器） | 1    | STDOUT_FILENO | stdout |
| 标准错误流           | 2    | STDERR_FILENO | stderr |

## 文件打开和关闭

**文件的打开和关闭概念**

打开就是占用资源

关闭就是释放资源

### **文件的打开**

- 文件的打开函数：FILE *fopen (const char *path, const char *mode);
  - Path: 普通文件当前路径不需要加目录，其他要使用完整的路径
  - Mode：见下表
  - 返回值：出错返回NULL，所以使用fopen函数必须判断是否为空

- 文件打开的模式**（非常重要）**

| 模式          | 功能描述                                                     |
| ------------- | ------------------------------------------------------------ |
| “r” 或 “rb”   | 以只读方式打开文件，文件必须存在。                           |
| “r+” 或 ”r+b” | 以读写方式打开文件，文件必须存在。                           |
| “w” 或 “wb”   | 以只写方式打开文件，==若文件存在则文件长度清为0==。若文件不存在则创建。 |
| “w+” 或 “w+b” | 以读写方式打开文件，其他同”w”。                              |
| “a” 或 “ab”   | 以只写方式打开文件，若文件不存在则创建；向文件写入的数  据被追加到文件**末尾**。 |
| “a+” 或 “a+b” | 以读写方式打开文件。其他同”a”                                |

- 库函数

| perror   | stdio.h          | 打印系统的错误描述 |
| -------- | ---------------- | ------------------ |
| strerror | errno.h string.h | 打印系统的错误描述 |

> strerror使用：`printf("fopen:%s\n",strerror(errno));`

### **文件的关闭：**

- 函数原型：int fclose(FILE *stream)

  - fclose()调用成功返回0，失败返回EOF（-1），并设置errno

  - 流关闭时自动刷新缓冲中的数据并释放缓冲区，比如：常规文件把缓冲区内容写入磁盘

  - 当一个程序正常终止时，所有打开的流都会被关闭

  - fclose()函数的入参stream必须保证为非空，否则出现断错误。

## 标准输入输出

标准IO的字符输入和输出

### **字符的输入（读单个字符）：**

- int fgetc(FILE *stream);
-  int getc(FILE *stream);  //宏
-  int getchar(void);  //读取键盘输入

> 成功时返回读取的字符；若到文件末尾或出错时返回EOF（-1），
>
> getchar()等同于fgetc(stdin)
>
> getc和fgetc区别是一个是宏一个是函数

**注意事项：**

1. 函数返回值是int类型不是char类型，主要是为了扩展返回值的范围。

2. tdin 也是FILE *的指针，是系统定义好的,指向的是标准输入（键盘输入）

3. ==打开文件后读取，是从文件开头开始读。读完一个后读写指针会后移。读写注意文件位置！==

4. 调用getchar会阻塞，等待你的键盘输入

 

### 字符的输出（写单个字符）：

- int fputc(int c, FILE *stream);
- int putc(int c, FILE *stream);
- int putchar(int c);

> 成功时返回写入的字符；出错时返回EOF(-1)
>
> putchar(c) 等同于fputc(c, stdout)

**注意事项：**

1. 返回和输入参数都是int类型

2. 遇到这种错误：Bad file descriptor， 很可能是文件打开的模式错误（只读模式去写，只写模式去读）

### 行输出（读取整个行）

- char *gets(char *s); 读取标准输入到缓冲区s
- char *fgets(char *s, int size, FILE *stream);

> 成功时返回s，到文件末尾或出错时返回NULL
>
> 遇到’\n’或已输入size-1个字符时返回，总是包含’\0’

**注意事项：**

1 gets函数已经被淘汰，因为会导致缓冲区溢出

2 fgets 函数第二个参数，输入的数据超出size，size-1个字符会保存到缓冲区，最后添加’\0’，如果输入数据少于size-1 后面会添加换行符。

### 行输出（写整行）

- int puts(const char *s);
- int fputs(const char *s, FILE *stream);

> 成功时返回非负整数；出错时返回EOF
>
> puts将缓冲区s中的字符串输出到stdout，并追加’\n’
>
> fputs将缓冲区s中的字符串输出到stream,不追加 ‘\n’

### 代码

```c
#include <stdio.h>
int main(int argc, const char *argv[])
{
	FILE * fe;
	int ret;
	fe=fopen("1.txt","r+");
	if(fe == NULL)//如果打开失败
	{
		perror("fopen");
		return 0;
	}
	ret=fputs("hello world",fe);
	if(ret == -1)//如果写入失败
	{
		perror("fputs");
	}
	fclose(fe);
	return 0;
}
```

##**二进制读写**

文本文件和二进制的区别：

存储的格式不同：文本文件只能存储文本。

计算机内码概念：文本符号在计算机内部的编码（计算机内部只能存储数字0101001....,所以所有符号都要编码）

**二进制读写函数格式：**

- size_t fread(void *ptr, size_t size, size_t n, FILE *fp);
  - void *ptr 读取内容放的位置指针  （void * -- 指向任意类型的指针）
  -  size_t size 读取的块大小  （size_t -- int）
  - size_t n 读取的个数
  -  FILE *fp 读取的文件指针

- size_t fwrite(const void *ptr, size_t size, size_t n, FILE *fp);
  - void *ptr 写文件的内容的位置指针
  - size_t size 写的块大小
  - size_t n 写的个数
  -  FILE *fp 被写入的文件指针

**注意事项：**

==文件写完后，文件指针指向文件末尾，如果这时候读，读不出来内容。==

解决办法：移动指针（后面讲解）到文件头；关闭文件，重新打开

### 代码

```c
#include <stdio.h>
#include <string.h>
struct studint{
	char name[10];
	int age;
	char sex[10];
};

int main(int argc, const char *argv[])
{
	FILE *fo;
	int ret;
	struct studint stu1,stu2,stu3;
	strcpy(stu1.name,"zhangsan");
	stu1.age = 5;
	strcpy(stu1.sex,"man");

	strcpy(stu2.name,"lisi");
	stu2.age = 7;
	strcpy(stu2.sex,"woman");

	fo = fopen("1.bin","w+");
	if(fo == NULL)
	{
		perror("fopen");
		return 0;
	}
	
	ret = fwrite(&stu1,sizeof(stu1),1,fo);
	if(ret == -1)
	{
		perror("fwrite");
		goto end;
	}
	ret = fwrite(&stu2,sizeof(stu2),1,fo);//写入
	if(ret == -1)
	{
		perror("fwrite");
		goto end;
	}
	fclose(fo);//关闭文件重新打开，使指针指向文件开头·
	
	fo = fopen("1.bin","r");
	if(fo == NULL)
	{
		perror("fopen");
		return 0;
	}
	ret = fread(&stu3,sizeof(stu3),1,fo);//读取
	if(ret == -1)
	{
		perror("fraed");
		goto end;
	}
	printf("%s %d %s",stu3.name,stu3.age,stu3.sex);

end:
	fclose(fo);
	return 0;
}
```

## 流的刷新

-  int fflush(FILE *fp);
  - 成功时返回0；出错时返回EOF
  - **将流缓冲区中的数据写入实际的文件**
  - Linux下只能刷新输出缓冲区,输入缓冲区丢弃
- 如果输出到屏幕使用fflush(stdout)

## 流的定位

- long ftell(FILE *stream); //返回当前文件指针位置

- void rewind(FILE *stream);  //将读写位置指向文件开头

- long fseek(FILE *stream, long offset, int whence);//移动指针

  - offset参数：偏移量，可正可负

  - fseek 函数whence参数：SEEK_SET/SEEK_CUR/SEEK_END

    - SEEK_SET ：从距文件**开头** offset 位移量为新的读写位置

    - SEEK_CUR：以**目前的读写位置**往后增加 offset 个位移量

    - SEEK_END：将读写位置指向文件**结尾**后再增加 offset 个位移量

**注意事项：**

1. ==文件的打开使用a模式 fseek无效==

2. rewind(fp) 相当于 fseek(fp,0,SEEK_SET);

3. 这三个函数只适用2G以下的文件

##  格式化输入输出

**输入**

- **scanf**：它是标准输入函数，用于从键盘读取数据。其使用格式字符串来指定输入数据的类型和格式，并将读取的值存储到变量中。
- **fscanf**：这个函数与`scanf`类似，但它从指定的文件流中读取数据，而不是从标准输入设备。这允许你从文件中按格式读取数据。
- **sscanf**：此函数也类似于`scanf`，但它是从给定的字符串（而不是文件或标准输入）中读取格式化输入。这在处理已经存储在内存中的文本数据时非常有用。

**输出**

- **printf**：这是最常用的格式化输出函数，它将格式化的数据输出到标准输出设备，通常是屏幕。`printf`使用格式化字符串来指定数据的输出格式，例如`%d`表示整数，`%f`表示浮点数等。
- **fprintf**：这个函数与`printf`类似，但它将输出发送到一个文件流而不是标准输出。这允许你将格式化的数据写入文件。
- **sprintf**：此函数用于将格式化的数据写入字符串。它是一个有用的工具，当你需要创建一个包含特定数据的字符串时，可以将其存储在字符数组中。
- **snprintf**：与`sprintf`类似，但`snprintf`允许你指定一个最大长度，以防止缓冲区溢出。
- **vprintf**、**vfprintf** 和 **vsprintf**：这些是可变参数版本的格式化输出函数，它们接受一个参数列表，而不是固定数量的参数。这使得它们在参数数量不确定时更加灵活。

## 标准IO-练习

每隔1秒向文件1.txt写入当前系统时间,行号递增

**函数说明：**

> **time**()	用来获取系统时间(秒数)
>
> - time_t **time**(time_t *seconds) //从 **1970.1.1 0:0:0** 开始计算
>
> **localtime**()	将系统时间转换成本地时间
>
> - struct **tm** *localtime(const time_t *timer)
>
> struct tm {
>
> ​	 int tm_sec;     /* 秒，范围从 0 到 59        */
>
> ​	 int tm_min;     /* 分，范围从 0 到 59        */
>
> ​	 int tm_hour;    /* 小时，范围从 0 到 23        */
>
> ​	 int tm_mday;    /* 一月中的第几天，范围从 1 到 31          */
>
> ​	 int tm_mon;     /* 月份，范围从 0 到 11        */
>
> ​	 int tm_year;    /* 自 1900 起的年数        */
>
> ​	 int tm_wday;    /* 一周中的第几天，范围从 0 到 6        */
>
> ​	 int tm_yday;    /* 一年中的第几天，范围从 0 到 365          */
>
> ​	 int tm_isdst;    /* 夏令时            */  
>
> };

- 写完文件记得fflush ，写到磁盘里面去。
- 标准IO磁盘文件的缓冲区一般为4096
- 注意和标准输出的全缓冲区别，标准输出是1024

```c
#include <stdio.h>
#include <time.h>
#include <unistd.h>
#include <string.h>
int main(int argc, const char *argv[])
{
	FILE *fo;
	time_t sys_time;//系统时间
	struct tm *loc_time;//本地时间
	int line_num = 0;//行号
	char buff[32];//初始行号计算缓冲区
	fo = fopen("1.txt","a+");
	if(fo == NULL)
	{
		perror("fopen");
		return 0;
	}
	/*计算初始行号*/
	while(fgets(buff,32,fo) != NULL)
	{
		if(buff[strlen(buff)-1] == '\n')
		{
			line_num++;
		}
	}
	while(1)
	{
		sys_time = time(NULL);
		loc_time = localtime(&sys_time);
		printf("%d,%04d-%02d-%02d--%02d:%02d:%02d\n",
				line_num,
				loc_time->tm_year+1900,
				loc_time->tm_mon+1,
				loc_time->tm_mday,
				loc_time->tm_hour,
				loc_time->tm_min,
				loc_time->tm_sec);
		fprintf(fo,"%d,%04d-%02d-%02d--%02d:%02d:%02d\n",
				line_num,
				loc_time->tm_year+1900,
				loc_time->tm_mon+1,
				loc_time->tm_mday,
				loc_time->tm_hour,
				loc_time->tm_min,
				loc_time->tm_sec);
		fflush(fo);
		line_num++;
		sleep(1);
	}
	fclose(fo);

	return 0;
}

```

## 文件IO

**介绍：**

- 文件I/O，又称系统IO，系统调用。是操作系统提供的API接口函数。
- posix(可移植操作系统接口)定义的一组函数
- 不提供缓冲机制，每次读写操作都引起系统调用
- 核心概念是文件描述符
- 访问各种类型文件
- Linux下, 标准IO基于文件IO实现

### **文件描述符概念：**

- 英文：缩写fd（file descriptor）
- 本质是0-1023的数字，表示文件。
- 每个打开的文件都对应一个文件描述符。
- 文件描述符是一个非负整数。Linux为程序中每个打开的文件分配一个文件描述符。
- 文件描述符从0开始分配，依次递增。
- 0, 1, 2 的含义 标准输入，标准输出，错误

### **文件IO打开-open**

> - int open(const char *pathname, int flags);  不创建文件
> - int open(const char *pathname, int flags, mode_t mode); 创建文件，但不能创建设备文件,成功时返回文件描述符；出错时返回EOF
>   - pathname：被打开的文件名
>   - flags
>     - O_RDONLY：只读方式打开文件
>     - O_WRONLY：可写方式打开文件
>     - O_RDWR：读写方式打开文件
>     - O_CREAT：如果该文件不存在，就创建一个新的文件，并用第三的参数为其设置权限。
>     - O_EXCL： 如果使用O_CREAT时文件存在，则可返回错误消息。这一参数可测试文件是否存在。
>     - O_NOCTTY：使用本参数时，如文件为终端，那么终端不可以作为调用open()系统调用的那个进程的控制终端。
>     - O_TRUNC：如文件已经存在，那么打开文件时先删除文件中原有数据。
>     - O_APPEND：以添加方式打开文件，所以对文件的写操作都在文件的末尾进行。
>   - mode：被打开文件的存取权限，为8进制表示法。

**文件IO和标准的模式对应关系：**

| 标准IO | 文件IO                                |
| ------ | ------------------------------------- |
| r      | O_RDONLY                              |
| r+     | O_RDWR                                |
| w      | O_WRONLY \| O_CREAT \|O_TRUNC, 0664   |
| w+     | O_RDWR \| O_CREAT \| O_TRUNC, 0664    |
| a      | O_WRONLY \| O_CREAT \| O_APPEND, 0664 |
| a+     | O_RDWR \| O_CREAT \| O_APPEND, 0664   |

**umask概念：**

- umask 用来设定文件或目录的初始权限，（只有在建立新文件时有效）
- 文件或目录的初始权限 = 文件或目录的最大默认权限 - umask权限

**open-示例1:**

> 以只写方式打开文件1.txt。如果文件不存在则创建，如果文件存在则清空：
>
> ```c
> int  fd;
> if ((fd  = open(“1.txt”, O_WRONLY|O_CREAT|O_TRUNC, 0666)) < 0) {
>     perror(“open”);
>     return  -1;
> }
> ```

**open-示例2:**

> 以读写方式打开文件1.txt。如果文件不存在则创建，如果文件存在则报错：
>
> ```c
> int  fd;
> if ((fd  = open(“1.txt”, O_RDWR|O_CREAT|O_EXCL, 0666)) < 0) {
>     if (errno == EEXIST) { 
>        perror(“exist error”);
>     } else {
>        perror(“other error”);
>     }
> }
> ```

### 文件I/O关闭-close

> close函数用来关闭一个打开的文件:
>
>  \#include <unistd.h>
>
>  int close(int fd);
>
> - 成功时返回0；出错时返回EOF
> - 程序结束时自动关闭所有打开的文件
> - ==文件关闭后，文件描述符不再代表文件==

### 文件I/O读-read

>  read函数用来从文件中读取数据:
>
>  \#include <unistd.h>
>
>  ssize_t read(int fd, void *buf, size_t count);
>
> - 成功时返回实际读取的字节数；出错时返回EOF
> - 读到文件末尾时返回0
> - buf是接收数据的缓冲区
> - count不应超过buf大小



### 文件I/O写-write

> write函数用来向文件写入数据:
>
>  \#include <unistd.h>
>
>  ssize_t write(int fd, void *buf, size_t count);
>
> - 成功时返回实际写入的字节数；出错时返回EOF
> - buf是发送数据的缓冲区
> - count不应超过buf大小

**文件I/O定位-lseek**

> lseek函数用来定位文件:
>
>  \#include <unistd.h>
>
>  off_t lseek(int fd, off_t offset, intt whence);
>
> - 成功时返回当前的文件读写位置；出错时返回EOF
> - 参数offset和参数whence同fseek完全一样

### 文件IO-练习

```c
#include <stdio.h>
#include <time.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <string.h>
int main(int argc, const char *argv[])
{
	int fo;
	time_t sys_time;
	struct tm *loc_time;
	int line_num = 0;
	char buff[32];
	if((fo = open("1.txt",O_RDWR|O_CREAT|O_APPEND,0664))<0)
	{
		perror("open");
		return -1;
	}	
    while(read(fo,buff,1) >0)
	{
		if(buff[0] == '\n')
		line_num ++;
	}
	while(1)
	{
		sys_time = time(NULL);
		loc_time = localtime(&sys_time);
		printf("%d,%04d-%02d-%02d--%02d:%02d:%02d\n",
				line_num,
				loc_time->tm_year+1900,
				loc_time->tm_mon+1,
				loc_time->tm_mday,
				loc_time->tm_hour,
				loc_time->tm_min,
				loc_time->tm_sec);
		sprintf(buff,"%d,%04d-%02d-%02d--%02d:%02d:%02d\n",
				line_num,
				loc_time->tm_year+1900,
				loc_time->tm_mon+1,
				loc_time->tm_mday,
				loc_time->tm_hour,
				loc_time->tm_min,
				loc_time->tm_sec);
		write(fo,buff,strlen(buff));

		line_num++;
		sleep(1);
	}
	close(fo);
	return 0;
}
```

### **标准IO对比文件IO**

| 功能 | 标准IO                               | 文件IO(低级IO) |
| ---- | ------------------------------------ | -------------- |
| 打开 | fopen,freopen,fdopen                 | open           |
| 关闭 | fclose                               | close          |
| 读   | getc,fgetc,getchar,fgets,gets,fread  | read           |
| 写   | putc,fputc,putchar,fputs,puts,fwrite | write          |

## 目录

**打开目录**

> \#include <dirent.h>
>
> DIR *opendir(const char *name);
>
> - DIR *fdopendir(int fd); 使用文件描述符，要配合open函数使用
>
> - DIR是用来描述一个打开的目录文件的结构体类型
> - 成功时返回目录流指针；出错时返回NULL

**读取目录**

> \#include <dirent.h>
>
>  struct dirent *readdir(DIR *dirp);
>
> - struct dirent是用来描述目录流中一个目录项的结构体类型
> - 包含成员char d_name[256]  参考帮助文档
> - 成功时返回目录流dirp中下一个目录项；
> - 出错或到末尾时时返回NULL

**关闭目录**

> closedir函数用来关闭一个目录文件:
>
> \#include <dirent.h>
>
> int closedir(DIR *dirp);
>
> - 成功时返回0；出错时返回EOF

示例

遍历目录并打印

```c
#include <stdio.h>
#include <dirent.h>
int main(int argc, const char *argv[])
{
	DIR* dp;//文件指针
	struct dirent * dt;//文件结构体
	dp = opendir("/home/linux/Study/");
	if(dp == NULL)
	{
		perror("opendir");
		return 0;
	}
	while((dt = readdir(dp)) != NULL)
	{
		printf("%s\n",dt->d_name);
	}
	closedir(dp);
	return 0;
}
```

利用pwd命令获取目录地址

## 文件权限与属性

**修改文件权限**

> chmod/fchmod函数用来修改文件的访问权限:
>
> \#include <sys/stat.h>
>
> int chmod(const char *path, mode_t mode);
>
> int fchmod(int fd, mode_t mode);
>
> - 成功时返回0；出错时返回EOF
> - ==注意：在vmware和windows共享的文件夹下，有些权限不能改变。==

示例

```c
#include <stdio.h>
#include <sys/stat.h>
int main(int argc,char **argv){
   int ret;
   ret = chmod("temp",0444);
   if(ret<0){
        perror("chmod");
        return 0;
    }
}
```

**获取文件属性**

> \#include <sys/stat.h>
>
> int stat(const char *path, struct stat *buf);
>
> int lstat(const char *path, struct stat *buf);
>
> int fstat(int fd, struct stat *buf);
>
> - 成功时返回0；出错时返回EOF
> - 如果path是符号链接stat获取的是目标文件的属性；而lstat获取的是链接文件的属性

**示例:**

遍历目录下的文件，并将文件大小和修改时间打印出来

```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>
#include <time.h>
#include <dirent.h>

int main(int argc, const char *argv[])
{
	DIR* dp;//文件地址
	struct dirent * dt;//文件属性结构体

	struct stat buf;
	int ret,i = 0;

	dp = opendir("/home/linux/Study/");
	if(dp == NULL)
	{
		perror("opendir");
		return 0;
	}
	while((dt = readdir(dp)) != NULL)
	{
		i++;
		ret = stat(dt->d_name,&buf);
		if(ret < 0)
		{
			perror("stat");
		}
		printf("%02d %s ",i,dt->d_name);
		printf("%d ",(int)buf.st_size);
		struct tm *t;
		t = localtime(&buf.st_ctime);
		printf("%d-%d-%d %d:%d\n",
				t->tm_year+1900,
				t->tm_mon+1,
				t->tm_mday,
				t->tm_hour,
				t->tm_min);
	}
	return 0;
}
```

## 静态库与动态库

**静态库与动态库的差异：**

> 1. **链接方式**：静态库在编译时会被整合到目标程序中，形成最终的可执行文件。这意味着如果多个程序使用同一个静态库，每个程序都会有该静态库的一个副本。而动态库是在程序运行时被加载到内存中的，多个程序可以共享同一份动态库的代码。
>2. **资源占用**：由于静态库的内容在编译时就整合到了程序中，因此会使得最终的可执行文件体积较大，占用更多的磁盘空间和内存资源。相比之下，动态库只在需要时才被加载，可以有效节省系统的资源。
> 3. **更新和兼容性**：当静态库更新时，需要重新编译所有使用了该静态库的程序才能使更新生效。而对于动态库，只需替换掉共享的库文件即可，不需要重新编译程序。这使得动态库在升级和维护方面更加灵活和方便。

### **创建静态库：**

> 1.  编写库文件代码,编译hello.c 生成 hello.o文件。
>
>    命令: $gcc -o hello.o -c hello.c
>
> 2.  ar 命令 创建 libhello.a 文件
>
>    命令：$ar -rsv libhello.a hello.o
>
> **链接使用：**
>
> - 编译生成可执行文件
>
>   $gcc -o 目标文件 源码.c -L路径 -lxxxx
>
>   -L 后面写库所在的路径（'.'当前路径）
>
>   -l 后面跟库的名称（不加后缀）
>
>   **注意：**
>
> - 静态库名字要以lib开头，后缀名为.a
> - 没有main函数的.c 文件不能生成可执行文件。
>
> > 查看静态库详细信息：
> >
> > ​	$ar -tv libhello.a
> >
> > ar 命令参数：
> >
> > - c   禁止在创建库时产生的正常消息
> > - r    如果指定的文件已经存在于库中，则替换它
> > - s   无论 ar 命令是否修改了库内容都强制重新**生成库符号表**
> > - v   将建立新库的详细的逐个文件的描述写至标准输出
> > - q   将指定的文件添加到库的末尾
> > - t    将库的目录写至标准输出 

###  **创建动态库：**

> 1. 生成位置无关代码的 .o 目标文件
>
>     $gcc -c  -fPIC  xxx.c xxxx.c ....
>
> 2. 生成动态库
>
>    $gcc -shared -o libxxxx.so xxx.o xxx.o ....
>
>    注意:动态库名字要以lib开头，后缀名为.so
>
> **链接使用：**
>
> - 编译生成执行文件（同静态库）
>
>   $gcc -o 目标文件 源码.c -L路径 -lxxxx

**执行动态库的可执行文件错误**

> 错误信息：./test: error while loading shared libraries: libmyheby.so: cannot open shared object file: No such file or directory
>
> 含义：可执行文件所使用的动态库找不到
>
> **解决办法：**
>
> 方法1：找到动态库，添加到/usr/lib里面
>
> 方法2：使用$export LD_LIBRARY_PATH=/你的动态库目录/
>
> 方法3：将***export LD_LIBRARY_PATH=/你的动态库目录/***添加在~/.bashrc 文件里面。使用"$source ~/.bashrc 生效。

- 命令 ldd :查看可执行文件链接的库
  - 例： $ldd a.out


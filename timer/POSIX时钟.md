POSIX时钟
===
POXIS时钟支持纳秒级的时间精度，Linux调用此API编译时需要加上 `-lrt`，与 `librt`(realtime)
库链接

## 获取时钟的值
```c
#define _POSIX_C_SOURCE 199309
#include <time.h>

int clock_gettime(clockid_t clockid, struct timespec *tp);
int clock_getres(clockid_t clockid, struct timespec *tp);
成功返回0 失败返回-1
```

函数调用成功后返回的值存储在结构 [timespec](../structs/timespec.md)中

clockid_t的可选值
时钟ID | 描述
---|---
CLOCK_REALTIME | 可设定系统级实时时钟
CLOCK_MONOTONIC | 不可设定的恒定态时钟
CLOCK_PROCESS_CPUTIME_ID | 每进程CPU时间的时钟
CLOCK_THREAD_CPUTIME_ID | 没线程CPU时间的时钟

`CLOCK_REALTIME` 的值是真实的系统时间，可以变更

`CLOCK_MONOTONIC` 是系统启动时间开始测量的，适用于不能忍受时间改变的程序（手动更改时钟）

`CLOCK_PROCESS_CPUTIME_ID` 是调用进程消耗的用户和系统CPU时间

`CLOCK_THREAD_CPUTIME_ID` 是每个进程中的单个线程消耗

获取时钟时间并格式化输出

```c
#define _POSIX_C_SOURCE 199309

#include "main.h"
#include <time.h>


void print_time(const char *format,const struct tm *timeptr)
{
  char *out_put;
  out_put = malloc(1024);
  strftime(out_put,1024,format,timeptr);
  printf("%s\n", out_put);
  free(out_put);
}

int main(int argc, const char *argv[])
{
  struct timespec ts;
  clock_gettime(CLOCK_REALTIME,&ts);
  struct tm *timeptr = gmtime(&ts.tv_sec);
  char * format = "%Y年%m月%d日 %H时%M分%S秒";
  timeptr->tm_hour += 8;
  print_time(format,timeptr);
}


## 设置时钟值

```c
#define _POSIX_C_SOURCE 199309
#include <time.h>

int clock_settime(clockid_t clockid,const struct timespec *tp);
成功返回0 失败返回-1
```

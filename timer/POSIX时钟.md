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

## 获取特定进程的时钟id
```c
#define _XOPEN_SOURCE 600
#include <time.h>
int clock_getcpuclockid(pid_t pid, clockid_t *clockid);
成功返回0,失败返回错误号
```

当pid为0时，返回调用进程cpu时间时钟ID


通过 `clock_gettime` 系统调用可以获取 [struct timespec](../structs/timespec.md)结构, 这样就可以测量进程
所消耗的CPU时间


## 获取特定线程运行时间
```c
#define _XOPEN_SOURCE 600
#include <pthread.h>
#include <time.h>
int pthread_getcpuclockid(pthread_t thread, clockid_t *clockid);
```
## 休眠
```c
#define _XOPEN_SOURCE 600
#include <time.h>
int clock_nanosleep(clockid_t clockid, int flags, const struct timespec *request,
                    struct timespec *remain);

成功返回0,失败返回剩余睡眠的秒数
```

## 间隔定时器

### 创建定时器

```c
#define _POSIX_C_SOURCE 199309
#include <signal.h>
#include <time.h>

int timer_create(clockid_t clockid, struct sigevent *evp, timer_t *timerid);
成功返回0，失败返回-1
```
`timer_create` 创建一个新定时器,并以由clockid指定的时钟进行测量

参数`timerid` 会返回一个 `timer_t`的数据类型，供后续使用

结构[struct sigevent](../structs/sigevent.md)指定了定时器到期以哪种方式通知进程

### 设置和移除定时器
```c
#define _POSIX_C_SOURCE 199309
include <time.h>
int timer_settime(timer_t timerid, int flags, const struct itimerspec *value,
                  struct itimerspec *old_value);

成功返回0 失败返回-1
```

参数`timer_t timerid` 是调用 `timer_create`产生的一个资源句柄

参数 `int flags` 如果设置为0, 则是一个相对的时间值，
如果设置为 `TIMER_ABSTIME` 则是一个绝对时间值，参数 `struct itimerspec *value`中的
`it_value` 从时钟值0开始,到达时间后会立即过期

结构 [struct itimerspec](../structs/itimerspec.md) 设置了定时器的值
其中 `it_value` 指定了第一次运行定时器的周期，所以所有参数不能为0，否则定时器无效,
如果 `it_interval` 值不存在，则为一次性定时器

参数`struct itimerspec *old_value` 返回前一定时器设置的值

如果要解除定时器，可将`value`的所有字段值设为0

### 获取定时器
```c
#define _POSIX_C_SOURCE 199309
include <time.h>

int timer_gettime(timer_t timerid, struct itimerspec *curr_value);
成功返回0 失败返回-1
```

### 删除定时器
```c
#define _POSIX_C_SOURCE 199309
#include <time.h>

int timer_delete(timer_t timerid);
成功返回0 失败返回-1
```
对于已经启动的定时器，会在删除前将其停止，同时保持pending信号

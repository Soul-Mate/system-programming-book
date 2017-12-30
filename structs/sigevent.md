struct sigevent 结构
===

```c
union sigval {
  int sigval_int;
  void *sigval_ptr;
};

struct sigevent {
  int sigev_notify;  // 通知的方式
  int sigev_signo;   // 定时器产生的信号
  union sigval sigev_value; // 传递给线程的信号值

  union {
    pid_t _tid; // 线程id
    struct {
      void(*_function)(union sigval); //定时器到期时线程处理函数
      void *_attribute;  // 线程属性
    } _sigev_thread;
  } _sigev_un;
};

#define sigev_notify_function _sigev_un._sigev_thread._function  // 宏定义的线程函数
#define sigev_notify_attributes _sigev_un._sigev_thread._attribute // 宏定义的线程属性
#define sigev_notify_thread_id _sigev_un.tid // 宏定义的线程id
```
`sigev_notify` 的可选值

sigev_notify的值 | 通知方式
---|---
SIGEV_NONE | 不通知，进程可以使用`timer_gettime`监控运行
SIGEV_SIGNAL | 定时器到期生成指定的sigev_signo信号
SIGEV_THREAD | 定时器到期调用线程
SIGEV_THREAD_ID | 和SIGEV_SIGNAL类似，但还会启用线程,发送的信号和线程id相等(Linux特有)

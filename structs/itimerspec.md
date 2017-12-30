struct itimerspec 结构
===

```c
struct itimerspec {
  struct timespec it_interval; // 定时器的间隔周期
  struct timespec it_value; // 定时器第一次运行的时间
}
```

[struct timespec](timespec.md) 用来指定时间，包含秒和纳秒

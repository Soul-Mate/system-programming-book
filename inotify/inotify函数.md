监控文件事件
===

如果程序需要对文件或者目录进行监控,侦查是否发生了特定的事件，自内核2.6.13起，linux开始使用了inotify机制

# 使用inotify的步骤

1. `inotify_init` 创建一个inotify实例
2. `inotify_add_watch` 向inotify实例监控列表添加条目 `inotify_rm_watch` 为逆向操作
3. `read` 调用读取inotify文件描述符，会返回一个或多个 `inotify_event` 结构 其中记录了处于inotify实例监控下发生的事件
4. 监控结束后关闭inotify产生的文件描述符

# inotify API

## 初始化inotify文件描述符

```c
#include <sys/inotify.h>
int inotify_init(void)
成功返回文件描述符 失败返回-1
```

## 添加监控项
```c
#include <sys/inotify.h>
int inotify_add_watch(int fd, const char *pathname, uint32_t mask);
```
pathname 指定了监控项，调用程序必须对该文件具有读权限

如果之前未将pathname加入监控，inotify_add_watch调用后会添加一个新的监控项，并返回一个文件描述符

## 删除监控项
```c
#include <sys/inotify.h>
int inotify_rm_watch(int fd,uint32_t wd);

参数wd是一个监控描述符,由inotify_add_watch调用
```

struct tm
===

struct tm结构体

```
struct tm {
    int tm_sec;  // 秒(0-60)
    int tm_min;  // 分(0-59)
    int tm_hour; // 时(0-23)
    int tm_mday; // 日(1-31)
    int tm_mon;  // 月(0-11)
    int tm_year; // 年
    int tm_wday; // 周几(周末=0)
    int tm_yday; // 这一年的第几天(0-365 1月1日=0)
    int tm_isdst;// 夏令时 (dst = 0 dst < 0 dst > 0)
}
```
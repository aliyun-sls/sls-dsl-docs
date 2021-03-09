# 快速参考

## 数据加工函数总览

快速入门演示了最简单的数据加工算子`e_set`， 完整的数据加工函数总览，参见： [函数总览](https://help.aliyun.com/document_detail/159702.html)


## 字符串处理

检查指定日志字段是否包含特定字符串, 比如 当日志中的test字段包含有hello字段，就增加了一个值为Hello World的msg字段
```
# op_in(字段，字符串)
e_if(op_in(v("test"), "hello"), e_set("msg", "Hello World"))
```

## 时间处理

将标准Unixtime时间戳，转换成人类可读的时间格式。比如 1615274756 转换为2021-03-09 15:25:56
```
# dt_strftimestamp(Unix时间戳,时间格式, 时区)
e_set("system_time_parsed",
    dt_strftimestamp(v("system_time"),"%Y-%m-%d %H:%M:%S","Asia/Shanghai"))
```


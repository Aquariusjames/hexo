---
layout: java
title: python time和datetime模块的使用
date: 2018-12-21 19:55:13
tags:
  - python
  - time
---

# 相关术语

- UTC time Coordinated Universal Time
  
  世界协调时，又称 格林尼治天文时间、世界标准时间。与UTC time对应的是各个时区的local time，东N区的时间比UTC时间早N个小时，因此UTC time + N小时 即为东N区的本地时间；而西N区时间比UTC时间晚N个小时，即 UTC time - N小时 即为西N区的本地时间； 中国在东8区，因此比UTC时间早8小时，可以以UTC+8进行表示。

- epoch time 
  
  表示时间开始的起点；它是一个特定的时间，不同平台上这个时间点的值不太相同，对于Unix而言，epoch time为 1970-01-01 00:00:00 UTC。

- timestamp（时间戳） 也称为Unix时间 或 POSIX时间
  
  它是一种时间表示方式，表示从格林尼治时间1970年1月1日0时0分0秒开始到现在所经过的毫秒数，其值为float类型。 但是有些编程语言的相关方法返回的是秒数（Python就是这样），这个需要看方法的文档说明。需要说明的是时间戳是个差值，其值与时区无关。

# time 模块

|方法/属性 | 描述|
|:--- |:--- |
|time.altzone | 返回与utc时间的时间差，以秒为单位（西区该值为正，东区该值为负）。其表示的是本地DST 时区的偏移量，只有daylight非0时才使用。|
|time.clock() |	返回当前进程所消耗的处理器运行时间秒数（不包括sleep时间），值为小数；该方法Python3.3改成了time.process_time()|
|time.asctime([t]) |	将一个tuple或struct_time形式的时间（可以通过gmtime()和localtime()方法获取）转换为一个24个字符的时间字符串，格式为: "Fri Aug 19 11:14:16 2016"。如果参数t未提供，则取localtime()的返回值作为参数。|
|time.ctime([secs]) |	功能同上，将一个秒数时间戳表示的时间转换为一个表示当前本地时间的字符串。如果参数secs没有提供或值为None，则取time()方法的返回值作为默认值。ctime(secs)等价于asctime(localtime(secs))|
|time.time()	|返回时间戳（自1970-1-1 0:00:00 至今所经历的秒数）|
|time.localtime([secs]) |	返回以指定时间戳对应的本地时间的 struct_time对象（可以通过下标，也可以通过 .属性名 的方式来引用内部属性）格式|
|time.localtime(time.time() + n*3600) |	返回n个小时后本地时间的 struct_time对象格式（可以用来实现类似crontab的功能）|
|time.gmtime([secs]) |	返回指定时间戳对应的utc时间的 struct_time对象格式（与当前本地时间差8个小时）|
|time.gmtime(time.time() + n*3600) |	返回n个小时后utc时间的 struct_time对象（可以通过 .属性名 的方式来引用内部属性）格式|
|time.strptime(time_str, time_format_str) |	将时间字符串转换为struct_time时间对象，如：time.strptime('2017-01-13 17:07', '%Y-%m-%d %H:%M')|
|time.mktime(struct_time_instance) |	将struct_time对象实例转换成时间戳|
|time.strftime(time_format_str, struct_time_instance) |	将struct_time对象实例转换成字符串|

time 的时间格式转换：

![time格式转换](/images/1063221-20170204153018854-1098926175.png)

# datetime 模块

datetime模块提供了处理日期和时间的类，既有简单的方式，又有复杂的方式。它虽然支持日期和时间算法，但其实现的重点是为输出格式化和操作提供高效的属性提取功能。

## datetime模块中定义的类

datetime模块定义了以下几个类：

|类名称	| 描述|
| :--- | :--- |
|datetime.date |	表示日期，常用的属性有：year, month和day|
|datetime.time |	表示时间，常用属性有：hour, minute, second, microsecond|
|datetime.datetime |	表示日期时间|
|datetime.timedelta |	表示两个date、time、datetime实例之间的时间间隔，分辨率（最小单位）可达到微秒|
|datetime.tzinfo |	时区相关信息对象的抽象基类。它们由datetime和time类使用，以提供自定义时间的而调整。|
|datetime.timezone |	Python 3.2中新增的功能，实现tzinfo抽象基类的类，表示与UTC的固定偏移量|

##  datetime模块中定义的常量
|常量名称 | 描述|
| :--- | :---|
|datetime.MINYEAR | datetime.date或datetime.datetime对象所允许的年份的最小值，值为1|
|datetime.MAXYEAR | datetime.date或datetime.datetime对象所允许的年份的最大值，只为9999|

## datetime.datatime 类

class 定义：

        class datetime.datetime(year, month, day, hour=0, minute=0, second=0, microsecond=0, tzinfo=None)

### 类方法和属性

|类方法/属性名称	| 描述|
| --- |--- |
|datetime.today() |	返回一个表示当前本期日期时间的datetime对象|
|datetime.now([tz]) | 返回指定时区日期时间的datetime对象，如果不指定tz参数则结果同上|
|datetime.utcnow() | 返回当前utc日期时间的datetime对象|
|datetime.fromtimestamp(timestamp[, tz]) | 根据指定的时间戳创建一个datetime对象|
|datetime.utcfromtimestamp(timestamp) | 根据指定的时间戳创建一个datetime对象|
|datetime.combine(date, time) | 把指定的date和time对象整合成一个datetime对象|
|datetime.strptime(date_str, format) | 将时间字符串转换为datetime对象|

### 对象方法和属性

|对象方法/属性名称 | 描述|
| --- |--- |
|dt.year, dt.month, dt.day | 年、月、日|
|dt.hour, dt.minute, dt.second | 时、分、秒|
|dt.microsecond, dt.tzinfo | 微秒、时区信息|
|dt.date() | 获取datetime对象对应的date对象|
|dt.time() | 获取datetime对象对应的time对象， tzinfo 为None|
|dt.timetz()	| 获取datetime对象对应的time对象，tzinfo与datetime对象的tzinfo相同|
|dt.replace([year[, month[, day[, hour[, minute[, second[, microsecond[, tzinfo]]]]]]]]) | 生成并返回一个新的datetime对象，如果所有参数都没有指定，则返回一个与原datetime对象相同的对象|
|dt.timetuple() | 返回datetime对象对应的tuple（不包括tzinfo）|
|dt.utctimetuple() | 返回datetime对象对应的utc时间的tuple（不包括tzinfo）|
|dt.toordinal() | 同date对象|
|dt.weekday() | 同date对象|
|dt.isocalendar() | 同date独享|
|dt.isoformat([sep])	| 返回一个‘%Y-%m-%d|
|dt.ctime() | 等价于time模块的time.ctime(time.mktime(d.timetuple()))|
|dt.strftime(format) | 返回指定格式的时间字符串|

datetime 时间格式转换：

![datetime 时间格式转换](/images/1063221-20170205083810667-126583354.png)

# 参考文档

- [Python之日期与时间处理模块](https://blog.csdn.net/p9bl5bxp/article/details/54945920)
从OSS获取IP2Location库进行IP地址数据富化 
=================================================

日志服务数据加工功能支持从OSS获取IP2Location库数据，对日志中的IP地址进行富化，补充IP地址所属的国家、省、市等信息。

前提条件
----

* 已创建访问密钥（AccessKey），用于访问OSS Bucket，详情请参见[为RAM用户创建访问密钥](t162684.html#task-188766)。

  推荐创建一个只读权限的AccessKey，用于从OSS获取文件；一个只写权限的AccessKey，用于上传文件到OSS。授权策略详情请参见[RAM Policy概述](t81295.html#concept-y5r-5rm-2gb)。

* 已从IP2Location官网下载IP地址文件，并上传到OSS，详情请参见[上传文件](t4334.html#concept-zx1-4p4-tdb)。

  使用只写权限的AccessKey进行上传。




背景信息
----

IP2Location提供全球IP地址数据库，可以帮助您精确查找、确定全球范围内IP地址的地理位置。您可以从IP2Location官网下载IP地址文件，上传至OSS。在数据加工过程中，如果您希望根据日志中的IP地址获取国家、省、市等信息，您可以通过[res_oss_file](t1267159.html#concept-1597682/section-mlb-osw-xzd)函数从OSS上获取IP2Location库数据，然后使用[特定结构化数据函数](t908958.html#concept-1130524/section-a6e-5e9-q0c)函数解析IP地址，最后使用[e_set](t947543.html#concept-1180788/section-7cr-8gz-by2)函数将解析结果中的新字段添加到日志中，实现数据富化。

实践案例 
-------------------------

* 原始日志

      ip: 19.0.0.0

  

* 加工规则

      e_set("geo", geo_parse(v("ip"), ip_db=res_oss_file(endpoint='http://oss-cn-hangzhou.aliyuncs.com',
                                                          ak_id=res_local("AK_ID"),
                                                          ak_key=res_local("AK_KEY"),
                                                          bucket='test', 
                                                          file='your ip2location bin file', 
                                                          format='binary', change_detect_interval=20),
                              provider="ip2location"))
      e_json("geo")

  

  res_oss_file函数重要字段说明如下表所示。


  | 字段     | 说明                                                         |
  | -------- | ------------------------------------------------------------ |
  | endpoint | OSS访问域名，详情请参见[访问域名和数据中心](t4350.html#concept-zt4-cvy-5db)。 |
  | ak_id    | 具备只读OSS权限的AccessKey ID。 出于安全考虑，建议配置为res_local("AK_ID")，表示从高级参数配置中获取。高级参数配置操作步骤请参见[创建数据加工任务](t947967.html#task-1181217)。![AccessKey](../images/p136966.png) |
  | ak_key   | 具备只读OSS权限的AccessKey Secret。 出于安全考虑，建议配置为res_local("AK_KEY")表示从高级参数配置中获取。 |
  | bucket   | 用于存储IP地址文件的OSS Bucket。                             |
  | file     | 您已上传的IP地址文件的名称。                                 |
  | format   | 使用res_oss_file函数从OSS获取IP2Location库数据时，文件输出格式需设置为format='binary'。 |

  

  

* 加工结果

      ip: 19.0.0.0
      city: Dearborn
      province: Michigan
      country: United States

  



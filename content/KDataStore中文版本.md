# KDataStore

## 不同存储方案对比
30 份 String 数据 

TODO(给出 KDataStore 的启动时间)
|                          | SharedPreferences        | MMKV                                   | DataStore                      | KDataStore                   | 
|--------------------------|--------------------------|----------------------------------------|--------------------------------|------------------------------|
| 机制                       | commit 同步写入磁盘，如果返回 true 则写入内存, <font color="red">但堵塞主线程 1.5 ms</font> <br/><br/> 一般通过 apply 先写入内存，后异步写入磁盘 | 先读取到系统级别的内存，然后每隔一段时间写入磁盘 | 异步写入磁盘，后更新内存，通过 Flow 异步观察      | 基于 DataStore。先写入内存，即时异步写入磁盘，包括备份文件。 |
| 采取事务(transactional) | <font color="red">No</font>, 但采用了原子操作以改善 | 后续校验数据，尝试恢复，不行则删除 | <font color="green">Yes</font> | <font color="green">Yes</font> | <font color="green">Yes</font>
| 读取中遇 IOException         | 返回默认值                  | 返回默认值                                         | <font color="green"> 自行处理 </font>                           | <font color="green">从备份文件中取</font>                             |
| 写入中遇 IOException         | <font color="red">commit(堵塞) 返回 false</br></br>apply 无反馈</font> | 后续校验数据，尝试恢复，不行则删除。              | 自行处理                           | <font color="green">记录，下次启动时从备份文件中更新</font>          | 
| Corruption<br/>Exception | <font color="red">不方便处理</font>                                                                               | 后续校验数据，尝试恢复，不行则删除                       | 自行处理                       | <font color="green">已通过复制备份文件数据处理</font>                    |
| 多进程共享                    |  自行封装                                                                              | <font color="green">支持</font>                    | 处于 alpha 阶段                    | DataStore 正式支持之后                    |
| 加密                       | 自行封装                                                                            | <font color="green">支持</font>                                     | 自行封装                           | <font color="green">支持</font> |
| 类型 | 常见基本类型 + String + Set&lt;String&gt;  | 常见基本类型 + String + Set&lt;String&gt; + <font color="green">Parcelable</font> | 常见基本类型 + String + Set&lt;String&gt; | <font color="green">常见基本类型 + String + enum + Serializable + KtSerializable + 自定义</font> | 
| 额外信息                      |                                                                               |      <font color="green">ANR前一刻更新的数据不会丢失</font> </br></br> <font color="red">断电或者系统崩溃后容易丢失很多数据</font>                                |      <font color="red"> 第一次同步取数据时耗时 24 ms</font> </br>后续可忽略                         | <font color="green"> 微配置 </font> <br/><br/> <font color="green"> 同时支持单项数据的 nullability (可空性) 和 default (默认值) </font> <br/><br/> <font color="red">初启动时间 30 份数据的时间，300 份数据的时间, 可在 Application 中(或[startup](https://developer.android.com/topic/libraries/app-startup)) 里通过 thread{ } 异步启动 </font> </br></br> <font color="red">比较新，</font> </br> 但基于 `DataStore` 且代码较少, 仅 TODO 行， 即时有 Bug 也比较少。|


## 迁移 (Migration)
`KDataStore` 内置 `protected context` 供你获取其他存储仓库，如 `SharedPreferences`, `DataStore`.

{{< hint info >}}
这份中文版本主要为初期的宣传考虑，其他作品并不会写中文版本和 Java 扩展。
{{< /hint >}}

{{< hint warning >}}
关于其他地方的存储方案分析，绝大多数对比都有失偏颇，大多数测试都有严重错误。
官网介绍相对准确，但经常掩盖缺点，建议配合 ChatGpt 查阅分析。
{{< /hint >}}
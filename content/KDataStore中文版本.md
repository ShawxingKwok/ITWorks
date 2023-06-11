## KReadStore

An extended data store with little configuration, easy encryption and extensive supported types.

## 不同存储方案对比
|                          | SharedPreferences                                                     | MMKV                             | DataStore          | KDataStore                                                        | 
|--------------------------|-----------------------------------------------------------------------|----------------------------------|--------------------|-------------------------------------------------------------------|
| 机制                       | commit 同步写入 <br/>apply 先写入内存，后异步写入磁盘， 其中的 `fsync()` 仍会有轻度堵塞 UI thread                 | mmap 原理，先写入系统级别的内存，然后隔一段时间写入磁盘 | 异步写入磁盘，通过 Flow 收集  | 基于 DataStore, 数据转为 MutableStateFlow。先写入应用程序级别的内存，即时异步写入磁盘，包括备份文件。 |
| 读取耗时                     | 可忽略                                                                   | 可忽略       | 0.04 ms，一般采取 `Flow.collect` 异步观察          | 可忽略                                                               |
| 写入耗时                     | 0.8 ms                                                                                         | 0.02 ms      | 1.6 ms        | 可忽略                                                               |
| 响应耗时                     | 230 ms (commit)<br/>Todo (apply)                                                                | 可忽略      | 3 ms             | 可忽略                                                               |
| 读取中遇 IOException         | 返回 empty map                                                                                    |            | 自行处理           | 从备份文件中取                                                           |
| 写入中遇 IOException         | commit 返回 false <br/> `apply` 通过事务回退，但此时已经事先写入内存，监听到的是没存进去的值                                      |        | 自行处理             | 记录，下次启动时从备份文件中更新                                                  | 
| Corruption<br/>Exception |                                                                                               | 断电或者系统崩溃后常见, 无法处理  | 不常见，自行处理 | 复制备份文件数据                                                          |
| 多进程共享                    | 支持                                                                                        | 支持     | 处于 alpha 阶段              | DataStore 正式支持之后                                                  |

可看出 DataStore 牺牲了性能换取数据安全。虽然这点牺牲的性能通过异步可以让用户感知不到，但异步总不如同步方便。

在 `DataStore<Preferences>` 的基础上，我封装了一个存储框架 KDataStore，主要有以下优点
- 微配置
- 通过 MutableStateFlow, 同步读写、异步观察都很方便
- 耗时可忽略
- 可加密
- 妥善处理了异常
- 多类型
  - `Int`, `Long`, `Float`, `Double`, `Boolean`, `String`, `enum`, `Serializable`
  - 使用 `Kotlin serialization` 的类型
  - 其他类型(需附上与 `Serializable` 的转换)


{{< hint info >}}
虽然比较新，但基于 `DataStore` 且代码较少, 仅 TODO 行， 有严重 Bug 的可能性较低。
{{< /hint >}}

{{< hint info >}}
这份中文版本主要为初期的宣传考虑，其他作品并不会写中文版本。
{{< /hint >}}

{{< hint warning >}}
关于其他地方的存储方案分析，绝大多数对比都有失偏颇，绝大多数测试都有严重错误。
官网介绍相对准确，但经常掩盖缺点，建议配合 ChatGpt 查阅分析。
{{< /hint >}}
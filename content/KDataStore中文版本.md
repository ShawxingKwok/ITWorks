# KDataStore

- 多类型
  - `Int`, `Long`, `Float`, `Double`, `Boolean`, `String`, `enum`, `Serializable`, `Kt Serializable`
  - 其他类型(需附上与 `Serializable` 的转换)

## 不同存储方案对比
|                          | SharedPreferences                                                     | MMKV                           | DataStore                        | KDataStore                                                        | 
|--------------------------|-----------------------------------------------------------------------|--------------------------------|----------------------------------|-------------------------------------------------------------------|
| 机制                       | commit 同步写入 <br/>apply 先写入内存，后异步写入磁盘， 其中的 `fsync()` 仍会有轻度堵塞 UI thread | mmap 原理，先写入系统级别的内存，然后隔一段时间写入磁盘 | 异步写入磁盘，通过 Flow 收集                | 基于 DataStore, 数据转为 MutableStateFlow。先写入应用程序级别的内存，即时异步写入磁盘，包括备份文件。 |
| 读取耗时                     | 可忽略                                                                   | 可忽略                            | 0.04 ms，一般采取 `Flow.collect` 异步观察 | 可忽略                                                               |
| 写入耗时                     | 0.8 ms                                                                | 0.02 ms                        | 1.6 ms                           | 可忽略                                                               |
| 响应耗时                     | 230 ms (commit)<br/>Todo (apply)                                      | 可忽略                            | 3 ms                             | 可忽略                                                               |
| 一般使用方式                   | 同步读取，异步写入，异步观察                                                        | 同步读取，同步写入                      | 异步观察，异步写入                        | 同步读写，异步观察                                                         |
| 读取中遇 IOException         | 取默认值                                                                  |                                | 自行处理                             | 从备份文件中取                                                           |
| 写入中遇 IOException         | commit 返回 false <br/> `apply` 通过事务回退，但此时已经事先写入内存，监听到的是没存进去的值          |                                | 自行处理                             | 记录，下次启动时从备份文件中更新                                                  | 
| Corruption<br/>Exception |                                                                       | 断电或者系统崩溃后常见, 无法处理              | 不常见，自行处理                         | 不常见，通过复制备份文件数据处理                                                  |
| 多进程共享                    | 支持                                                                    | 支持                             | 处于 alpha 阶段                      | DataStore 正式支持之后                                                  |
| 类型支持                     | 常见基本类型                                                                | 常见基本类型, Parcelable             | 常见基本类型, 自定义(繁琐)                  | 广泛                                                                |
| 加密                       | 自行封装                                                                  | 支持                             | 自行封装                             | 支持                                                                |
| 配置                       | 繁琐                                                                    | 繁琐                             | 繁琐                               | 简单                                                                |
| 是否支持 Java                | 是                                                                     | 是                              | 是                                | <font color="red">否</font>                                        | 
| 使用量                      | 极多                                                                    | 多                              | 多                                | <font color="red">少</font>                                        |

{{< hint info >}}
虽然 KDataStore 比较新，但基于 `DataStore` 且代码较少, 仅 TODO 行， 即时有 Bug 也比较少。
{{< /hint >}}

## 迁移
`KDataStore` 内置 `protected context` 供你获取其他存储仓库，如 `SharedPreferences`, `DataStore`.

{{< hint info >}}
这份中文版本主要为初期的宣传考虑，其他作品并不会写中文版本和 Java 扩展。
{{< /hint >}}

{{< hint warning >}}
关于其他地方的存储方案分析，绝大多数对比都有失偏颇，绝大多数测试都有严重错误。
官网介绍相对准确，但经常掩盖缺点，建议配合 ChatGpt 查阅分析。
{{< /hint >}}
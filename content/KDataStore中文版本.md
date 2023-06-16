# KDataStore

## 不同存储方案对比
|                          | SharedPreferences                                                                                  | MMKV                                                                                             | DataStore                                              | KDataStore                                                                                                     | 
|--------------------------|----------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|--------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| 机制                       | 写入时先更新内存 </br></br>`commit` 同步写入磁盘, <font color="red">但堵塞主线程 1.4 ms</font> <br/> 一般通过 `apply` 异步写入 | 先读取到系统级别的内存</br></br><font color="green">同步不堵塞读写</font></br></br>每隔一段时间后台异步写入磁盘                  | 异步写入磁盘，成功后更新内存。</br></br>通过 Flow 异步观察。 响应时间 2.1 ms     | 基于 DataStore </br></br> 先读取到应用级别的内存 </br></br> <font color="green">同步不堵塞读写</font></br></br>数据更新时异步写入磁盘，包括备份文件。 |
| 采取事务(transactional)      | <font color="red">No</font>, 但采用了 synchronized 操作和备份文件以改善                                          | 后续校验数据，尝试恢复，不行则删除                                                                                | <font color="green">Yes</font>                         | <font color="green">Yes</font>                                                                                 | <font color="green">Yes</font>
| 读取中遇 IOException         | 返回默认值                                                                                              | 返回默认值                                                                                            | 自行 catch 处理                                            | <font color="green">从备份文件中取</font>                                                                             |
| 写入中遇 IOException         | 用未写入该数据的备份文件替换。</br></br> 如用 `commit`, 可通过返回的 false 获悉                                             | 后续校验数据，尝试恢复，不行则删除。                                                                               | 自行 catch 处理                                            | <font color="green">记录，下次启动时从备份文件中更新</font>                                                                    | 
| Corruption<br/>Exception | <font color="red">用空文件替换</font>                                                                    | 后续校验数据，尝试恢复，不行则删除                                                                                | 自行 catch  处理                                           | <font color="green">已通过复制备份文件数据处理</font>                                                                       |
| 多进程共享                    | 自行封装入 ContentResolver                                                                              | <font color="green">支持</font>                                                                    | 处于 alpha 阶段                                            | DataStore 正式支持之后                                                                                               |
| 加密                       | 自行封装                                                                                               | <font color="green">支持</font>                                                                    | 自行封装                                                   | <font color="green">支持</font>                                                                                  |
| 类型                       | 常见基本类型 + String + Set&lt;String&gt;                                                                | 常见基本类型 + String + Set&lt;String&gt; + <font color="green">Parcelable</font>                      | 常见基本类型 + String + Set&lt;String&gt;                    | <font color="green">常见基本类型 + String + enum + Serializable + KtSerializable + 自定义</font>                        | 
| 额外信息                     |                                                                                                    | <font color="green">ANR前一刻更新的数据不会丢失</font> </br></br> <font color="red">断电或者系统崩溃后容易丢失很多数据</font> | <font color="red"> 第一次同步取数据时耗时 24 ms</font> </br>后续可忽略 | 见表格下方                                                                                                          |


- <span style="color: green; "> 微配置，调用方便 </span> 
- <span style="color: green; "> 同时支持单项数据的 nullability (可空性) 和 default (默认值) </span> 
- <span style="color: red; "> 初启动时间为 10 ms，</span>但数据显著增加并不会影响多少。如果介意亦可在 Application 中(或[startup](https://developer.android.com/topic/libraries/app-startup)) 里异步启动  
- <span style="color: red; "> 比较新，</span> 但基于 `DataStore` 且代码较少, 仅 TODO 行， 即时有 Bug 也比较少。

以上测试结果采用 30 份 7(key length) * 20(value length) 的 String 数据。

## 迁移 (Migration)
`KDataStore` 内置 `protected appContext` 供你获取其他存储仓库，如 `SharedPreferences`, `MMKV`, `DataStore` 等。

{{< hint info >}}
这份中文版本主要为初期的宣传考虑，其他作品并不会写中文版本和 Java 扩展。
{{< /hint >}}

{{< hint warning >}}
关于其他地方的存储方案分析，绝大多数对比都严重遗漏和偏颇，大多数测试都有严重错误。
官网相对准确，但经常掩盖缺点，建议配合 ChatGpt 查阅分析。
{{< /hint >}}
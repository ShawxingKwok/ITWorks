---
title: 中文 
weight: 2
---

# KDataStore

## 不同存储方案对比

|                          | SharedPreferences                                                                                                         | MMKV                                                                                             | DataStore                                          | KDataStore                                                                                                     |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| 机制                       | 写入时先更新内存 </br></br>`commit` 同步写入磁盘, <font color="red">但堵塞主线程 1.4 ms</font> <br/> 一般通过 `apply` 异步写入</br></br>不论写入结果如何，通知监听 | 先读取到系统级别的内存</br></br><font color="green">同步不堵塞读写</font></br></br>每隔一段时间后台异步写入磁盘                  | 异步写入磁盘，成功后更新内存。</br></br>通过 Flow 异步观察。 响应时间 2.1 ms | 基于 DataStore </br></br> 先读取到应用级别的内存 </br></br> <font color="green">同步不堵塞读写</font></br></br>数据更新时异步写入磁盘，包括备份文件。 |
| 采取事务(transactional)      | <font color="red">No</font>, 但采用了 `synchronized` 操作和备份文件以改善                                                               | 后续校验数据，尝试恢复，不行则删除                                                                                | <font color="green">Yes</font>                     | <font color="green">Yes</font>                                                                                 | <font color="green">Yes</font>
| 读取中遇 IOException         | 返回默认值                                                                                                                     | 返回默认值                                                                                            | 自行 catch 处理                                        | <font color="green">从备份文件中取</font>                                                                             |
| 写入中遇 IOException         | 用未写入该数据的备份文件替换。</br></br> 如用 `commit`, 可通过返回的 false 获悉                                                                    | 后续校验数据，尝试恢复，不行则删除。                                                                               | 自行 catch 处理                                        | <font color="green">记录，下次启动时从备份文件中更新</font>                                                                    |
| Corruption<br/>Exception | <font color="red">用空文件替换</font>                                                                                           | 后续校验数据，尝试恢复，不行则删除                                                                                | 自行 catch  处理                                       | <font color="green">已通过复制备份文件数据处理</font>                                                                       |
| 多进程共享                    | 自行封装入 ContentResolver                                                                                                     | <font color="green">支持</font>                                                                    | 处于 alpha 阶段                                        | DataStore 正式支持之后                                                                                               |
| 加密                       | 自行封装                                                                                                                      | <font color="green">支持</font>                                                                    | 自行封装                                               | <font color="green">支持</font>                                                                                  |
| 类型                       | 常见基本类型 + String + Set&lt;String&gt;                                                                                       | 常见基本类型 + String + Set&lt;String&gt; + <font color="green">Parcelable</font>                      | 常见基本类型 + String + Set&lt;String&gt;                | <font color="green">常见基本类型 + String + enum + Serializable + KtSerializable + 自定义</font>                        |
| 额外信息                     |                                                                                                                           | <font color="green">ANR前一刻更新的数据不会丢失</font> </br></br> <font color="red">断电或者系统崩溃后容易丢失很多数据</font> |                                                    | 见表格下方                                                                                                          |


- <span style="color: green; "> 微配置，调用方便 </span>
- <span style="color: green; "> 同时支持单项数据的 nullability (可空性) 和 default (默认值) </span>
- <span style="color: red; "> 初启动时间为 10 ms，</span>但数据显著增加并不会影响多少。如果介意亦可在 Application 中(或[startup](https://developer.android.com/topic/libraries/app-startup)) 里异步启动
- <span style="color: red; "> 比较新，</span> 但基于 `DataStore` 且代码较少, 仅 TODO 行， 即时有 Bug 也比较少。

以上测试结果采用 30 份 7(key length) * 20(value length) 的 String 数据。


## 基础用法预览

{{< tabs "Preview">}}

{{< tab "view-kt" >}}
<video width="720" controls>
  <source src="../view.mp4" type="video/mp4">
</video>
{{< /tab >}}

{{< tab "view-java" >}}
<video width="720" controls>
  <source src="../view.mp4" type="video/mp4">
</video>
{{< /tab >}}

{{< tab "compose" >}}
<video width="720" controls>
  <source src="../compose.mp4" type="video/mp4">
</video>
{{< /tab >}}

{{< /tabs >}}

## 配置

Get a [sample](../demo.zip) or configure as below.

{{< tabs "setup" >}}
{{< tab "view" >}}
{{< tabs "setup view" >}}
{{< tab "groovy" >}}
```groovy
//region KDataStore
android.lintOptions.disable 'NonConstantResourceId'

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += '-Xcontext-receivers'
}

dependencies {
    implementation 'io.github.shawxingkwok:kt-util:1.0.0'
    implementation 'io.github.shawxingkwok:android-util-view:1.0.0'
    implementation 'io.github.shawxingkwok:android-kdatastore:1.0.0'
}
//endregion 
```
{{< /tab >}}
{{< tab "kotlin" >}}

```kotlin
//region KDataStore
android.lintOptions.disable ("NonConstantResourceId")

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation ("io.github.shawxingkwok:kt-util:1.0.0")
    implementation ("io.github.shawxingkwok:android-util-view:1.0.0")
    implementation ("io.github.shawxingkwok:android-kdatastore:1.0.0")
}
//endregion
```
{{< /tab >}}
{{< /tabs >}}
{{< /tab >}}

{{< tab "compose" >}}
{{< tabs "setup compose" >}}
{{< tab "groovy" >}}
```groovy
//region KDataStore
android.lintOptions.disable 'NonConstantResourceId'

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += '-Xcontext-receivers'
}

dependencies {
    implementation 'io.github.shawxingkwok:kt-util:1.0.0'
    implementation 'io.github.shawxingkwok:android-kdatastore-compose:1.0.0'
}
//endregion
```
{{< /tab >}}
{{< tab "kotlin" >}}

```kotlin
//region KDataStore
android.lintOptions.disable ("NonConstantResourceId")

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation ("io.github.shawxingkwok:kt-util:1.0.0")
    implementation ("io.github.shawxingkwok:android-kdatastore-compose:1.0.0")
}
//endregion
```
{{< /tab >}}
{{< /tabs >}}
{{< /tab >}}
{{< /tabs >}}

## Core Usage
### Set data model
<image src="../config.png" width=550></image>

### Update
```kotlin

// suspend 
Settings.isDarkMode.emit(true)
Settings.isDarkMode.emit{ !it }
```
That type `Flow` is declared inside `class KDataStore` and extends from the official `Flow`, being both
collectable and mutable.

<image src="../flow.png"></image>

### Collect
{{< tabs "collect" >}}
{{< tab "view" >}}
I suggest my `collectOnResume` if in `Fragment`.
```kotlin
class MainFragment : Fragment(){
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        Settings.isDarkMode.collectOnResume{
            switch.isChecked = it
        }
    }
}
```
{{< /tab >}}

{{< tab "compose" >}}
I suggest my flow state extension.

<image src="../_compose collection.png"></image>

---

use
<image src="../compose collection.png"></image>
{{< /tab >}}

{{< /tabs >}}

## Kotlin serialization
### Usage
It's an official platform-neutral data conversion, and directly savable in my `KDataStore`.

```kotlin
import kotlinx.serialization.Serializable
import pers.shawxingkwok.kdatastore.KDataStore
        
@Serializable
data class Data(val a: Int, val b: String)

object MDataStore: KDataStore(){
    val data by ktSerializable(Data(1, ""))
}
```

### Config
{{< tabs "Kt serialization config" >}}
{{< tab "Groovy" >}}
```groovy
plugins{
    id 'kotlinx-serialization' version "$kt_version"
}
```

```groovy
dependencies {
    implementation 'org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.0'
}
```
{{< /tab >}}
{{< tab "Kotlin" >}}
```groovy
plugins{
    id ("kotlinx-serialization") version ("$kt_version")
}
```

```groovy
dependencies {
    implementation ("org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.0")
}
```
{{< /tab >}}
{{< /tabs >}}

## 迁移
`KDataStore` 内置 `appContext` 供你获取其他存储仓库，如 `SharedPreferences`, `MMKV`, `DataStore` 等。

## 加密

{{< hint info >}}
默认的 AES 实现部分有参考<a href="https://github.com/MFlisar/MaterialPreferences" target="_blank">别人的设计。</a>
{{< /hint >}}

## Options
### scope
TODO(handlerScope, ioScope)

### file name

## Details
### Reset

### updateAll

</br></br>
{{< hint info >}}
## <a href="https://github.com/ShawxingKwok/KDataStore" target="_blank">GitHub repo</a>
{{< /hint >}}

{{< hint warning >}}
这份中文版本主要为初期的宣传考虑，其他作品并不会写中文版本和 Java 扩展。
{{< /hint >}}

{{< hint danger >}}
关于其他地方的存储方案分析，绝大多数对比都严重遗漏和偏颇，大多数测试都有严重错误。
官网相对准确，但经常掩盖缺点，建议配合 ChatGpt 查阅分析。
{{< /hint >}}
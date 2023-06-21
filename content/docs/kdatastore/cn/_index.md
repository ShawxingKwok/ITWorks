---
title: 中文 
weight: 2
---

# KDataStore

## 不同存储方案对比

<table>
<tr>
    <th></th>
    <th>SharedPreferences</th>
    <th>MMKV</th>
    <th>DataStore</th>
    <th>KDataStore</th>
</tr>

<tr>
    <td>机制</td>
    <td>
        写入时先更新内存 
        <br><br>commit 同步写入磁盘, <span style="color: red; ">但堵塞主线程 1.4 ms</span> 
        <br/> 一般通过 apply 异步写入<br><br>不论写入结果如何，通知监听
    </td>
    <td> 
        先读取到系统级别的内存
        <br><br><span style="color: green; ">同步不堵塞读写</span>
        <br><br>每隔一段时间后台异步写入磁盘 
    </td>
    <td> 异步写入磁盘，成功后更新内存。<br><br>通过 Flow 异步观察。 响应时间 2.1 ms </td>
    <td> 
        基于 DataStore 
        <br><br> 先读取到应用级别的内存 
        <br><br> <span style="color: green; ">同步不堵塞读写</span>
        <br><br>数据更新时异步写入磁盘，包括备份文件。 
    </td>
</tr>
    
<tr>
    <td>启动时间(ms)</td>
    <td>2.2</td>
    <td>3.2</td>
    <td>49(异步)</td>
    <td><span style="color: red; ">13</span>, 文件显著增加时影响不大</td>
</tr>

<tr>
    <td>读取中遇 IOException</td>
    <td>返回默认值</td>
    <td>返回默认值</td>
    <td rowspan="3">自行 catch 处理</td>
    <td><span style="color: green; ">从备份文件中取</span></td>
</tr>

<tr>
    <td>写入中遇 IOException</td>
    <td>用未写入该数据的备份文件替换。<br><br> 如用 commit, 可通过返回的 false 获悉</td>
    <td rowspan="2">后续校验数据，尝试恢复，不行则删除。</td>
    <td><span style="color: green; ">记录，下次启动时从备份文件中更新</span></td>
</tr>

<tr>
    <td> Corruption<br>Exception </td>
    <td> <span style="color: red; ">用空文件替换</span> </td>
    <td> <span style="color: green; ">通过复制备份文件数据处理</span> </td>
</tr>

<tr>
    <td>多进程共享</td>
    <td>自行封装入 ContentResolver</td>
    <td> <span style="color: green; ">支持</span> </td>
    <td>处于 alpha 阶段</td>
    <td>DataStore 正式支持之后</td>
</tr>

<tr>
    <td>加密</td>
    <td>自行封装</td>
    <td><span style="color: green; ">支持</span></td>
    <td>自行封装</td>
    <td> <span style="color: green; ">支持</span> </td>
</tr>

<tr>
    <td>类型安全</td>
    <td>否</td>
    <td>否</td>
    <td><span style="color: green; ">是</span></td>
    <td><span style="color: green; ">是</span></td>
</tr>

<tr>
    <td>除常见基本类型, String, Set&lt;String&gt; 外的类型支持</td>
    <td></td>
    <td> <span style="color: green; ">Parcelable</span></td>
    <td><span style="color: green; ">自定义</span><br>但需放在独立的 DataStore 中</td>
    <td> <span style="color: green; "> enum <br> Serializable <br> KtSerializable <br> 自定义</span></td>
</tr>

<tr>
    <td>额外优点</td>
    <td></td>
    <td> 
        <span style="color: green; ">ANR前一刻更新的数据不会丢失</span> 
    </td>
    <td></td>
    <td>
        <span style="color: green; "> 建模简单，调用方便 </span>
        <br><br><span style="color: green; "> 支持单项数据的 nullability (可空性) 和 default (默认值) </span>
    </td>
</tr>

<tr>
    <td>额外缺点</td>
    <td></td>
    <td><span style="color: red; ">断电或者系统崩溃后容易丢失很多数据</span></td>
    <td></td>
    <td><span style="color: red; "> 比较新，</span> 但基于官方的 DataStore 且代码较少, 仅 TODO 行， 即时有 Bug 也比较少。</td>
</tr>
</table>

以上测试结果采用 30 份 7(key length) * 20(value length) 的 String 数据，机型魅族18s。代码在[源码](https://github.com/ShawxingKwok/KDataStore/archive/refs/heads/master.zip)中。

{{< hint warning >}}
关于其他地方的存储方案对比分析，绝大多数都有严重错误。
官网相对准确，但也很片面。如想探究，建议看源码并查阅 ChatGpt 分析。
{{< /hint >}}

## 基础用法
{{< tabs "Preview">}}

{{< tab "view-kt" >}}
<video width="720" controls>
  <source src="../view-kt.mp4" type="video/mp4">
</video>
{{< /tab >}}

{{< tab "view-java" >}}
<video width="720" controls>
  <source src="../view-java.mp4" type="video/mp4">
</video>
{{< /tab >}}

{{< tab "compose" >}}
<video width="720" controls>
  <source src="../compose.mp4" type="video/mp4">
</video>
{{< /tab >}}

{{< /tabs >}}

## 配置

或可直接看[源码](https://github.com/ShawxingKwok/KDataStore/archive/refs/heads/master.zip)中的 demo。

### 根目录 
`build.gradle`
{{< tabs "root plugins" >}}
{{< tab "Groovy" >}}
```
plugins{
    ...
    id 'org.jetbrains.kotlin.plugin.serialization' version "$version_kt" apply false
}
```
{{< /tab >}}

{{< tab "Kts" >}}
```
plugins{
    ...
    id ("org.jetbrains.kotlin.plugin.serialization") version "$version_kt" apply false
}
```
{{< /tab >}}
{{< /tabs >}}

### 模型模块
单独分出一个 module, 常见命名为 `settings`, （如果不采纳，后续的 `settings` 命名则一并更改）
language 选择 **kotlin** 而非 java。

`build.gradle`
{{< tabs "settings plugins" >}}
{{< tab "Groovy" >}}
```
plugins {
    ...
    id 'kotlinx-serialization'
}

dependencies {
    ...
    implementation 'org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.0'
    implementation 'io.github.shawxingkwok:kt-util:1.0.0'
    implementation 'io.github.shawxingkwok:android-kdatastore:1.0.0'
    implementation 'androidx.startup:startup-runtime:1.1.1'
}
```
{{< /tab >}}
{{< tab "Kts" >}}
```
plugins {
    ...
    id ("kotlinx-serialization")
}

dependencies {
    ...
    implementation ("org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.0")
    implementation ("io.github.shawxingkwok:kt-util:1.0.0")
    implementation ("io.github.shawxingkwok:android-kdatastore:1.0.0")
    implementation ("androidx.startup:startup-runtime:1.1.1")
}
```
{{< /tab >}}
{{< /tabs >}}

### 调用方 `build.gradle`
{{< tabs "caller side" >}}

{{< tab "Groovy" >}}
{{< tabs "Groovy">}}
{{< tab "view-kt" >}}
```groovy
tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    ...
    implementation 'io.github.shawxingkwok:android-util-view:1.0.0'
    implementation 'io.github.shawxingkwok:android-kdatastore:1.0.0'
    // 引入刚才设置的 module
    implementation project(':settings') 
}
```
{{< /tab >}}

{{< tab "view-java / compose" >}}
```groovy
dependencies{
    ...
    implementation 'io.github.shawxingkwok:android-kdatastore:1.0.0'
    // 引入刚才设置的 module
    implementation project(':settings') 
}
```
{{< /tab >}}
{{< /tabs >}}
{{< /tab >}}

{{< tab "Kts" >}}
{{< tabs "Kts">}}
{{< tab "view-kt" >}}
```kotlin
tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach{
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    ...
    implementation ("io.github.shawxingkwok:android-util-view:1.0.0")
    implementation ("io.github.shawxingkwok:android-kdatastore:1.0.0")
    // 引入刚才设置的 module
    implementation (project(":settings")) 
}
```
{{< /tab >}}

{{< tab "view-java / compose" >}}
```kotlin
dependencies{
    ...
    implementation ("io.github.shawxingkwok:android-kdatastore:1.0.0")
    // 引入刚才设置的 module
    implementation (project(":settings")) 
}
```
{{< /tab >}}
{{< /tabs >}}
{{< /tab >}}
{{< /tabs >}}

## 类型支持
### 常见基本类型

### Java Serializable

### Kotlin serializable
It's an official platform-neutral data conversion.

```kotlin
import kotlinx.serialization.Serializable
import pers.shawxingkwok.kdatastore.KDataStore
        
@Serializable
data class Data(val a: Int, val b: String)

object MDataStore: KDataStore(){
    val data by ktSerializable(Data(1, ""))
}
```

### Nullability
nullable 时对 default 限制为 null

## 迁移
`KDataStore` 内置 `appContext` 供你获取其他存储仓库，如 `SharedPreferences`, `MMKV`, `DataStore` 等。

## 加密

## Options
### scope
TODO(handlerScope, ioScope)

### file name

## Details

Reset
    全局
    局部
 
如果在 Application 中要调用 `Settings`, 应以异步的方式，
```kotlin
CoroutineScope(Dispatchers.Main.Immediate).launch{
    Settings.xx.collect{
        ...
    }
}
```

</br></br>
{{< hint info >}}
## <a href="https://github.com/ShawxingKwok/KDataStore" target="_blank">GitHub repo</a>
{{< /hint >}}

{{< hint warning >}}
这份中文版本主要为初期的宣传考虑，其他作品并不会写中文版本和 Java 扩展。
{{< /hint >}}
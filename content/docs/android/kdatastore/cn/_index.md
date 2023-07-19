---
title: 中文 
weight: 2
---

{{< hint warning >}}
这份中文版本和 `Java` 扩展主要用作初期宣传，其他作品并不会有。
{{< /hint >}}

{{< hint warning >}}
在支持 `IOS` 之后会移到 `Multiplatform mobile` 分组中。
{{< /hint >}}

# 本地快捷存储方案对比

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
        同步读写
        <br><br>
        <span style="color:red">commit 堵塞当前线程</span>
        <br> apply 不堵塞，<span style="color:red">但不知道是否成功写入磁盘。</span>
        <br><br><span style="color:red">不论是否成功写入，都更新内存，通知监听</span>
    </td>
    <td> 
        先读取到系统级别的内存
        <br><br><span style="color: green; ">同步不堵塞读写</span>
        <br><br>后台定时异步写入磁盘 
    </td>
    <td> 异步读写，写入成功后更新内存。<br><br>通过 Flow 异步观察。</td>
    <td> 
        基于 DataStore 
        <br><br> 先读取到应用级别的内存 
        <br><br> <span style="color: green; ">同步不堵塞读写</span>
        <br><br>数据更新时异步写入磁盘，包括备份文件。 
    </td>
</tr>
    
<tr>
    <td>性能(ms)</td>
    <td>启动: 2.1 <br> commit: 2.4 </td>
    <td>启动: 2.6</td>
    <td>响应: 8</td>
    <td><span style="color: red; ">启动: 18</span> <br> 文件显著增加时影响不大</td>
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
    <td>用未写入该数据的备份文件替换，且不再写入。<br><br> 如用 commit, 可通过返回的 false 获悉</td>
    <td rowspan="2">后续校验数据，尝试恢复，不行则删除。</td>
    <td><span style="color: green; ">记录，下次启动时从备份文件中更新</span></td>
</tr>

<tr>
    <td> Corruption<br>Exception </td>
    <td> <span style="color: red; ">用空文件替换</span> </td>
    <td> <span style="color: green; ">通过复制备份文件数据处理</span> </td>
</tr>

<tr>
    <td>多进程</td>
    <td>自行封装</td>
    <td> <span style="color: green; ">支持</span> </td>
    <td rowspan="2">处于 alpha 阶段</td>
    <td rowspan="2">DataStore 正式支持之后</td>
</tr>

<tr>
    <td>多平台</td>
    <td><span style="color: red; ">不支持</span></td>
    <td> <span style="color: red; ">不支持</span> </td>
</tr>

<tr>
    <td>加密</td>
    <td>自行封装</td>
    <td><span style="color: green; ">支持</span></td>
    <td>自行封装</td>
    <td> <span style="color: green; ">需引入其他加密库</span> </td>
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
    <td> <span style="color: green; ">Kt Serializable (包括常见存储类型）<br><br>自定义</span></td>
</tr>

<tr>
    <td>额外优点</td>
    <td></td>
    <td> 
        <span style="color: green; ">ANR前一刻更新的数据不会丢失</span> 
    </td>
    <td></td>
    <td>
        <span style="color: green; "> 建模简单，调用方便 <br><br> 体积小，jar on Android side 仅 8 kb </span>
    </td>
</tr>

<tr>
    <td>额外缺点</td>
    <td></td>
    <td><span style="color: red; ">断电或者系统崩溃后容易丢失很多数据</span></td>
    <td></td>
    <td><span style="color: red; "> 比较新 </span> 
        <br><br> <span style="color: red; ">建模时只能使用 Kotlin, </span> 只会 Java 的同学需稍掌握一点 Kotlin 知识。
    </td>
</tr>
</table>

以上测试结果采用 30 份 7(key length) * 20(value length) 的 String 数据，机型魅族18s。代码在[源码](https://github.com/ShawxingKwok/KDataStore/archive/refs/heads/master.zip)中 api 的 androidTest。

{{< hint warning >}}
关于其他地方的存储方案对比分析，绝大多数都有严重错误。
官网相对准确，但也很片面。如想探究，建议自己测试并查阅源码。
{{< /hint >}}

# 基础用法
用 Dark theme 举例 

## UI 展示
<video height="200" controls>
  <source src="../effect.mov" type="video/mp4">
</video>

## 建模
单独分出一个 `Android` 模块, 常见命名为 `settings`。 （后续的文档均按照 `settings`）
{{< codeImg "../model.png" >}}
<br> 

关于 `KDSFlow` 
{{< codeImg "../kdsFlowExpect.png" >}}
{{< codeImg "../kdsFlowActual.png" >}}

{{< hint warning >}}
其中的 `liveData` 为 `Android` 专供，在后续的其他平台上并不支持。
{{< /hint >}}

## 调用

在其他模块中调用 
{{< tabs "Preview">}}

{{< tab "view-kt" >}}
1. 在 `Activity`/`BasicActivity` 中观察`Flow`/`LiveData`，绑定主题。  
2. `checked RadioButton` 会随用户点击自动变化，根据 `isDarkMOde.value` 设置起始状态即可，不用绑定。  
3. 在 `RadioGroup` 监听中更新存值。  
{{< codeImg "../kt_basic_usage.png" >}}
{{< hint info >}}
在 `Fragment` 中观察 `Flow` 时建议采用 
{{< newTab collectOnResume "https://shawxingkwok.github.io/ITWorks/docs/android/util-view/#flowcollectonresume" >}}。
{{< /hint >}}
{{< /tab >}}

{{< tab "view-java" >}}
1. 在 `Activity`/`BasicActivity` 中观察 `LiveData`，绑定主题。
2. `checked RadioButton` 会随用户点击自动变化，根据 `isDarkMode().getValue()` 设置起始状态即可，不用绑定。
3. 在 `RadioGroup` 监听中更新存值。  
{{< codeImg "../java_basic_usage.png" >}}

{{< /tab >}}

{{< tab "compose" >}}
将 `State` 和主题绑定  
{{< codeImg "../composeTheme.png" >}}

`RadioButton`处更新存值
{{< codeImg "../composeRadioButton.png" >}}
{{< /tab >}}

{{< /tabs >}}

# 配置

配置相应 `build.gradle`, 或可见[源码](#release)中的 demo。

## 根目录 
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

## 模型模块
单独分出一个 module, 常见命名为 `settings`, （如果不采纳，后续的 `settings` 命名则一并更改）
language 选择 **kotlin** 而非 java。

{{< tabs "settings plugins" >}}
{{< tab "Groovy" >}}
```
plugins {
    ...
    id 'kotlinx-serialization'
}

dependencies {
    ...
    implementation 'org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.1'
    implementation 'io.github.shawxingkwok:kt-util:1.0.0'
    implementation 'io.github.shawxingkwok:android-util-core:1.0.0'
    implementation 'io.github.shawxingkwok:kdatastore:1.0.0'
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
    implementation ("org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.1")
    implementation ("io.github.shawxingkwok:kt-util:1.0.0")
    implementation ("io.github.shawxingkwok:android-util-core:1.0.0")
    implementation ("io.github.shawxingkwok:kdatastore:1.0.0")
}
```
{{< /tab >}}
{{< /tabs >}}

## 调用方 
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
    implementation 'io.github.shawxingkwok:kdatastore:1.0.0'
    implementation project(':settings')
}
```
{{< /tab >}}

{{< tab "view-java / compose" >}}
```groovy
dependencies{
    ...
    implementation 'io.github.shawxingkwok:kdatastore:1.0.0'
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
    implementation ("io.github.implementation ("io.github.shawxingkwok:kdatastore:1.0.0")shawxingkwok:android-util-view:1.0.0")
    
    implementation (project(":settings")) 
}
```
{{< /tab >}}

{{< tab "view-java / compose" >}}
```kotlin
dependencies{
    ...
    implementation ("io.github.shawxingkwok:kdatastore:1.0.0")
    implementation (project(":settings")) 
}
```
{{< /tab >}}
{{< /tabs >}}
{{< /tab >}}
{{< /tabs >}}

# 类型支持

`Kt Serializable` 为 Kotlin 官方出的序列化工具，用法类似 `Java Serializable`, 但多平台，且速度快两倍多。 
被 `Serializable` 标记的 `class`, 
基本类型，`enum`, `Pair`, `IntArray`, `List` 的默认实现等等均可视为 `Kt Serializable`,
详见 {{< newTab "kotlinx.serialization" "https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/basic-serialization.md" >}}。 

[//]: # (It's an official platform-neutral data conversion.)
{{< codeImg "../types.png" >}}
- `Not nullable` 时需声明默认值。
- `Nullable` 时默认值被限制为 `null`。
- 自定义时需实现与 `Kt Serializable` 之间的相互转换。

# 迁移
类比下图格式（判断存在 -> 迁移 -> 删除）从其他存储仓库迁移过来。

比如取自 `SharedPreferences`
{{< codeImg "../migration.png" >}}
<br>

此外内置 `delete`, `exist` 两个函数辅助从 `KDataStore` 迁移到别处。
{{< tabs exist >}}

{{< tab Kt >}}
{{< codeImg "../delete_exist_kt.png" >}}
{{< /tab >}}
{{< tab Java >}}
{{< codeImg "../delete_exist_java.png" >}}
{{< /tab >}}
{{< /tabs >}}
警告以防止误用，并无任何异常风险。

# 可选参数
{{< codeImg "../args.png" >}}

加密部分需引入其他加密库，自制 cipher。 
{{< codeImg "../cipher.png" >}}

{{< hint info >}}
Android 在 api 29 版本开始引入了沙盒机制，实现了数据隔离，相对已经很安全了。
{{< /hint >}}

# 重置
## 全部
{{< tabs wholeReset >}}
{{< tab Kt >}}
{{< codeImg "../wholeResetKt.png" >}}
{{< /tab >}}
{{< tab Java >}}
{{< codeImg "../wholeResetJava.png" >}}
{{< /tab >}}
{{< /tabs >}}

## 局部
比如重置声明过的 `isDarkMode`
{{< tabs partialReset >}}
{{< tab Kt >}}
```
Settings.isDarkMode.reset()
```
{{< /tab >}}
{{< tab Java >}}
```
Settings.isDarkMode().reset();
```
{{< /tab >}}
{{< /tabs >}} 

# 快速启动
如果你介意这点启动时间(**5～30 ms**), 可先行在 `Application` 中异步调用 `Settings`。 

# <a href="https://github.com/ShawxingKwok/KDataStore" target="_blank">GitHub 仓库</a>  
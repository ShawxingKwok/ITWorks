---
title: English
weight: 1
---

# Abstract
`KDataStore` is my personal **local persistent Android storage solution** that developed based on Jetpack's {{< newTab DataStore "https://developer.android.com/topic/libraries/architecture/datastore?hl=zh-cn" >}}.
The emphasis on optimizations is as follows:
- Singleton mode.
- Generating `key` through delegation.
- Adapting `MutableStateFlow` for real-time observation, synchronous reading and writing with memory, and asynchronous writing to disk.
- Backing up data to handle exceptions.

See the <a href="https://github.com/ShawxingKwok/KDataStore" target="_blank">GitHub repository</a> (includes demo) for detailed information.

{{< hint warning >}}
`Java` support is for the initial promotion and not considered in my other works. 
{{< /hint >}}

{{< hint warning >}}
This page will be moved to `Multiplatform` after `IOS` support.
{{< /hint >}}

# Comparison

<table>
<tr>
    <th></th>
    <th><a href="https://developer.android.com/training/data-storage/shared-preferences" target="_blank">SharedPreferences</a></th>
    <th><a href="https://github.com/Tencent/MMKV#readme" target="_blank">MMKV</a></th>
    <th><a href="https://developer.android.com/topic/libraries/architecture/datastore" target="_blank">DataStore</a></th>
    <th>KDataStore</th>
</tr>

<tr>
    <td>Performance</td>
    <td>
        Startup: 2.5ms
        <br><br> Reading: negligible time consumption. 
        <br><br> <span style="color:red">Writing with <code>commit</code>: blocking for 2.3ms</span>
        <br><br> Writing time consumption with <code>apply</code>is negligible, <span style="color:red">but it is unsure if the data has been successfully written to disk asynchronously.</span> </td>
    <td>
        Startup: 2.3ms
        <br><br>Reading and writing: negligible time consumption
    </td>
    <td>All are performed asynchronously, so only <span style="color:red">response time consumption is measured: 8.6ms</span></td>
    <td><span style="color: red;">
        Startup: 13.5ms</span> <br> This impact is not significant when the file size increases substantially, and it can be also resolved by call the <code>KDataStore subclass </code>asynchronously in the <code>Application</code>.
        <br><br>Reading and writing time consumption is negligible. 
    </td>
</tr>

<tr>
    <td>Type safety</td>
    <td>No</td>
    <td>No</td>
    <td><span style="color: green; ">Yes</span></td>
    <td><span style="color: green; ">Yes</span></td>
</tr>

<tr>
    <td>Support for types other than common basic types, <code>String</code>, and <code>Set&lt;String&gt;</code></td>
    <td></td>
    <td> <span style="color: green; ">Parcelable</span></td>
    <td><span style="color: green; ">Customizable</span><br>However, it needs to be placed in an independent <code>DataStore</code></td>
    <td> <span style="color: green; ">Kt<code>Serializable</code> (including common storage types）<br><br>Customizable</span></td>
</tr>

<tr>
    <td>Exception during reading</td>
    <td colspan="2"> <span style="color:red">Returns a empty <code>HashMap</code>, which means returning defaults for all</span></td>
    <td rowspan="2">Manual catch</td>
    <td><span style="color: green; ">Gets from backup file</span></td>
</tr>

<tr>
    <td>IOException during writing</td>
    <td>Replaces with the backup file without the data, and <span style="color: red; ">do not write again.</span>
    <br><br> Returns <code>false</code> if via <code>commit</code></td>
    <td><span style="color:red">Only <code>catch</code> without handling.</span></td>
    <td><span style="color: green; ">Records at once. Update from backup file at next startup</span></td>
</tr>

<tr>
    <td>Multiprocess</td>
    <td>Manual encapsulation</td>
    <td> <span style="color: green; ">Supported</span> </td>
    <td rowspan="2">In <code>1.1.0-alpha</code>stage</td>
    <td rowspan="2">After <code>DataStore 1.1.0</code> releases</td>
</tr>

<tr>
    <td>Multiplatform</td>
    <td><span style="color: red; ">Not supported</span></td>
    <td> <span style="color: red; ">Not supported</span> </td>
</tr>

<tr>
    <td>Crypto</td>
    <td>Manual encapsulation</td>
    <td><span style="color: green; ">Supported</span></td>
    <td>Manual encapsulation</td>
    <td> <span style="color: green; ">You need to choose a crypto protocol and customize a <code>cipher</code>.</span> </td>
</tr>

<tr>
    <td>Additional advantages</td>
    <td></td>
    <td> 
        <span style="color: green; ">Data updated before <code>ANR</code> will not be lost</span> 
    </td>
    <td></td>
    <td>
        <span style="color: green; "> Small size, jar on Android side is only 12.6 kb </span>
    </td>
</tr>

<tr>
    <td>Additional disadvantages</td>
    <td></td>
    <td><span style="color: red; ">After a power outage or system crash, a lot of data is likely to be lost</span></td>
    <td></td>
    <td><span style="color: red; "> Quite new </span> 
        <br><br> <span style="color: red; ">Modeling requires Kotlin.</span> Java is only supported on caller side.  
    </td>
</tr>
</table>

The test results above are based on 30 sets of String data of Meizu 18s. Source code is in {{< repo KDataStore KDataStore >}}.benchmark.

{{< hint warning >}}
There are serious errors in most storage scheme comparison analysises from other information sources. 
The official website is relatively accurate, but very one-sided. 
If you want to explore further, I recommend you test it yourself and view the source code for deep exploration.
{{< /hint >}}

# Basic Usage
Taking the example of `dark theme`.

## UI Display
<video height="200" controls>
  <source src="../effect.mov" type="video/mp4">
</video>

## Model
Set an independent `Android` module, commonly named `settings`.
{{< hint info >}}
All namings in the following documents refer to `settings`.
{{< /hint >}}
{{< codeImg "../model.png" >}}
<br>

About `KDSFlow`
{{< codeImg "../kdsFlowExpect.png" >}}

`actual KDSFlow` on Android
{{< codeImg "../kdsFlowActual.png" >}}

## Call

Call in other modules
{{< tabs "Preview">}}

{{< tab "view-kt" >}}
1. Observe `Flow`/`LiveData` in `Activity`/`BasicActivity` and bind the `theme`.
2. Checked `RadioButton` will change with the user's click. Just set the initial state according to 
`isDarkMode.value` rather than bind.
3. Update the stored value in the `RadioGroup` listener.  
{{< codeImg "../kt_basic_usage.png" >}}

{{< hint info >}}
{{< newTab collectOnResume "/ITWorks/docs/android/util-view/#flowcollectonresume" >}} is recommended to observe `Flow` in `Fragment`, 
of which the `setup` is included in the following.
{{< /hint >}}
{{< /tab >}}

{{< tab "view-java" >}}
1. Observe `LiveData` in `Activity`/`BasicActivity` and bind the `theme`.
2. Checked `RadioButton` will change with the user's click. Just set the initial state according to
   `isDarkMode.value` rather than bind.
3. Update the stored value in the `RadioGroup` listener.  
   {{< codeImg "../java_basic_usage.png" >}}

{{< /tab >}}

{{< tab "compose" >}}
Bind `state` and `theme`  
{{< codeImg "../composeTheme.png" >}}

Update stored value at `RadioButton`
{{< codeImg "../composeRadioButton.png" >}}
{{< /tab >}}

{{< /tabs >}}

# Configuration

Configure `build.gradle`/`build.gradle.kts` as below, or reference the demo in {{< repo KDataStore Github >}}
which uses {{< newTab "version catalog" "https://developer.android.com/build/migrate-to-catalogs" >}}.

## Root Directory
{{< tabs "root plugins" >}}
{{< tab "Groovy" >}}
```
plugins{
    ...
    id 'org.jetbrains.kotlin.plugin.serialization' version "$version_kt" apply false
}
```
{{< /tab >}}

{{< tab "Kotlin" >}}
```
plugins{
    ...
    id ("org.jetbrains.kotlin.plugin.serialization") version "$version_kt" apply false
}
```
{{< /tab >}}
{{< /tabs >}}

## Model module
{{< tabs "settings plugins" >}}
{{< tab "Groovy" >}}
```
plugins {
    ...
    id 'kotlinx-serialization'
}

dependencies {
    ...
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:1.7.1' 
    implementation 'org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.1'
    implementation 'io.github.shawxingkwok:kt-util:1.0.0'
    implementation 'io.github.shawxingkwok:kdatastore:1.0.0'
}
```
{{< /tab >}}
{{< tab "Kotlin" >}}
```
plugins {
    ...
    id ("kotlinx-serialization")
}

dependencies {
    ...
    implementation ("org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.1")
    implementation ("io.github.shawxingkwok:kt-util:1.0.0")
    implementation ("io.github.shawxingkwok:kdatastore:1.0.0")
}
```
{{< /tab >}}
{{< /tabs >}}

## Caller Side
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
    implementation project(':modelModuleName') // or remote library
}
```
{{< /tab >}}

{{< tab "view-java / compose" >}}
```groovy
dependencies{
    ...
   implementation 'io.github.shawxingkwok:kdatastore:1.0.0'
   implementation project(':modelModuleName') // or remote library
}
```
{{< /tab >}}
{{< /tabs >}}
{{< /tab >}}

{{< tab "Kotlin" >}}
{{< tabs "Kotlin">}}
{{< tab "view-kt" >}}
```kotlin
tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach{
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    ...
    implementation ("io.github.shawxingkwok:kdatastore:1.0.0")
    implementation ("io.github.shawxingkwok:android-util-view:1.0.0")
    implementation (project(":modelModuleName")) // or remote library
}
```
{{< /tab >}}

{{< tab "view-java / compose" >}}
```kotlin
dependencies{
    ...
    implementation ("io.github.shawxingkwok:kdatastore:1.0.0")
    implementation (project(":modelModuleName")) // or remote library
}
```
{{< /tab >}}
{{< /tabs >}}
{{< /tab >}}
{{< /tabs >}}

{{< hint warning >}}
Include `KDataStoreInitializer::class.java` in `dependencies`, in case this caller module introduced 
{{< newTab startup-runtime "https://developer.android.com/topic/libraries/app-startup?hl=zh-cn" >}}.
{{< /hint >}}

# Type Support

{{< newTab "kotlinx.serialization" "https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/basic-serialization.md" >}}
usage is similar to `Java Serializable`, but multiplatform and more than twice as fast.
Classes annotated with `Serializable`, basic types, `enum`, `Pair`, `IntArray`, `List`'s default implementation and some others 
can be considered as `Serializable`.

{{< codeImg "../types.png" >}}
- When `non-null`, a default value must be declared.
- When `nullable`, the default value is restricted to `null`.

{{< hint warning >}}
Edit in a stored object would not trigger the disk update.
{{< codeImg "../note_update.png" >}}
{{< /hint >}}

# Migration
Migrate from other storage repositories with this format (judge existence -> migrate -> delete) in which `appContext` 
is from `KDataStore`.

Take the example of migrating from `SharedPreferences`.
{{< codeImg "../migration.png" >}}
<br>

Additionally, there are two built-in functions `delete` and `exist` that assist in migrating from `KDataStore` to other places.
{{< tabs exist >}}

{{< tab Kt >}}
{{< codeImg "../delete_exist_kt.png" >}}
{{< /tab >}}
{{< tab Java >}}
{{< codeImg "../delete_exist_java.png" >}}
{{< /tab >}}
{{< /tabs >}}
Warning is for preventing misuse rather than exception risks.

# Optional arguments
{{< codeImg "../args.png" >}}
<br>
{{< codeImg "../cipher.png" >}}

For the `cipher` part, you need to choose a crypto protocol from `Java` standard library, 
or other libraries to customize it.

{{< hint info >}}
Data isolation was introduced in Android API 29, making it relatively safe without crypto which
approximately doubles the startup time.
{{< /hint >}}

# Reset
## All
{{< tabs wholeReset >}}
{{< tab Kt >}}
{{< codeImg "../wholeResetKt.png" >}}
{{< /tab >}}
{{< tab Java >}}
{{< codeImg "../wholeResetJava.png" >}}
{{< /tab >}}
{{< /tabs >}}

## Partial
Take the example of `isDarkMode`.
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
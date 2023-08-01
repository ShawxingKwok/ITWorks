---
title: English
weight: 1
---

{{< hint warning >}}
`Java` support is for the initial promotion and not considered in my other works. 
{{< /hint >}}

{{< hint warning >}}
This page will be moved to `Multiplatform` group after `IOS` support.
{{< /hint >}}

# Local Android Storage Solutions Comparison

<table>
<tr>
    <th></th>
    <th><a href="https://developer.android.com/training/data-storage/shared-preferences" target="_blank">SharedPreferences</a></th>
    <th><a href="https://github.com/Tencent/MMKV#readme" target="_blank">MMKV</a></th>
    <th><a href="https://developer.android.com/topic/libraries/architecture/datastore" target="_blank">DataStore</a></th>
    <th>KDataStore</th>
</tr>

<tr>
    <td>Features</td>
    <td>
        <span style="color:red"> <code>commit</code> blocks the current thread</span>
        <br><br> 
        <code>apply</code> does not block, <span style="color:red">but it is uncertain whether data is
         successfully written or not.</span>
    </td>
    <td> 
         Read data into system-level memory at first.
        <br><br><span style="color: green; ">Synchronously non-blocking read and write</span>
        <br><br>Background timed asynchronous write to disk 
    </td>
    <td> Asynchronous read and write, update memory after successful write. Observe asynchronously via <code>Flow</code>.</td>
    <td> 
        Based on <code>DataStore</code> 
        <br><br> First read into application-level memory 
        <br><br> <span style="color: green; ">Synchronously non-blocking read and write, simple modeling, easy calling 
        <br><br>Update memory first, then write to disk asynchronously background, including backup files.</span>
    </td>
</tr>

<tr>
    <td>Time-consuming part</td>
    <td>Startup: 2.1ms <br> <code>commit</code>: 2.4ms </td>
    <td>Startup: 2.6ms</td>
    <td>Response: 8.0ms</td>
    <td><span style="color: red; ">Startup: 18.1ms</span> <br> little affected when file size increases significantly</td>
</tr>

<tr>
    <td>IOException during read</td>
    <td>Return default value</td>
    <td>Return default value</td>
    <td rowspan="3">Manual catch</td>
    <td><span style="color: green; ">Get from backup file</span></td>
</tr>

<tr>
    <td>IOException during write</td>
    <td>Replace with the backup file without the data, and <span style="color: red; ">do not write again.</span>
    <br><br> Return <code>false</code> if via <code>commit</code></td>
    <td rowspan="2">Check data later, try to restore, delete if unsuccessful.</td>
    <td><span style="color: green; ">Record at once. Update from backup file at next startup</span></td>
</tr>

<tr>
    <td> Corruption Exception </td>
    <td> <span style="color: red; ">Replace with an empty file</span> </td>
    <td> <span style="color: green; ">Copy backup file</span> </td>
</tr>

<tr>
    <td>Multiprocess</td>
    <td>Manual encapsulation</td>
    <td> <span style="color: green; ">Supported</span> </td>
    <td rowspan="2">In the alpha stage</td>
    <td rowspan="2">After DataStore officially supports</td>
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
    <td> <span style="color: green; ">Requires introducing other crypto libraries</span> </td>
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
    <td>Additional advantages</td>
    <td></td>
    <td> 
        <span style="color: green; ">Data updated before <code>ANR</code> will not be lost</span> 
    </td>
    <td></td>
    <td>
        <span style="color: green; "> Small size, jar on Android side is only 8 kb </span>
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

The above test results use 30 sets of String data on Meizu 18s.
Source code is in {{< repo KDataStore KDataStore >}}.benchmark.

{{< hint warning >}}
Regarding the storage scheme comparison analysis in other places, the vast majority of them have serious errors.
The official website is relatively accurate, but very one-sided. If you want to explore, I recommend to test and view the source code.
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
{{< newTab collectOnResume "/ITWorks/docs/android/util-view/#flowcollectonresume" >}} is recommended to observe `Flow` in `Fragment`.
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
Suppose it's named `settings`.
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
    implementation 'io.github.shawxingkwok:android-util-core:1.0.0'
    api 'io.github.shawxingkwok:kdatastore:1.0.0'
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
    implementation ("io.github.shawxingkwok:android-util-core:1.0.0")
    api ("io.github.shawxingkwok:kdatastore:1.0.0")
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
    implementation project(':settings')
}
```
{{< /tab >}}

{{< tab "view-java / compose" >}}
```groovy
dependencies{
    ...
    implementation project(':settings') 
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
    implementation ("io.github.shawxingkwok:android-util-view:1.0.0")
    implementation (project(":settings")) 
}
```
{{< /tab >}}

{{< tab "view-java / compose" >}}
```kotlin
dependencies{
    ...
    implementation (project(":settings")) 
}
```
{{< /tab >}}
{{< /tabs >}}
{{< /tab >}}
{{< /tabs >}}

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
Migrate from other storage repositories with this format (judge existence -> migrate -> delete). 
`AppContext` is from introduced library `android-util-core`.

Take the example of `SharedPreferences`.
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

`cipher` implementation needs other crypto libraries. 

{{< hint info >}}
Data isolation was introduced in Android API 29, making it relatively safe without crypto which
increases the startup time by about half.
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

# Quick Start
Call `Settings` in `Application` asynchronously if you mind the startup time (**5～30 ms**).
# <a href="https://github.com/ShawxingKwok/KDataStore" target="_blank">GitHub repo with demo</a>
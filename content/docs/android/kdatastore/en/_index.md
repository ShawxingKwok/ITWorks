---
title: English
weight: 1
---

{{< hint warning >}}
This Chinese version and the `Java` extension are mainly used for initial promotion and will not have any other works.
{{< /hint >}}

{{< hint warning >}}
After supporting `IOS`, it will be moved to the `Multiplatform mobile` group.
{{< /hint >}}

# Comparison of Local Storage Solutions

<table>
<tr>
    <th></th>
    <th>SharedPreferences</th>
    <th>MMKV</th>
    <th>DataStore</th>
    <th>KDataStore</th>
</tr>

<tr>
    <td>Mechanism</td>
    <td>
        Synchronous read and write
        <br><br>
        <span style="color:red"> <code>commit</code> blocks the current thread</span>
        <br> <code>apply</code> does not block, <span style="color:red">but it is uncertain whether the write is successful.</span>
        <br><br><span style="color:red">Regardless of whether the write is successful or not, the memory is updated and the listener is notified.</span>
    </td>
    <td> 
        Read from system-level memory first
        <br><br><span style="color: green; ">Synchronous and non-blocking read and write</span>
        <br><br>Asynchronously write to disk in the background 
    </td>
    <td> Asynchronous read and write, update memory after successful write.<br><br>Asynchronously observe through Flow.</td>
    <td> 
        Based on DataStore 
        <br><br> Read from application-level memory first 
        <br><br> <span style="color: green; ">Synchronous and non-blocking read and write</span>
        <br><br>Asynchronously write to disk, including backup files, when data is updated. 
    </td>
</tr>

<tr>
    <td>Performance (ms)</td>
    <td>Startup: 2.1 <br> commit: 2.4 </td>
    <td>Startup: 2.6</td>
    <td>Response: 8</td>
    <td><span style="color: red; ">Startup: 18</span> <br> Little impact when file size significantly increases</td>
</tr>

<tr>
    <td>IOException during reading</td>
    <td>Returns default value</td>
    <td>Returns default value</td>
    <td rowspan="3">Handled by catching</td>
    <td><span style="color: green; ">Retrieve from backup file</span></td>
</tr>

<tr>
    <td>IOException during writing</td>
    <td>Replaces with the backup file that has not been written, and no longer writes.<br><br> If using commit, can be determined by the returned false</td>
    <td rowspan="2">Subsequently verifies the data, attempts to recover, and deletes if unsuccessful.</td>
    <td><span style="color: green; ">Records and updates from the backup file on the next startup</span></td>
</tr>

<tr>
    <td>Corruption Exception</td>
    <td> <span style="color: red; ">Replaces with an empty file</span> </td>
    <td> <span style="color: green; ">Processes by copying data from the backup file</span> </td>
</tr>

<tr>
    <td>Multi-process</td>
    <td>Self-encapsulated</td>
    <td> <span style="color: green; ">Supported</span> </td>
    <td rowspan="2">In alpha stage</td>
    <td rowspan="2">After DataStore is officially supported</td>
</tr>

<tr>
    <td>Multi-platform</td>
    <td><span style="color: red; ">Not supported</span></td>
    <td> <span style="color: red; ">Not supported</span> </td>
</tr>

<tr>
    <td>Encryption</td>
    <td>Self-encapsulated</td>
    <td><span style="color: green; ">Supported</span></td>
    <td>Self-encapsulated</td>
    <td> <span style="color: green; ">Requires importing other encryption libraries</span> </td>
</tr>

<tr>
    <td>Type safety</td>
    <td>No</td>
    <td>No</td>
    <td><span style="color: green; ">Yes</span></td>
    <td><span style="color: green; ">Yes</span></td>
</tr>

<tr>
    <td>Support for types other than common basic types, String, Set&lt;String&gt;</td>
    <td></td>
    <td> <span style="color: green; ">Parcelable</span></td>
    <td><span style="color: green; ">Custom</span><br>But needs to be placed in an independent DataStore</td>
    <td> <span style="color: green; ">Kt Serializable (including common storage types)<br><br>Custom</span></td>
</tr>

<tr>
    <td>Additional advantages</td>
    <td></td>
    <td> 
        <span style="color: green; ">Data updated until ANR is not lost</span> 
    </td>
    <td></td>
    <td>
        <span style="color: green; ">Simple modeling, convenient to call <br><br> Small size, jar on Android side is only 8 kb </span>
    </td>
</tr>

<tr>
    <td>Additional disadvantages</td>
    <td></td>
    <td><span style="color: red; ">Easy to lose a lot of data after power failure or system crash</span></td>
    <td></td>
    <td><span style="color: red; ">Relatively new</span> 
        <br><br> <span style="color: red; ">Modeling can only be done using Kotlin, </span> Java-only developers need to have some knowledge of Kotlin.
    </td>
</tr>
</table>

The above test results are based on 30 sets of String data with a length of 7 (key length) * 20 (value length), using the Meizu 18s model. The code is in the [source code](https://github.com/ShawxingKwok/KDataStore/archive/refs/heads/master.zip) in the api's androidTest.

{{< hint warning >}}
Regarding the comparison and analysis of storage solutions in other places, most of them have serious errors.
The official website is relatively accurate, but also very one-sided. If you want to explore, it is recommended to test and consult the source code yourself.
{{< /hint >}}

# Basic Usage
Using Dark theme as an example

## UI Display
<video height="200" controls>
  <source src="../effect.mov" type="video/mp4">
</video>

## Modeling
Separate a `Android` module, commonly named `settings`. (Subsequent documents will follow the `settings` naming convention)
{{< codeImg "../model.png" >}}
<br>

About `KDSFlow`
{{< codeImg "../kdsFlowExpect.png" >}}
{{< codeImg "../kdsFlowActual.png" >}}

{{< hint warning >}}
The `liveData` mentioned here is exclusive to Android and is not supported on other platforms.
{{< /hint >}}

## Invocation

Calling from other modules
{{< tabs "Preview">}}

{{< tab "view-kt" >}}
1. Observe `Flow`/`LiveData` in `Activity`/`BasicActivity` and bind the theme.
2. The `checked RadioButton` will automatically change according to the user's click, and the initial state can be set based on `isDarkMode.value`, no need to bind.
3. Update the stored value in the `RadioGroup` listener.  
   {{< codeImg "../kt_basic_usage.png" >}}
   {{< hint info >}}
   When observing `Flow` in a `Fragment`, it is recommended to use
   {{< newTab collectOnResume "https://shawxingkwok.github.io/ITWorks/docs/android/util-view/#flowcollectonresume" >}}.
   {{< /hint >}}
   {{< /tab >}}

{{< tab "view-java" >}}
1. Observe `LiveData` in `Activity`/`BasicActivity` and bind the theme.
2. The `checked RadioButton` will automatically change according to the user's click, and the initial state can be set based on `isDarkMode().getValue()`, no need to bind.
3. Update the stored value in the `RadioGroup` listener.  
   {{< codeImg "../java_basic_usage.png" >}}

{{< /tab >}}

{{< tab "compose" >}}
Bind `State` with the theme  
{{< codeImg "../composeTheme.png" >}}

Update the stored value at the `RadioButton`  
{{< codeImg "../composeRadioButton.png" >}}
{{< /tab >}}

{{< /tabs >}}

# Configuration

Configure the corresponding `build.gradle`, or refer to the demo in the [source code](#release).

## Root directory
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

## Model module
Separate a module, commonly named `settings`, (if not adopted, the naming of subsequent `settings` will be changed accordingly)
Choose **kotlin** as the language instead of java.

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

## Caller side
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

# Type Support

`Kt Serializable` is a serialization tool officially released by Kotlin. Its usage is similar to `Java Serializable`, but it is multi-platform and more than twice as fast.
Classes marked with `Serializable`,
basic types, `enum`, `Pair`, `IntArray`, default implementations of `List`, etc. can be considered as `Kt Serializable`,
see {{< newTab "kotlinx.serialization" "https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/basic-serialization.md" >}} for details.

{{< codeImg "../types.png" >}}
- When `Not nullable`, default value needs to be declared.
- When `Nullable`, default value is limited to `null`.
- For custom types, mutual conversion between `Kt Serializable` is required.

# Migration
Migrate from other storage repositories in the format shown in the figure (check existence -> migrate -> delete).

For example, taken from `SharedPreferences`
{{< codeImg "../migration.png" >}}
<br>

In addition, the built-in `delete` and `exist` functions assist in migrating from `KDataStore` to elsewhere.
{{< tabs exist >}}

{{< tab Kt >}}
{{< codeImg "../delete_exist_kt.png" >}}
{{< /tab >}}
{{< tab Java >}}
{{< codeImg "../delete_exist_java.png" >}}
{{< /tab >}}
{{< /tabs >}}
The warning is to prevent misuse, and there is no risk of any exceptions.

# Optional Parameters
{{< codeImg "../args.png" >}}

For encryption, other encryption libraries need to be imported and a custom cipher needs to be created.
{{< codeImg "../cipher.png" >}}

{{< hint info >}}
Starting from API 29, Android introduced a sandbox mechanism, which achieved data isolation and is relatively secure.
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
For example, reset the declared `isDarkMode`
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

# Quick initialization
Call `Settings` in `Application` first asynchronously if you are concerned about its startup 
time (**5-30 ms**).

# <a href="https://github.com/ShawxingKwok/KDataStore" target="_blank">GitHub Repo</a>
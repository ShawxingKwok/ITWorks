---
title: KDataStore 
weight: 4
draft: false
---

[中文版本](../../中文版本)

An extended data store with little configuration, easy encryption and extensive supported types 
(basic types, kt-serializable, java-serializable and customed).

## Basic feature preview
{{< tabs "Preview">}}
{{< tab "view" >}}
{{< video src="view preview.mp4" >}}
{{< /tab >}}
{{< tab "compose" >}}
{{< video src="compose preview.mp4" >}}
{{< /tab >}}
{{< /tabs >}}

## Actual performance contrast
TODO

## Setup
Get a [sample](demo.zip) or configure as below.

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
### Configure 
<image src="config.png" width=550></image>

### Update  
```kotlin
// async
Settings.isDarkMode.toss(true)
Settings.isDarkMode.toss{ !it }

// suspend 
Settings.isDarkMode.emit(true)
Settings.isDarkMode.emit{ !it }
```
That type `Flow` is declared inside `class KDataStore` and extends from the official `Flow`, being both
collectable and mutable.

<image src="flow.png"></image>

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

<image src="_compose collection.png"></image>

---

use
<image src="compose collection.png"></image>
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

## Migration

## Encryption
default encrypted

{{< hint info >}}
This encryption part references https://github.com/MFlisar/MaterialPreferences.
{{< /hint >}}

## Options
### scope
TODO(handlerScope, ioScope)

### file name

## Details
### Reset

### updateAll
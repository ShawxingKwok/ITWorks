---
title: English 
weight: 1
---

## Basic feature preview
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

## Setup
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

## Migration
There is a `protected context` inside `KDataStore` for you to get other stores like 
`SharedPreferences`, `DataStore`.

## Encryption

{{< hint info >}}
This default AES cipher references <a href="https://github.com/MFlisar/MaterialPreferences" target="_blank">some one else's design.</a>
{{< /hint >}}

## Options
### scope
TODO(handlerScope, ioScope)

### file name

## Details
### Reset

### updateAll

## <a href="https://github.com/ShawxingKwok/KDataStore" target="_blank">GitHub repo</a>
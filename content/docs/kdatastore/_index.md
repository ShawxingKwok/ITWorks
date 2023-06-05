---
title: KDataStore 
weight: 4
draft: false
---

An extended data store with little configuration, easy encryption and extensive supported types.

## Basic feature preview
{{< tabs "Preview">}}
{{< tab "view" >}}
{{< video src="preview" >}}
{{< /tab >}}
{{< tab "compose" >}}
TODO
{{< /tab >}}
{{< /tabs >}}

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
### Configure with extensive supported types
<image src="functions.png" width=550></image>

### Collect or update `KDataStore.Flow` 
That type `Flow` is declared inside `class KDataStore` and extends from the official `Flow`, being both
collectable and mutable.

<image src="flow.png" width = 600></image>

---

<image src="bool.png" ></image>

---

```kotlin
// update asyncly somewhere else
Settings.bool.toss(true)
Settings.bool.toss{ !it }

// or suspend in a suspended environment
Settings.bool.emit(true)
Settings.bool.emit{ !it }
```

## Kotlin serialization
### Usage
It's a platform-neutral data conversion, and directly savable in my `KDataStore`.

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
    id 'org.jetbrains.kotlin.plugin.serialization' version "$kt_version"
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
    id ("org.jetbrains.kotlin.plugin.serialization") version ("$kt_version")
}
```

```groovy
dependencies {
    implementation ("org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.0")
}
```
{{< /tab >}}
{{< /tabs >}}

## Extended collection

## Migration

## Side action 

## Encryption
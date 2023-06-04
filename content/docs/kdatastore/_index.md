---
title: KDataStore 
weight: 4
draft: false
---

An extended data store with little configuration, easy encryption and extensive supported types.

## Basic feature preview
{{< video src="preview" >}}

## Setup
Get a [sample](demo.zip) or configure as below.

{{< tabs >}}
{{< tab "Groovy" >}}
```groovy
// Project build.gradle
plugins {
    id 'org.jetbrains.kotlin.plugin.serialization' version "$kt_version" apply false
}

// Module build.gradle
plugins{
    id 'kotlinx-serialization'
}

//region KDataStore
android.lintOptions.disable 'NonConstantResourceId'

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += '-Xcontext-receivers'
}

dependencies {
    implementation 'androidx.core:core-ktx:1.10.1' 
    implementation 'org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.0' 
    implementation 'io.github.shawxingkwok:kt-util:1.0.0'
    implementation 'io.github.shawxingkwok:android-util-view:1.0.0'
    implementation 'io.github.shawxingkwok:android-kdatastore:1.0.0'
}
//endregion
```
{{< /tab >}}
{{< tab "Kotlin" >}}
```kotlin
// Project build.gradle
plugins {
    id ("org.jetbrains.kotlin.plugin.serialization") version ("$kt_version") apply (false)
}

// Module build.gradle
plugins{
    id ("kotlinx-serialization")
}

//region KDataStore
android.lintOptions.disable ("NonConstantResourceId")

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation ("androidx.core:core-ktx:1.10.1")
    implementation ("org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.0")
    implementation ("io.github.shawxingkwok:kt-util:1.0.0")
    implementation ("io.github.shawxingkwok:android-util-view:1.0.0")
    implementation ("io.github.shawxingkwok:android-kdatastore:1.0.0")
}
//endregion
```
{{< /tab >}}
{{< /tabs >}}

## Core

### Extensive supported types 

<image src="functions.png"></image>

### `KDataStore.Flow` 
That type `Flow` is declared inside `class KDataStore` and extends from official `Flow`. 

<image src="flow.png" width=500></image>

### Kotlin serialization
TODO

## Migration

## Side action 

## Encryption

## Compose extension



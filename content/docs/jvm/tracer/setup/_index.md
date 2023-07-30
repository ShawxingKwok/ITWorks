---
title: Setup
weight: 2
---

Configure with `gradle` as below.

1. repository:  `mavenCentral()`

2. plugin
    ```kotlin
    plugins{
        // Minimum version: kt `1.8.0`, ksp `1.8.0-1.0.9`.  
        // keep the prefix same with your kotlin version.
        // here supposes your kotlin plugin is at '1.8.0'
        id("com.google.devtools.ksp") version "1.8.0-1.0.9"
    }
    ```

3. add this part directly, rather than insert messily.
{{< tabs "Tracer" >}}
{{< tab "Groovy" >}}
```groovy
//region tracer
// options
// ksp.arg("tracer.internal", "")

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach {
    kotlinOptions.freeCompilerArgs += '-Xcontext-receivers'
}

dependencies {
    implementation 'io.github.shawxingkwok:tracer-annotations:1.0.0'
    ksp 'io.github.shawxingkwok:tracer-compiler:1.0.0'
}
//endregion 
```
{{< /tab >}}
{{< tab "Kotlin" >}}
```kotlin
//region tracer
// options
// ksp.arg("tracer.internal", "")

tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach {
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation ("io.github.shawxingkwok:tracer-annotations:1.0.0")
    ksp ("io.github.shawxingkwok:tracer-compiler:1.0.0")
}
//endregion 
```
{{< /tab >}}
{{< /tabs >}}
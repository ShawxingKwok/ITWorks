---
title: Setup
weight: 2
---
{{< hint warning >}}
Requires the kotlin plugin of version `1.8.0` or later.  
{{< /hint >}}

See module **sample** on {{< repo Tracer Github >}} or configure your `build.gradle`/`build.gradle.kts` as below.

1. repository:  `mavenCentral()`

2. plugin
    ```kotlin
    plugins{
        // keep the prefix same with your kotlin version.
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
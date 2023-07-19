---
title: Setup
weight: 2
---
Download a  
<a href="common.zip" download>Common</a> / <a href="android.zip" download>Android</a> 
sample or configure your `build.gradle`/`build.gradle.kts` as below.

# Ksp plugin
{{< tabs "Ksp plugin" >}}
{{< tab "Groovy" >}} 
```
plugins{
    ...
    // Assuming your kotlin version is `1.7.21`, here uses the latest ksp plugin version beginning 
    // with `1.7.21` ('1.7.21-1.0.8').  
    id 'com.google.devtools.ksp' version '1.7.21-1.0.8'
}
```
{{< /tab >}}
{{< tab "Kotlin" >}} 
```kotlin
plugins{
    // Assuming your kotlin version is `1.7.21`, here uses the latest ksp plugin version beginning 
    // with `1.7.21` ('1.7.21-1.0.8').  
    id("com.google.devtools.ksp") version "1.7.21-1.0.8"
}
```
{{< /tab >}}
{{< /tabs >}}

# Source sets
Skip this step if your ksp plugin version is `1.8.0-1.0.9` or higher.  
This part may be different for other ksp plugins.
(See [ksp quickstart](https://kotlinlang.org/docs/ksp-quickstart.html#make-ide-aware-of-generated-code))
```groovy
// Omissible if your ksp plugin version is '1.8.0-1.0.9' or higher. 
kotlin.sourceSets.configureEach {
    kotlin.srcDir("$buildDir/generated/ksp/$name/kotlin/")
}
```

# Tracer
Add this part directly, rather than insert messily.
{{< tabs "Tracer" >}}
{{< tab "Groovy" >}}
```groovy
//region tracer
// options
ksp{
//    arg("tracer.internal", "")
}

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach {
    kotlinOptions.freeCompilerArgs += '-Xcontext-receivers'
}

dependencies {
    // Keep this version latest with the prefix not higher than your kotlin version.
    ksp 'io.github.apollokwok:tracer-common-compiler:1.7.20-1.2.0'
    // Keep this version latest but not higher than the version above.
    implementation 'io.github.apollokwok:tracer-common-annotations:1.7.20-1.2.0'
}
//endregion 
```
{{< /tab >}}
{{< tab "Kotlin" >}}
```kotlin
//region tracer
// options
ksp{
//    arg("tracer.internal", "")
}

tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach {
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    // Keep this version latest with the prefix not higher than your kotlin version.
    ksp("io.github.apollokwok:tracer-common-compiler:1.7.20-1.2.0")
    // Keep this version latest but not higher than the version above.
    implementation("io.github.apollokwok:tracer-common-annotations:1.7.20-1.2.0")
}
//endregion 
```
{{< /tab >}}
{{< /tabs >}}
---
title: Util-view
weight: 2
---

## Typical Effect
{{< video src="effect" >}} 

## Setup
Add this part directly, rather than insert messily.
{{< tabs >}}
{{< tab "Groovy" >}}

```groovy
//region shawxingkwok: android-util-view
android.lintOptions.disable 'NonConstantResourceId'

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation 'io.github.shawxingkwok:kt-util:1.0.0'
    implementation 'io.github.shawxingkwok:android-util-view:1.0.0'
}
//endregion
```
{{< /tab >}}
{{< tab "Kotlin" >}}

```kotlin
//region shawxingkwok: android-util-view
android.lintOptions.disable("NonConstantResourceId")

tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach {
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation("io.github.shawxingkwok:kt-util:1.0.0")
    implementation("io.github.shawxingkwok:android-util-view:1.0.0")
}
//endregion
```
{{< /tab >}}
{{< /tabs >}}

## <a href="html/view/pers.shawxingkwok.androidutil.view/" target="_blank">Document</a>

## <a href="https://github.com/ShawxingKwok/AndroidUtil" target="_blank">GitHub repo</a>
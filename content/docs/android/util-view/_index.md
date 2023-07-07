---
title: Util-view
weight: 2
---

# Setup
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

# Flow.collectOnResume

# KFragment

# KRecyclerViewAdapter

# <a href="https://github.com/ShawxingKwok/AndroidUtil-View" target="_blank"> GitHub repo </a>
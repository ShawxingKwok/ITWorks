---
title: Android
weight: 1
---

# Most could be static
Provide static `application context` first. 
```kotlin
class App : Application() {
    companion object{
        @SuppressLint("StaticFieldLeak")
        lateinit var context: Context
            private set
    }

    override fun onCreate() {
        super.onCreate()
        context = applicationContext
    }
}
```
As `App` is always initialized at first and destroyed at last, `applicationContext` could be 
safely static for common Android developers, which could make most traditionally injected objects 
also static, like`Database`, `DAO` and many related utils. 


<br>
{{< hint info >}}
Skip if you are using `Compose`. Learn the common tracer part first if you are not familiar.
{{< /hint >}}

# Setup
Add the dependency below common tracer.
{{< tabs "Setup" >}}
{{< tab "Groovy" >}} 
```groovy
// Keep this version latest with the prefix not higher than your kotlin version.
// Never put this above 'tracer-common-compiler'
ksp 'io.github.apollokwok:tracer-android-traditional-compiler:1.7.20-1.0.1'
```
{{< /tab >}}
{{< tab "Kotlin" >}} 
```kotlin
// Keep this version latest with the prefix not higher than your kotlin version.
// Never put this above 'tracer-common-compiler'
ksp("io.github.apollokwok:tracer-android-traditional-compiler:1.7.20-1.0.0")
```
{{< /tab >}}
{{< /tabs >}}

<img src=setup.png />

# Effect
Connects with `activity`/`parentFragment`'s interior elements with automatic 
`requireActivity`/`requireParentFragment`.

{{< video src=effect >}}

# ViewModel
I suggest to annotate your subclass of `ViewModel` with `Tracer.Tip` to avoid tracing insides. 

<br>
{{< button href="https://github.com/ApolloKwok/TracerAndroidTraditional" >}}Github repo{{< /button >}}
<br><br>
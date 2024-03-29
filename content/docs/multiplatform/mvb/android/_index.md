---
title: Android
weight: 1
---
# Abstract
`MVB` is my new architecture standing for `Model-View-Bridge`. Now, it only has an Android implementation, 
but the pattern is applicable to every platform that supports Kotlin. The UI part uses the traditional `View` without 
`DataBinding`, and the Kt part hides `ViewModel`, resulting in much less code and clearer logic.

Other points:
- `Compose` is the most concise declarative UI framework, and also the most flexible in terms of custom views. However, 
its recombination mechanism inevitably reconstructs massive objects, resulting in its low upper performance limit.

- Regarding `navigation parameters` for `Activity` / `Fragment`, I have a proposal on 
{{< newTab Reddit "https://www.reddit.com/r/androiddev/comments/15pa5qf/proposal_android_could_support_activityfragment/" >}}.

- `RecyclerView.Adapter` is the most common among components with complex writing styles, whereas my encapsulated 
{{< newTab "KRecyclerViewAdapter" "/ITWorks/docs/android/util-view/#krecyclerviewadapter" >}} arranges list items as `Compose`. 

- For the UI part, I have a {{< newTab "new design pattern proposal" "../proposal" >}} of which the expected effect is 
much better than `Compose` and `Flutter`.   

Overall, I suggest `MVB` if you haven't switched to `Compose` from `View` at present. In the future, there would be a new
imperative UI mode based on that {{< newTab "proposal" "../proposal" >}} implementation in which `MVB` mode would still work.

# Setup
{{< tabs >}}
{{< tab "Groovy" >}}
```groovy
android{
    buildFeatures {
        viewBinding true
    }
}

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation 'io.github.shawxingkwok:android-util-view:1.0.8'
    implementation 'io.github.shawxingkwok:mvb-android:1.0.7'
}
```
{{< /tab >}}
{{< tab "Kotlin" >}}
```kotlin
android{
    buildFeatures {
        viewBinding = true
    }
}

tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach {
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation("io.github.shawxingkwok:android-util-view:1.0.8")
    implementation("io.github.shawxingkwok:mvb-android:1.0.7")
}`
```
{{< /tab >}}
{{< /tabs >}}

# Usage
{{< hint info >}}
Examples are all with `Fragment`, but also applicable on `ComponentActivity` (a super class of `AppCompatAtivity`).  
{{< /hint >}}

## rmb
This is the abbreviation of `remember`, meaning the value is alive across the configuration change (e.g. rotation).
Switch from the traditional way as below.
{{< codeImg rmb.png >}}
<p style="text-align:center;">↓</p>
{{< codeImg rmb_.png >}}
{{< hint info >}}
Initialization of the property declared with `val` is `synchronized`. 
{{< /hint >}}

## save
### Direct
Saved values are not only alive across the configuration change, but also restorable from the killed process. 

{{< tabs "save" >}} 

{{< tab `Common` >}}
{{<codeImg saveParcelable.png >}}
<p style="text-align:center;">↓</p>
{{<codeImg saveParcelable_.png >}}
{{< /tab >}}

{{< tab `Flow` >}}
{{<codeImg saveFlow.png >}}
<p style="text-align:center;">↓</p>
{{<codeImg saveFlow_.png >}}
{{< /tab >}}

{{< tab `LiveData` >}}
{{<codeImg saveLiveData.png >}}
<p style="text-align:center;">↓</p>
{{<codeImg saveLiveData_.png >}}
{{< /tab >}}

{{< /tabs >}}
{{< hint warning >}}
Saved value types should follow {{< newTab Parcelize "https://developer.android.com/kotlin/parcelize" >}} rules. 
Please note that there is a mistake in the official document. `Set` is actually saved as `Serialziable` and can't keep 
`Parcelable`.
{{< /hint >}}
{{< hint info >}}
I suggest to use `Flow` instead of `LiveData`, though it takes some time to learn.
{{< /hint >}}
{{< hint info >}}
Rules below are same to `saveMutableStateFlow`, `saveMutableSharedFlow`, `saveMutableLiveData`, and not limited with 
`val` / `var`.
{{< /hint >}}

### Transform
You could also save values with any type by appending `transform` after `save` with the converted values following 
{{< newTab Parcelize "https://developer.android.com/kotlin/parcelize" >}} rules.
{{< codeImg saveWithTransform.png >}}
{{< hint warning >}}
The lambda `convert` keeps alive after its lifecycle owner is killed by the application. Therefore, 
don't reference callables from its owner to avoid memory leaks.
{{< /hint >}}

### Parcelable component
Sometimes you need to pass `KClass<out Parcelable>` as the parcelable component. Besides, there could be no more than 1
parcelable subtype.
{{< codeImg saveWithParcelableComponent.png >}}

## observe
The delegated values via `rmb` / `save` are easily observable if it's `Flow` / `LiveData`. The latter `observe` lambdas 
are active between every `onStart` and `onStop`, which is generally used for linking `value` to `UI state`.
{{< codeImg observe.png >}}
<p style="text-align:center;">↓</p>
{{< codeImg observe_.png >}}

## mvbScope
This is `viewModelScope` of the hidden `MVBViewModel`. This `coroutineScope` is not affected by the configuration change, 
and cancelled only when its owner `ComponentActivity` / `Fragment` is killed by `user presses back` or `low memory`. 
Besides, it's not static but actually an extensive property. Check out the source code, and you will find there is no 
memory leak.
{{< codeImg mvbScope.png >}}

## Format  
Take an example of simulating the stopwatch page in the IOS clock with concise code below. 

<video height="400" controls>
  <source src="stopwatch.mov" type="video/mp4">
</video>

### Utils and Components for reusable and complex parts 
{{< codeImg stopwatchUtil.png >}}
<br> See {{< newTab "KRecyclerViewAdapter" "/ITWorks/docs/android/util-view/#krecyclerviewadapter" >}} if it's new to you.
{{< codeImg stopwatchAdapter.png >}}

### Static processing
For example, declaring components and enabling them in `ComponentActivity.onCreate` / `Fragment.onViewCreated` 
{{< codeImg staticProcessing.png >}}

### Bridge
Declaring variable data sources via `rmb` and `save`, and linking them to `UI state` via appending `observe`.
{{< codeImg bridge1.png >}}
{{< codeImg bridge2.png >}}

### Fixed listeners
I suggest to put fixed listeners here or below, allowing for variating data source or navigating to other pages. 
{{< codeImg fixedListeners.png >}}

Or this style
{{< codeImg fixedListenersWithSeparateActions.png >}}

{{< hint info >}}
Some functions from other libraries are independent with `MVB`, e.g. 
{{< newTab "binding(XxBinding::bind)" "https://dylancaicoding.github.io/ViewBindingKTX/#/en/" >}}, 
{{< newTab onClick "/ITWorks/docs/android/util-view/#viewonclick" >}}, 
and {{< newTab updateIf "/ITWorks/docs/multiplatform/kt-util/html/-kt-util/pers.shawxingkwok.ktutil/update-if.html" >}}.
{{< /hint >}}

# <a href="https://github.com/ShawxingKwok/MVB" target="_blank"> GitHub repo</a>
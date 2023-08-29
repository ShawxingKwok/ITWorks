---
title: Util-core
weight: 1
---

{{< hint info >}}
Contains only `KLog` at present, and would be expanded with some small practical tools in the future.
{{< /hint >}}

# Setup
```
dependenceis{
    ...
    implementation ("io.github.shawxingkwok:android-util-core:1.0.1")
}
```

# AppContext trick
Design as below in your basic module:
{{< codeImg appContext.png >}}
<br>
Don't forget to declare your application name which may need to begin with the package name. 
{{< codeImg appInManifest.png >}}

In this way, your `Database`, `DAO`, `NetService` and some other tools could be
also static, of which the profit is much more considerable than the additional resource memory.

# Companion extension trick
Declare static `AppDatabase.INSTANCE` based on `AppContext` and `retrofit` first, then `DAO` and `Service`
could be created concisely.
{{< codeImg trick_dao.png >}}
<br>
{{< codeImg trick_netservice.png >}}
<br>
Call them easily. 
{{< codeImg trick_call.png >}}

# KLog
## Main advantages
- Tag is global and has a link.
- Logs on level `V` and `D` are cancelled after the current module releases.
- Light-weight. (2kb in jar)

## Modify logcat view
Extend the displayed tag length first. As for others, it's my personal preferences.
{{< video src=logcat >}} 

## Direct
`obj`'s type is `Any?` and printed out with its `toString`.

{{< codeImg klog.png >}}

## Other levels
{{< codeImg klog_otherLevels.png >}}
`KLog.wtf` is not provided. In my opinion, you'd better throw an error if fatal or use `KLog.e` if not.

## More args
### Tag prefix 
{{< codeImg klog_prefix.png >}} 
<br>
{{< codeImg klog_prefix_.png >}}

### Throwable
This is assignable only in `KLog.e`.
{{< codeImg klog_tr.png >}}

## Avoid tags too long
Make `id`, `tagPrefix` short, and avoid the file name too long, or else the link would fail.
{{< codeImg klog_linkFail.png>}}

## Special types
Useful `KProperty0`, common `Array`, and basic type arrays are specially handled, since their `toString` 
don't tell values. 
{{< codeImg klog_specialTypes.png >}}

## In open source android library
Set an **internal** log object class. 
{{< codeImg mlog_lib_class.png >}}

`id` should be an abbreviated or short all-caps library name for being easily distinguished from other tags.
{{< hint danger >}}
Remember to build the module if there is no directory 'build', or else you probably import `BuildConfig` from another
library.
{{< /hint >}}

Effect
{{< codeImg mlog_lib_effect_code.png >}}
<br>
{{< codeImg mlog_lib_effect_console.png >}}

## In each app submodule
Set a log class extended from `KLog.InApp`. Here sets `id` "DB", supposing in the `database` module.
{{< codeImg mlog_app.png >}}

Effect 
{{< codeImg mlog_app_.png >}}

{{< hint info >}}
`tag~:APP` in the logcat could exclude logs from foreign libraries. 
{{< /hint >}}

## Extend
Source code
{{< codeImg klog_extend_src.png >}}

Take the example of overriding functions on level `E`.   
{{< codeImg klog_extend_sample.png >}}

{{< hint danger >}}
`stackTrace` in `log` is easily disturbed. Never call `log` indirectly like `super...` 
or set defaults for your function parameters.
{{< /hint >}}

Or you could reference my concise `fun log`, `KLog` and design yourself.

## Provide position when `inline`
`stackTrace` is disturbed in `inline` functions and `crossinline` blocks.
Specify `tagPrefix` with `fileName` in these cases. 
This problem also exists in links from other log libraries, and would be fixed by the kotlin standard library.
{{< codeImg klog_test_inline.png >}}
<br>
{{< codeImg klog_test_start.png >}}
<br>
Log out
{{< codeImg klog_test_console.png >}} 

# <a href="https://github.com/ShawxingKwok/AndroidUtil-Core" target="_blank"> GitHub repo </a>
---
title: Util-core
weight: 1
---

{{< hint info >}}
Contains only `AppContext` and `KLog` at present, 
and would be expanded with some small practical tools in the future.
{{< /hint >}}

# Setup
```
dependenceis{
    ...
    implementation ("io.github.shawxingkwok:android-util-core:1.0.0")
}
```

# AppContext
It is the static `applicationContext` for you to get anywhere. In this way, your 
`Database`, `DAO`, `NetService` and some other tools could be static, 
of which the profit is much more considerable than the additional resource memory. 

Source code
{{< codeImg appcontext.png >}}

It's based on {{< newTab startup "https://developer.android.com/topic/libraries/app-startup" >}}.
However, if your `android library` also uses it,      

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
- Tag is global and has a link.
- Logs are intelligently cancelled after released. 
- Light-weight. (2kb in jar)

## Modify logcat view
Extend tag first. As for others, it's my personal preferences.
{{< video src=logcat >}} 

## Direct
`obj`'s type is `Any?` and printed out with its `toString`.

{{< codeImg klog.png >}}

## Other levels
{{< codeImg klog_otherLevels.png >}}
`KLog.wtf` is not provided. In my opinion, you'd better throw an error if fatal or use `KLog.e` if not.

## More args
### Tag prefix 
{{< codeImg klog_prefix.png >}} {{< codeImg klog_prefix_.png >}}

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

## Automatic cancel
{{< codeImg klog_src.png >}}

Logs on level `V`, `D` and `I` are cancelled when not `onDebug`. 

All logs from this library are automatically cancelled when the app is released.

## In open source android library
Set an **internal** log object class. 
{{< codeImg mlog_lib_class.png >}}

`id` should be an abbreviated all-caps library name for being easily distinguished from other tags.
{{< hint danger >}}
Remember to build the module if there is no directory 'build', or else you probably import `BuildConfig` from another
library.
{{< /hint >}}

Effect
{{< codeImg mlog_lib.png >}}
{{< codeImg mlog_lib_.png >}}

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
{{< codeImg klog_expand_src.png >}}

Extend as this.
{{< codeImg klog_expand_sample.png >}}

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
---
title: Util-core
weight: 1
---

{{< hint info >}}
Contains only `AppContext` and `KLog` at present, 
and would be expanded with some small practical tools in the future.
{{< /hint >}}

# Dependency
```groovy
"io.github.shawxingkwok:android-util-core:1.0.0" 
```

# AppContext
It is the static `applicationContext` for you to get anywhere. In this way, your 
`Database`, `DAO`, `SharedPreferences`,`DataStore` and some other tools can be easy
singleton object classes, of which the profit is much more important than the 
additional resource memory. 

{{< codeImg appcontext.png >}}

# KLog
- Practical and concise.
img
- Intelligently cancelled logs after released 
- Little config
- Light-weight (line number)

{{< hint info>}}
The following is told in a progressive relationship, please read in order.
{{< /hint >}}

## Informal learning project 

All logs are cancelled when the app is released. 

声明 Log.wtf 比较鸡肋，应用 Exception 替代

## Open source library
Set a log object class with the abbreviated library name.("DEMO" here)
```kotlin
internal object MLog : KLog(BuildConfig.DEBUG, "DEMO") 
```
{{< hint danger >}}
Remember to build the module if there is no directory 'build', or else you probably import `BuildConfig` from another
library.
{{< /hint >}}

compared to above logs in 'Informal learning project'
- tag changed.
- logs on level `VERBOSE` and `DEBUG` are undone when the library is released. 

## App
1. Set an abstract log class in the basic/common module with the abbreviated application name. 
("FB" here)
    ```kotlin
    abstract class FBLog(onDebug: Boolean, id: String) : KLog(onDebug, "$id-FB")
    ```

2. Set an **internal** log class in each submodule. Here supposes in the `Database` module. 
    ```kotlin
    internal object MLog : FBLog(BuildConfig.DEBUG, "DB")
    ```

3. Use as below

# <a href="https://github.com/ShawxingKwok/AndroidUtil" target="_blank">GitHub repo</a>
---
title: Short term
weight: 1
---

- Ksp is aware of generated code -> Incremental processing, which makes build much faster.

- Ksp is aware of whether `x` is `private` or not -> support `var`
    ```kotlin
    @Tracer.Root
    class Foo{
        var x: Int = 1
            private set
        
        var y: Int = 1
    }
    ```
    <p align="center">↓</p>
  
    Background generates 
    ```kotlin
    public val FooTracer.`_Int_Foo_x` inline get() = `_Foo`.`x` as Int

    public var FooTracer.`_Int_Foo_y` 
        inline get() = `_Foo`.`y` as Int
        inline set(value){ `_Foo`.`y` = value }   
    ``` 

- Ksp is aware of `context receiver` -> properties with `context receiver` won't be traced, and you 
  needn't annotate them with `Tracer.Omit` 

- An IDE plugin for solving the renaming problem of changing objects positions. 
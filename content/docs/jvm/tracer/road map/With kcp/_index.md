---
title: With Kcp
weight: 3
---

# Automatic initial config
```kotlin
@Tracer.Root
class House : HouseTracer{
   
    override val _House get() = this
}

@Tracer.Nodes(House::class)
class Bedroom(override val __House: House) : BedroomTracer {
   val bed = Bed() 
   
   override val _Bedroom: Bedroom get() = this
}

context(BedroomTracer)
class Bed{
    private val house get() = __House
}
```

<p align="center">↓</p>  

```kotlin
@Tracer.Root
class House

@Tracer.Nodes(House::class)
class Bedroom{
    val bed = Bed()
}

context(BedroomTracer)
class Bed{
    private val house get() = __House
}
```

# Automatic hidden generation 
When you finish inputting `val masterBedroom = Bedroom()` in `root class House`, you could inject 
like `private val bedroom get() = _Bedroom` immediately without building.

# Nullable elements are insides traceable and flexibly hinted
```kotlin
@Tracer.Root
class A{
    var b: B? = null
  
    private val name: String? get() = _String_C_name 
}

class B{
    val c = C()
    
    private val name: String get() = _String_C_name
}

class C{
    val name = "Apollo"
}
```
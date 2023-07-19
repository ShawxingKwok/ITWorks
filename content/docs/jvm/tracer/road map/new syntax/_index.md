---
title: New syntax
weight: 3
---

# Automatic config
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
root class House

nodes(House)
class Bedroom{
    val bed = Bed()
}

tracer(Bedroom)
class Bed{
    private val house get() = __House
}
```

# Automatic hidden generation 
When you finish inputting `val masterBedroom = Bedroom()` in `root class House`, you could inject 
like `private val bedroom get() = _Bedroom` immediately without building.

# Nullable elements are insides traceable and flexibly hinted
```kotlin
root class A{
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
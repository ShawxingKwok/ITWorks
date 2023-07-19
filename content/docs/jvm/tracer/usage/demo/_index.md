---
title: Demo
weight: 5
---

With given model and general code below, realize it with tracer and compare with my suggested 
answer. 
# Model
<img src=img.png ></img>

# Conversion
{{< tabs >}}
{{< tab "General" >}}
```kotlin
class Car(
    horsepower: Int,
    engineCapacity: Int,
    outerTireRadius: Int,
    innerTireRadius: Int,
) {
    private val wheels: List<Wheel> = List(4){
        val tire = Tire(outerTireRadius, innerTireRadius)
        tire.inflate()
        val rim = Rim(innerTireRadius)
        WheelImpl(outerTireRadius, innerTireRadius, rim, tire)
    }

    private val engine = Engine(horsepower, engineCapacity, wheels)

    init {
        wheels.forEach { (it as WheelImpl).engine = engine }
    }

    fun drive() {
        engine.start()
        repeat(5) {
            engine.speedUp()
        }
        println("\nDriving at ${wheels.first().speed} miles per hour")
    }
}
```

```kotlin
class Engine(
    private val horsepower: Int,
    private val capacity: Int,
    private val wheels: List<Wheel>,
) {
    var revolvingSpeed = 0
        private set

    fun start() {
        println(
            """

             Engine started
             Horsepower: $horsepower
             Engine capacity: $capacity
             
             """.trimIndent()
        )
        wheels.forEach { it.rotate() }
        println()
    }

    fun speedUp(){
        revolvingSpeed += 10
        println("Speed up")
    }

    fun slowdown(){
        revolvingSpeed -= 10
        println("Slow down")
    }
}
```

```kotlin
class Rim(val radius: Int)
```

```kotlin
class Tire(val outerRadius: Int, val innerRadius: Int) {
    fun inflate() {
        println("Tire inflated")
    }
}
```

```kotlin
interface Wheel {
    val speed: Int
    fun rotate()
}
```

```kotlin
class WheelImpl(
    private val outerRadius: Int,
    private val innerRadius: Int,
    private val rim: Rim,
    private val tire: Tire,
) : Wheel {
    lateinit var engine: Engine

    override val speed: Int get() = (2 * Math.PI * outerRadius * engine.revolvingSpeed / 120).toInt()

    override fun rotate(){
        println("Wheel rotated")
    }
}
```

```kotlin
fun main(){
    val car: Car

    val modelingDuration = measureTimeMillis {
        car = Car(150, 1400, 35, 30)
    }

    val drivingStartDuration = measureTimeMillis { car.drive() }

    println("\nIt takes $modelingDuration ms to model.")
    println("It takes $drivingStartDuration ms to start driving.")
}
```
{{< /tab >}}
{{< tab "Tracer" >}} 
```kotlin
class Car(
    val horsepower: Int,
    val engineCapacity: Int,
    val outerTireRadius: Int,
    val innerTireRadius: Int,
) : CarTracer{
    val wheels: List<Wheel> = List(4){ WheelImpl(_Car) }
    val engine = Engine()

    fun drive() {
        engine.start()
        repeat(5) { engine.speedUp() }
        println("\nDriving at ${wheels.first().speed} miles per hour")
    }

    override val _Car: Car get() = this
}
```

```kotlin
context (CarTracer)
class Engine {
    val horsePower: Int = _Int_Car_horsepower
    val capacity: Int = _Int_Car_engineCapacity
    var revolvingSpeed = 0
        private set

    private val wheels get() = `_List‹Wheel›_Car_wheels`

    fun start() {
        println(
            """

             Engine started
             Horsepower: $horsePower
             Engine capacity: $capacity
             
             """.trimIndent()
        )
        wheels.forEach { it.rotate() }
        println()
    }

    fun speedUp(){
        revolvingSpeed += 10
        println("Speed up")
    }

    fun slowdown(){
        revolvingSpeed -= 10
        println("Slow down")
    }
}
```

```kotlin
context (WheelImplTracer)
class Rim{
    val radius: Int get() = _Int_Tire_innerRadius
}
```

```kotlin
context (WheelImplTracer)
class Tire {
    val innerRadius: Int = `__Int_˚Car_Car_innerTireRadius`
    val outerRadius: Int = `__Int_˚Car_Car_outerTireRadius`

    fun inflate() {
        println("Tire inflated")
    }
}
```

```kotlin
interface Wheel {
    val speed: Int
    fun rotate()
}
```

```kotlin
class WheelImpl(override val __Car: Car) : WheelImplTracer, Wheel {
    val rim = Rim()
    val tire = Tire().apply { inflate() }

    private val revolvingSpeed get() = `__Int_˚Car_Engine_revolvingSpeed`

    override val speed: Int get() = (2 * Math.PI * tire.outerRadius * revolvingSpeed / 120).toInt()

    override fun rotate(){
        println("Wheel rotated")
    }

    override val _WheelImpl: WheelImpl get() = this
}
```

```kotlin
fun main(){
    val car: Car

    val modelingDuration = measureTimeMillis {
        car = Car(150, 1400, 35, 30)
    }

    val drivingStartDuration = measureTimeMillis { car.drive() }

    println("\nIt takes $modelingDuration ms to model with Tracer.")
    println("It takes $drivingStartDuration ms to start driving with Tracer.")
}
```
{{< /tab >}}
{{< /tabs >}}
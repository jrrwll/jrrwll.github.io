
---
title: "Scala"
---

## Scala语法

```scala
// import math._ but cos
import math.{cos => _, _}

object HelloWolrd {
    def main(args: Array[String]): Unit = {
        val myVal: String = "Hello World!"
        var myVar: Long = System.currentTimeMillis()
        myVar += 1
        val name = myVal:Object // cast
        println(s"$myVal")
    }
    def patternMatch(x: Any): Any = x match {
        case 1 => 1
        case "five" => 5
        case _ => 0
    }
}
```

```scala
trait Animal {
    final val age = 18
    val color: String
    val kind = "Animal"
    def eq(x: Any): Boolean
    def ne(x: Any): Boolean = !eq(x)
}
class Cat extends Animal {
    override val color = "Yellow"
    override val kind = "Cat"
    def eq(x: Any): Boolean = false
}
class Dog(name: String) extends Animal {
    def this() = this("Dog")
    override val color = "Brown"
    def eq(x: Any): Boolean = true
}
```

```scala
trait Traversable {
    def foreach[U](f: Elem => U): Unit
}

trait Iterable extends Traversable

trait Seq extends Iterable
trait Set extends Iterable
trait Map extends Iterable

import cpllection.JavaConvertions._
```
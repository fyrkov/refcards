###  Kotlin basics
`class Person(val name: String, val age: Int)` has full constructor and two getters.\
`data class Person(val name: String, val age: Int)` additionally generates `equals`, `hashCode` and `toString`.


#### Functions

Functions can be:
- top-level
- member function (i.e. method)
- local function inside a function

Void function:
```
fun displayMax(a: Int, b: Int) {
    println(...)
}
```

Function with expression body:
```
fun max(a: Int, b: Int) : Int = if (a>b) a else b
```

Default arguments:
```
fun displayMax(a: Int = 0, b: Int = 0) {
    println(...)
}
```

Calling Kotlin top-level function from Java
```
// Without this annotation calling would look like MyFileKt.foo();
@file:JvmName("Util")
package intro

fun foo() = 0
```
```
package other;

imports intro.Util;

public class JavaUsage {
    public static void main(String[] args) {
        int i = Util.foo();
    }
}
```

#### Control structures

`If` can be an expression:
```
val max = if (a > b) a else b;
```

`When` is useful for > 2 branches.\
`When` can also be an expression:
```
enum class Color {
    BLUE, RED
}

fun displayMax(coler: Color): String =
    when (color) {
        BLUE -> "cold"
        RED -> "hot"
    }
}
```

`When` can execute without a variable and test any boolean:
```
val (descr, color) = when {
    degrees < 5 -> "cold" to BLUE
    degrees < 23 -> "mild" to ORANGE
    else -> "hot" to RED
}
```

`For` loops:
```
for (i in 1..9) {

}
...
for (i in 1 until 9) {
    
}
...
for (i in 1 downTo 9 step 2) {
    
}
...
for (ch in "abc") {
    
}
...
val list = listOf("a", "b")
for (s in list) {
    
}
...
val map = mapOf(1 to "a", 2 to "b")
for ((key, value) in map) {
    
}
...
val list = listOf("a", "b")
for ((index, elemnt) in list.withIndex()) {
    
}
```


#### Ranges and `in` check.
Ranges can be built from comparable objects.
- 1..9
- 1 until 10
- 'a'..'z'
- "ab".."az"
- startDate..endDate

Storing a range: 
```
val dataRange: ClosedRange<MyDate> = startDate..endDate
```


Usage of `in` for range check:
```
if (myDate in startDate..endDate) {

}
...
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
...
fun isNotDigit(c: Char) = c !in '0'..'9'
```

Usage of `in` for collection check:
```
// The same as list.contans(element)
if (element in list) {

}
```

Usage of `in` for iteration:
```
for (i in 'a'..'z') {

}
```

#### Extension functions
Add additional functionality on top of the exsiting class without modifying it
```
fun String.lastChar() = get(length - 1)
...
import ...lastChar
valc = "abc".lastChar();
...
fun List<Int>.sumExt(): Int {
    var result = 0
    for (i in this) {
        result += i
    }
    return result
}
```

#### Naullable types
There is a compile time check for nullity for standard type declareations.\
Nullable types are declared with `?`
```
val s1: String? = null
...
s1?.length  // safe access expression
...
val length: Int = s1?.length ?: 0  // safe access expression with Elvis
```
Not-null assertion will throw NPE if var is null
```
val s1: String? = null
s1!!
```
Containers can be declared as:
```
List<Int?>
List<Int>?
```

#### Safe casts
`instanceof` = `is`\
casting = `as` (produce `ClassCastException` if can't cast)
safe casting = `as?` (produce null if can't cast) 

#### New on lambdas
`run` function runs the lambda and return it last value:
```
// this var will store 42
val foo1 = run {
    println("")
    42
}
```

#### New lambdas for collections

```
val list: List<Int> = listOf(1,2,3,4)

// unlike groupBy creates pairs - if duplicates exist last occurrence win
// good when key is unique
val associate: Map<Char, Int> =  list.associate {'a' + it to it * 10}

// splits into two collections: one that satisfies the prediceate and one with the rest
val partition: Pair<List<Int>, List<Int>> = list.partition { it % 2 == 0 }

// merges two collections into collection of pairs.
val zip: List<Pair<Int, Int>> = list.zip(list)

// flattens nested iterables
val listOf: List<List<Int>> = listOf<List<Int>>(list)
val flatten: List<Int> = listOf.flatten()
```

Storing a lambda in a variable:
```
// isEven here is a function type
val isEven: (Int) -> Boolean = {
    i: Int -> i % 2 == 0
}
```

:exclamation: `return` always returns from `fun.`
A `return` from a lambda is possible with the following syntax:
```
list.flatMap {
    if (it == 0) return@flatMap listOf<Int>()
    listOf(it, it)
}
```
Another option is yo use anonymous function:
```
list.flatMap(fun (e): List<Int> {
    if (e == 0) return listOf()
    return listOf(e, e)
})
```

#### Accessors
Property without a field
```
class Rectangle(val height: Int, val width: Int) {

    val isSquare: Boolean
        get() {
            return height == width
        }
}
...
println(rectangle.isSquare)
```

Making setter private:
```
class Rectangle() {

    // only accessible within the class
    val counter: Int = 0
        private set
}
```

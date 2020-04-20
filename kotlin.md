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

Extension functions extending class `A` can be created only in the class `B`. In the followin example it is a **member extension** and can access all class members.
```
class Words {
    private val list = mutableListOf<String>()

    // can be called then only when `this`
    // to an instance of Words is available
    fun String.record(): Words {
        list.add(this)
        return this@Words
    }
}
...
val words = Words()
with(words) {
    "one".record()
}
```

#### Companion objects
"Static" functions.
Companion objects provide static-similar behavior:
```
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
...
val instance = MyClass.create()
```

#### Nullable types
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

#### Constructors
Primary cotr is a part of class header.
```
class Person(firstName: String) { /*...*/ }
// or 
class Person constructor(firstName: String) { /*...*/ }
```
It can not contain initialization code directly in the body but can in special `init` blocks:
```
class InitOrderDemo(name: String) {
    val firstProperty = "First property: $name".also(::println)
    
    init {
        println("First initializer block that prints ${name}")
    }
}
```
Declaring properties and initializing them from the primary constructor:
```
class Person(val firstName: String, 
        val lastName: String, 
        var age: Int) { /*...*/ }
```
Declaring secondary constructors prefixed with `constructor`:
```
class Person {
    var children: MutableList<Person> = mutableListOf<Person>();
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
```

Inheritance with calling a super class constructor:
```
open class Parent(val name: String)
class Child(name: String) : Parent(name)
...
class Child : Parent {
    constructor(name: String, param: Int) : super(name)
}
```
Another way to call parent constructor `super(width)`:
```
class GameBoardImpl<T>(width: Int) : BoardImpl(width), GameBoard<T> {}
```


#### Properties and Accessors
Access modifiers:
- `private` means visible inside this class only (including all its members);
- `protected` — same as `private` + visible in subclasses too;
- `internal` — any client inside this module who sees the declaring class sees its `internal` members;
- `public` — any client who sees the declaring class sees its `public` members.

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
Mutable property
```
var i = 0
val foo: Int
    get() = i++
    // or
    get() {
        return i++
    }
```

Making setter private:
```
class Rectangle() {

    // only accessible within the class
    val counter: Int = 0
        private set
}
```

Defining proprty in an interface and overriding it in sub-classes
```
interface User {
    val nickname: String
}
...
class FacebookUser(val accId: Int) : User {
    // Here property is stored in a field
    override val nickname = getFacebookName(accId)
}
...
class SubscribeUser(val email: String) : User {
    override val nickname: String
        // property with custom getter doesn't have the corresponding field
        get() = email.substringBefore('@')
}
```
Defining an extension property (like an extension function)
```
val String.lastIndex: Int
    get() = this.length - 1
...
// mutable ext prop
val String.lastChar: Char
    get() = get(length - 1)
    set(value: Char) {
        this.setCharAt(length - 1, value)
    }
```

Lazy property (initialized only on the first access)
```
val lazyVal: String by lazy {
    println("computed lazyVal")
    "Hello"
}
```

Late initialization property. Initialize the property not in the constructor, but in a specially designated for that purpose method
```
class MyClass {
    lateinit var lateProp: String
    ...
    fun init() {
        lateProp = "value"
    }
    ...
    // without lateinit
    lateProp?.length
    // with lateinit
    // may throw UninitializedPropertyAccessException
    lateProp.length
}
```

#### Enum
```
enum class Color(val r: Int, val g: Int, val b: Int) {
    // mind the semicolon
    BLUE(0,0,255), ORANGE(255,165,0), RED(255,0,0);
    fun rgb() = (r * 256 + g) * 256 + b
}
```

#### Data class
Has `equals`, `hashCode`, `copy`, `toString` and some others.
```
data class Contract(val name: String, val address: String)
...
contract.copy(address = "new address")
```
:exclamation: `equals` uses only properties defined in the primary constructor.

#### Sealed class
```
interface Expr
class Num(val value: Int) : Expr()
class Sum(val left: Expr, val right: Expr) : Expr()
...
//  compiler says that 'when' expression is not exhausted if there is no `else` branch
fun eval(e: Expr): Int = when (e) {
    is Num -> e.value
    is Sum -> eval(e.left) + eval(e.right)
    else -> throw IllegalArgumentExpression()
}
```
`sealed` modifier (applicable for a class) restricts class hierarchy to the set of sub-classes **located in the same file**:
```
sealed class Expr
```

#### Nested and inner classes
Nested class:
```
// java
static class A {}
// kotlin
class A {}
```
Inner class:
```
// java
class A {}
// kotlin
inner class A {}
```
Accessing the outer reference from an inner class:
```
class A {
    inner class B {
        this@A
    }
}
```

#### Class delegation
Automatically generates delegate methods with `by` keyword:
```
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override fun print() { print(x) }
}

// Derived implemets Base by delegating to b
class Derived(b: Base) : Base by b

fun main() {
    val b = BaseImpl(10)
    Derived(b).print()
}
```

#### Object and object expressions
Object is a singleton in Kotlin.
```
object KSingleton {
    fun foo() {}
}
```
Object expressions can replace java's anonymous classes. These classes are not singletons.
```
window.addMouseListener(
    object : MouseAdapter() {
        override fun mCLicked(e: MouseEvent) {}
    }
)
```

#### Operator overloading
Supported for the following symbols:
- `+`, `-` (for `Comparable`)
- `>`, `<`, `>=`, `<=`
-  `*`, `/`, `%`
- unary operators: `+a`, `++a`, `a++`, `-a`,..., `!a`
- access by index: `x[i]`
- equality: `==`
- referential equality: `===`
- range creation: `1..100`, `a..f`
- contains: `in`

Example:
```
val list = listOf(1, 2, 3)
val newList = list + 2 // for immutable creates a new list
...
val mutableList = mutableListOf(1, 2, 3)
mutableList += 4 // updates the list
```

Defining custom operator for `get` and `set` with square brackets:
```
class Board {...}

operator fun Board.get(x: Int, y: Int): Char {...}
operator fun Board.set(x: Int, y: Int, valueL Char): Char {...}

board[1, 2] = 'x'
```
Defining 

#### Some library `inline` functions
Normal functions that takes lambdas as an argument require creating an anonymous class that will capture outer scope vars. Inline functions are inlined by the compiler as it's body instead. As a result, there is no performance over head.

Usage example for `inline` - calling a higher order function that accepts a lambda in a loop thus eliminating N allocations of anonymous classes.

1. `run` function runs the lambda and return it last value. Can be called with safe access (unlike `with`)
```
val windowOrNull = ...
windowOrNull?.run {
    width = 300
}
```
2. `let` for nullable types
```
fun getEmail(): Email?
...
val email = getEmail()
if (email != null) senEmailTo(email)
// Or alternatively
email?.let {e -> sendEmailTo(e)}
```
3. `takeIf` returns the receiver object if it passes the predicate or `null` otherwise. The opposite is `takeUnless`
```
issue.takeIf { it.status == FIXED }
...
person.patronymicName.takeIfString::isNotEmpty)
...
issues.filter {it.status == OPEN }
    .takeIf(List<Issue>::isNotEmpty)
    ?.let { println("here are some open issues") }
```
4. `repeat`
```
repeat(10) {
    println("Welcome")
}
```
5. `synchronized`
```
fun foo(lock: Lock) {
    synchronized(lock) { println("action") }
}
// which is equal to
fun foo(lock: Lock) {
    lock.lock()
    try {}
        println("action")
    } finally {
        lock.unlock()
    }
}
```
6. `withLock`
```
val l: Lock = ...
l.withLock { ... }
```
7. `use` for try with resources
```
// java
try (BufferedReader br = new BufferedReader(new FileReader(path))) {
    return br.readline();
}
// kotlin
BufferedReader(FileReader(path)).use { br-> return br.readLine()}
```

8. `with` has `this` as implicit receiver. 
It calls it's second argument (labmda) on the first argument (receiver) as an extension function:
```
inline fun <T, R> with(
    receiver: T,
    block; T.() -> R
): R = receiver.block()
```
Example:
```
val sb = StringBuilder()
with (sb) {
    append("Alphabet")
    for (c in 'a'..'z') {
        append(c)
    }
    toString()
}
```

`with` vs `run`:
```
with (x) { ... }
x.run { ... }
```

9. `apply` calls the given block with `this` as implicit receiver. `apply` returns the receiver, and not the last lambda value like `run` or `with`
```
val mainWindow = windowById["main"]?.apply {
    width = 300
} ?: return
```

10. `also` returns the receiver like `apply` but accepts regular lambda.
 Lambda with the receiver is really useful when you can omit `this` reference because you only call it's members.  However, there are cases when you pass the receiver is an argument.
```
 windowById["main"]?.apply {
    width = 300
}?.also {
    showWindow(it)
}
```

|                         | { .. this .. } | { .. it .. } |
|-------------------------|----------------|--------------|
| return result of lambda | `with` / `run`     | `let`          |
| return receiver         | `apply`          | `also`         |

#### Lambda with receiver
Lambda with receiver is simply a lambda with an implicit `this` inside.

Lambda vs lambda with receiver:
```
// regular lambda
val isEven: (Int) -> Boolean = { it % 2 == 0}
// lambda with receiver. `Int` is receiver type
val isOdd: Int.() -> Boolean = { it % 2 == 1}
...
// calling regular lambda
isEven(0)
// lambda with receiver is called as an extension function
1.isOdd()
```

#### Sequences
Is an analogue of Java `Stream` with lazy computations.

Problem: inline functions defined as extensions on Iterables create intermediate collections:
```
val list = listOf(1, 2, -3)
val maxOddSquare = list
    .map { it * it } // here new List created
    .filter { it % 2 == 1 } // here new list created
    .max()
```
`Sequence` performing all steps in a lazy manner (only when a terminal operator is called):
```
val maxOddSquare = list
    .asSequence()
    .map { it*it }
    .filter { it % 2 == 1 }
    .max()
```

:exclamation: Operations on collections are applied for the whole collection on each step (horizontal evaluation).\
Operations on sequences are applied per element (vertical evaluation).

Generating a sequence not from Iterable:
```
// the sequence is finished when lamda return `null`
val seq = generateSequence {
    Random.nextInt(5).takeIf { it > 0 }
}
...
val input = generateSequence {
    readLine().takeUnless { it == "exit" }
}
...
// generating infinite sequnece (lazy!)
val input = generateSequence(0) { it + 1 }
numbers.take(5).toList() // [0, 1, 2, 3, 4]
```

Using `yield` with `Sequence`
```
fun fibonacci(): Sequence<Int> = sequence {
    var elements = Pair(0, 1)
    while (true) {
        yield(elements.first)
        elements = Pair(elements.second, elements.first + elements.second)
    }
}
```
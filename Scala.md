# Scala
## 1. Basic Scala
1. Values
- vals are immutable
- Compiler can infer types
2. Variables
- Mutable
```scala
var eVariable: Int = 4 //side effects
eVariable = 1
eVariable += 1
```
3. Types
```scala
val x: Int = 4
val eString: String = "Hello"
val anotherString = "goodbye"
val eBoolean: Boolean = false
val eChar: Char = 'a'
val anInt: Int = x
val eShort: Short = 4613
val eLong: Long = 89258230482958093L
val eFloat: Float = 2.0f
val eDouble: Double = 3.14
```
4. Expressions
- Everything in Scala is an expression
```scala
val eWeirdValue = (aVariable = 3) //Unit === void
```
- Side effects: println(), whiles, reassigning
- code blocks:
```scala
val eCodeBlock = {
    val y = 2
    val z = y + 1 // z is not visiable outside

    if (z > 2) "hello" else "goodbye"
}
```
- Above is called a code block
- code block is an expression
- The value is the last expression
- instruction are executed (Java), expressions are evaluated (Scala)
5. Functions
- Syntax: args and return type
```scala
def aFunction(a: String, b: Int): String = {
    a + " " + b
}
// ParameterlessFunction () can be ignored
def eParameterlessFunction(): Int = 42

def aRepeatedFunction(eString: String, n :Int): String = {
    if (n == 1) aString
    else aString + aRepeatedFunction(aString, n-1)
}
```
- When need loops, use recursion
6. Type Inference
7. Recursion
- Stack recursion: may Stackoverflow Error
- Tail recursion: use this
```scala
def factorial(n: Int): BigInt = {
    def factHelper(x: Int, accumulator: BigInt): BigInt =
        if (x <= 1) accumulator
        else factHelper(x - 1, x * accumulator)
    factHelper(n, 1)
}
```
8. Call-by-Name and Call-by-Value
```scala
def calledByValue(x: Long): Unit = {
    println("by value: " + x)
    println("by value: " + x)
}
// => the expression is passed literally
def calledByName(x: => Long): Unit = {
    println("by value: " + x)
    println("by value: " + x)
}
```
9. Default and Named Arguments
```scala
def trFact(n: Int, acc: Int = 1): Int =
    if (n <= 1) acc
    else trFact(n -1, n*acc)
```
10. Smart Operations on String
```scala
val str: String = "Hello, I am learning Scala"
println(str.charAt(2))
println(str.substring(7, 11))
println(str.split(" ").toList)
println(str.startsWith("Hello"))
println(str.replace(" ", "-"))
println(str.toLowerCase())
println(str.length)
str.take(2)
str.reverse

// S-Interpolators
val name = "zhi ji"
val age = "23"
val greeting = s"Hello, my name is $name and I will be turning $(age + 1) years old"
// F-interpolators, formatted string
val myth = f"$name%s can eat $speed%2.2f burgers per minute"
// raw-interpolator
println(raw"This is a \n newline") // printed literally
val escaped = "This is a \n newline";
println(raw"$escaped") // \n will break the line
```

## 2. OOP for scala
1. Basic
- Class paramaters are not Fields(member)
```scala
class Person(name: String, age: Int) // constructor

class Person(name: String, val age: Int) {// now you can use parameter as field

    // Method
    def greet(name: String): Unit = println(s"$name says: Hi, $name")

    // overloading
    def greet(): Unit

    // multiple constructor
    def this(name: String) = this(name, 0)
    def this() = this("John Doe");

} 
```
2. Method Notation
```scala
class Person(name: String, favoriteMovie: String) {
    def likes(movie: String): Boolean = movie == favoriteMovie
}

val mary = new Person("Mary", "Inception")

// infix notation = operator notation:
println(mary likes "Inception")
```
3. Scala Objects
- Scala does not have class-level functoinality "static"
- Scala object = singleton instance
```scala
object Person {
    val N_EYES = 2
}
```
- Companions: have the same name for a class and an object
```scala
object Person {
    val N_EYES = 2
}

class Person {

}

val mary = new Person
val john = new Person
println(mary == john) // false

val mary = Person
val john = Person
println(mary == john) // true

def main(args: Array[String]): Unit

```
4. Inheritance 

5. Packaging and Import
- Package members are accessible by their simple name
- import the package
```scala
val date = new Date
val sqlDate = new SqlDate(2018, 5, 4)
```
- Packages are in hierarchy

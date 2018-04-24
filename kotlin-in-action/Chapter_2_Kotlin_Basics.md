## 2.0 Functions and Variables

### 2.0.1 Variables
Even though a val reference is itself immutable and cannot be changed, the object that it points to may be mutable.

```
val languages = arrayListOf("Java")
languages.add("Kotlin”)
```

## 2.1 Classes and Properties

### 2.1.0 Properties
When interloping between Kotlin and Java, the properties are accessed as follows:


Class | Kotlin | Java
--- | --- | ---
Person | `name` | `getName()`<br>`setName()`
Person | `isMarried` | `isMarried()`<br>`setMarried()`

### 2.1.1 Layout: Directories and Packages
You should not be afraid to store several classes in the same file

### 2.1.2 Accessing Properties
Kotlin automatically generates setters and getters for you so you do not have to explicitly implement them as you would in Java. 

Let's look at class `Person` below:

```
data class Person(var name: String, var lastName: String)
```

You can access the properties by doing

```
val person = Person("Pascal", "How")
val firstName = person.name
val lastName = person.lastName
```

You can also assign new values to `name` and `lastName` to update the state of `person`

```
person.name = "James"
person.lastName = "Coggan"
println("firstName: ${person.name} and lastName: ${person.lastName}")
```

**Output**
```
firstName: James and lastName: Coggan
```

If an immutable property, `fullName` is defined in `Person`, the value of `fullName` gets assigned at the time a new `Person` object is created. 

Changing the value of `name` and `lastName` will not change the value of `fullName` because the latter is declared as `val`

```
data class Person(var name: String, var lastName: String) {
    val fullName = "$name $lastName"
}

//	Elsewhere
val person = Person("Pascal", "How")
person.name = "James"
person.lastName = "Coggan"

println("fullName: ${person.fullName}")
```

**Output**
```
fullName: Pascal How
```

However, this gets trickier when defining a custom getter to return the `fullName` such as in the example below because `get()` will evaluate the `fullName` every time

```
data class Person(var name: String, var lastName: String) {
    val fullName
        get() = "$name $lastName"
}

//	Elsewhere
val person = Person("Pascal", "How")
person.name = "James"
person.lastName = "Coggan"

println("fullName: ${person.fullName}")

```
**Output**
```
fullName: James Coggan
```

## 2.2 Enum and when
Just as in Java, enums are not lists of values and you can declare properties or methods on enum classes. Note that the last enum entry is followed by `;`

### 2.2.1 Enum With Properties

```
enum class Color(val r: Int, val g: Int, val b: Int) {

    RED(255,0,0), ORANGE(255,165,0),
    YELLOW(255,255,0), GREEN(0,255,0),
    BLUE(0,0,255), INDIGO(75,0,130),
    VIOLET(238, 130,238);

    fun rgb() = (r * 256 + g) * 256 + b
    
}

println(Color.BLUE.rgb())

```

**Output**
```
255
```

The code finds the branch corresponding to the color value and unlike in Java, a break statement is not needed

```
fun getWarmth(color: Color) = when(color) {
    Color.RED, Color.ORANGE, Color.YELLOW -> "warm"
    Color.GREEN -> "neutral"
    Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold"
}

println(getWarmth(Color.ORANGE))
```

**Output**
```
warm
```

### 2.2.2 Using when with arbitrary objects
The when construct is more powerful than Java switch statement and allows branch conditions other than enum constants,strings and number literals. 
when allows any object.

The Kotlin library contains a function setOf that creates a Set containing objects specified as its arguments. The order of the objects does not matter and two sets are equal as long as they contain the same items.

```
fun mix(c1: Color, c2: Color) =
        when (setOf(c1, c2)) {
            setOf(Color.RED, Color.YELLOW) -> Color.ORANGE
            setOf(Color.YELLOW, Color.BLUE) -> Color.GREEN
            setOf(Color.BLUE, Color.VIOLET) -> Color.INDIGO
            else -> throw java.lang.Exception("Dirty color")
        }
        
println(mix(Color.BLUE, Color.YELLOW))
```

**Output**
```
GREEN
```

### 2.2.3 Using when for type checking
The when expression can also be used to check for the type of the when argument value. There is no need to cast a variable explicitly after checking that it has a certain type.
The compiler does it for you automatically using a smart cast.

```
fun eval(e: Expr): Int =
        when (e) {
            is Num -> {
                println("num: ${e.value}")
                e.value
            }
            is Sum -> eval(e.Left) + eval(e.right)
            else -> throw IllegalArgumentException("UnknownExpression")
        }
```

Also note that if we have a block as branch, the last expression in the block is returned.

## 2.3 Iterating Over Things
### 2.3.1 Ranges and Progressions

**Ranges** | **Output**
--- | ---
`for (i in 1..4) print(i)` | prints "1234"
`for (i in 4..1) print(i)` | prints nothing
`for (i in 4 downTo 1) print(i)` | prints "4321"
`for (i in 1..4 step 2) print(i)` | prints "13"
`for (i in 4 downTo 1 step 2) print(i)` | prints "42"
`for (i in 1 until 10) println(i)` | i in [1, 10), 10 is excluded

### 2.3.2 Iterating Over Maps
Read values with `map[key]`
Write values with `map[key] = value`    //    This is similar to the Java version `map.put(key, value)`

To iterate over a collection while keeping track of the index of the current item, the following unpacking syntax can be used. We do not need to store the index into a separate variable and increment it by hand as we would in Java
```
val list = arrayListOf("10", "11", "1001")
```

```
for ((index, element) in list.withIndex()) {
    println("$index: $element")
}
```

**Output**
```
0: 10
1: 11
2: 1001
```

### 2.3.3 Using in with when branches
Checking if a character belongs to a certain range can be done easily

```
private fun recognise(c: Char) = when (c) {
    !in '0'..'9' -> "It is not a digit"
    in 'a'..'z', in 'A'..'Z' -> "It is a letter"
    else -> "No idea"
}

println(recognise('a')
```
**Output**
```
It is a letter
```
```
println("Kotlin" in "Java".."Scala”)
```
**Output**
```
true
```

Interestingly this prints `true` due to the way that String class implements the Comparable interface. The strings here are compared alphabetically

## 2.4 Exceptions

### 2.4.1 throw
Exception handling in Kotlin is very similar to Java except that kotlin does not require you to declare the exceptions that can be thrown by a function.
In Java, you would explicitly write throws IOException after the function declaration.

```
val percentage =
        if (number !in 0..100) {
            number
        } else {
            throw IllegalArgumentException("A percentage must be a number from 0 to 100: $number")
        }
```

In Kotlin, unlike Java, throw is an expression

### 2.4.2 try, catch, finally

`try` can be used as an expression and similarly to other expressions, the value of the expression as a whole is the value of the last expression. 

```
fun readNumber(reader: BufferedReader) {
    val number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        return
    }
    println(number)
}

val reader = BufferedReader(StringReader("not a number"))
readNumber(reader))
```
**Output**

Nothing gets printed. This is because of the return statement in the catch block meaning that the execution of the function does not continue after the catch block

```
fun readNumber(reader: BufferedReader) {
    val number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        null
    }
    println(number)
}

val reader = BufferedReader(StringReader("not a number"))
readNumber(reader))
```
**Output**
```
null
```
A `NumberFormatException` is caught so the result value is null and gets printed.

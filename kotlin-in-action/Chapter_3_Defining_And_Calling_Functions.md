## 3.0 Creating collections in Kotlin
Kotlin uses the same standard library classes as Java but we can do a lot more with them.
For example, we can obtain the last element in a list or the maximum in a collection of numbers

```
val strings = listOf("first", "second", "fourteenth")
println(strings.last())
```

**Output**
```
fourteenth
```

```     
val numbers = setOf(1, 14, 2)
```

**Output**
```
println(numbers.max())
14
```

## 3.1 Making Functions Easier To Call
Kotlin offers a lot of useful mechanisms to make functions easier to call by getting rid of the boilerplate code and making your code less verbose.

### 3.1.1 Named Arguments
Consider the function `joinToString(collection, " ", " ", ".")`
This function takes in a collection such as `listOf(1, 2, 3)` and returns the list of values with a prefix, postfix and separated by a defined separator.
The problem here is that by looking at the function itself, it is difficult to determine which is the prefix, the postfix or the separator without looking at the signature of the function.

With Kotlin you can use named arguments as follows:
`joinToString(collection, separator = " ", prefix = " ", postfix = " ")`

**Tips**
1. Order of the parameters do not matter
2. Unfortunately you cannot use named arguments when calling methods written in Java

### 3.1.2 Default Parameter Values
A problem in Java is the abundance of overloaded methods created often for the sake of backwards compatibility

Some of the problems this causes include:
1. Duplication
2. When calling an overload that omits some parameters, it is unclear what values are used for them

In Kotlin you can specify default values to avoid creating overloads

```
fun <T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
) : String
```

**Output**
```
joinToString(list, ", ", "", "")
1, 2, 3

joinToString(list)
1, 2, 3

joinToString(list, ";")
1, 2, 3
```

**Tips**
1. When using the regular syntax, you have to specify the arguments in the same order as in the function declaration and you can only omit trailing arguments
2. When using named arguments, you can omit some arguments from the middle of the list and specify only the ones you need in any order you want

**Output**
```
joinToString(list, suffix = ";", prefix = "# ")
# 1, 2, 3;
```

**Defaults and Java**
Note that Java does not have the concept of default parameter values. If you call the Kotlin function with default parameter values from Java, you have to call all the parameter values explicitly.
To facilitate this, you can annotate the Kotlin function with `@JvmOverloads` and this will instruct the compiler to auto-generate the Java overloaded methods while omitting each of the parameters one by one.

Annotating the `joinToString` method with `@JvmOverloads` generates
```
String joinToString(Collection<T> collection, String separator, String prefix, String postfix)
String joinToString(Collection<T> collection, String separator, String prefix)
String joinToString(Collection<T> collection, String separator)
String joinToString(Collection<T> collection)
```

## 3.2 Top Level Functions and Properties
In Java all methods need to be part of a class but very often, these classes do not need to hold any state and we end up with a lot of static factory methods. Such examples include the `Collections` class as well as classes with `Util` in their names.

In Kotlin, you can define functions directly at the top level of a source file outside a class. When using this function in Kotlin, it will be part of your imports. Consider the following example:

```
File name: join.kt 

fun joinToString(...): String { ... }

//  Usage
joinToString(list, ", ", "", "")
```

The Java equivalent

```
public class JoinKt {
    public static String joinToString(...) { ... }
}

//  Usage
JoinKt.joinToString(list, ", ", "", "")
```

Note the Kt appended to the file name. This is not very intuitive and the naming is ugly. It would make more sense to have something named `StringUtil` for instance. This can easily be done by changing the file name with the help of an annotation

```
@file:JvmName("StringUtil")

fun joinToString(...): String { ... }
```

The `joinToString` function can now be called from Java as `StringUtil.joinToString(list, ", ", "", "")`

## 3.3 Extension Functions and Properties
Extension functions is one of the cool new features of Kotlin that help with extending existing classes. Imagine that you are working with a 3rd party library and you really wished that it has a specific functionality that it is currently lacking. Kotlin extension functions allow you to add new functions to existing classes and call them on an instance of that class as if the method belonged to the class itself without having to resort to composition or the decorator pattern.

To use it, put down the name of the class you wish to extend followed by the name of the function you want to add to that class. The class you are extending is called the _receiver type_. When calling an instance of that class, it is referred to as the _receiver object_. This can be seen in the example below:

```
fun String.lastChar(): Char = this.get(this.length - 1)
```

To use this extension function, you can do `"Kotlin".lastChar()`
`String` is the receiver type
`Kotlin` is the receiver object

**Note**
- Extension functions can directly access methods and properties of the class you are extending
- Extension functions do not allow you to break encapsulation. If a property is declared as private or protected within the class, the extension will not have access to it.

### 3.3.1 Calling ExtensionFunctions From Java
Under the hood an extension function is a static method that accepts the receiver object as its **first** argument. If the extension function was declared in a file named StringUtil.kt, call the function in Java as follows:

```
char c = StringUtilKt.lastChar("Java")
```

### 3.3.2 Improving The _joinToString_ function

```
fun <T> Collection<T>.joinToString(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in this.withIndex()) {
        if(index >0) result.append(separator) {
            result.append(element)
        }
    }
    result.append(postfix)
    return result.toString()
}
```

**Output**
```
val list = listOf(1, 2, 3)
println(list.joinToString(separator = "; ", prefix = "(", postfix = ")"))

(1; 2; 3)
```



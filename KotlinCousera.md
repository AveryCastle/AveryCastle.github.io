# Convert Java to Kotlin

```java
void updateWeather(int degree) {
  String description;
  Color color;
  if (degree < 10) {
    description = "cold";
    color = Color.BLUE;
  } else if (degree < 25) {
    description = "mild";
    color = Color.ORANGE;    
  } else {
    description = "hot";
    color = Color.RED;    
  }
}
```

Step1. kotlin function + variable 사용하기
```kotlin

fun updateWeather(degree: Int) {
  var description : String
  var color : Color
  if (degree < 10) {
    description = "cold"
    color = Color.BLUE
  } else if (degree < 25) {
    description = "mild"
    color = Color.ORANGE
  } else {
    description = "hot"
    color = Color.RED
  }
}
```

Step2. when condition + Pair 사용해서 if..else 반복문 및 중복된 값 할당문 없애기
```kotlin

fun updateWeather(degree: Int) {
    var (description : String, color : Color) = when {
        degree < 10 -> Pair("cold", Color.BLUE)
        degree < 25 -> Pair("mild", Color.ORANGE)
        else -> Pair("hot", Color.RED)
    }
}

```


Step3. Pair 대신 Infix function **to** 사용하기
```kotlin

fun updateWeather(degree: Int) {
    var (description : String, color : Color) = when {
        degree < 10 -> "cold" to Color.BLUE
        degree < 25 -> "mild" to Color.ORANGE
        else -> "hot" to Color.RED
    }
}

```

## val, var
- val: read-only value
- var: mutable variable

## function
### Top Level function을 갖고 있는 클래스 이름 변경하는 방법

Main.kt 파일:
```kotlin
package intro

// Top Level에 function 정의를 함.
fun main(args: Array<String>) {
    // if: expression
    val name = if (args.isNotEmpty()) args[0] else "Kotlin"
    println("Hello, $name!")
    println("Hello, ${args.getOrElse(0) { "Anonymous" }}")

    println("First ${foo()} - Second ${foo()}")
}

fun foo(): String {
    println("Calculating foo")
    return "foo"
}

fun max(a: Int, b: Int): Int = if (a > b) a else b
```

JavaMain.java:
```java
public class JavaMain {

    public static void main(String[] args) {
        String result = Main.foo(); // top-level에 정의된 kotlin function을 Java에서 파일명의 클래스가 갖고 있는 static method처럼 호출함.
        System.out.println(result);
    }
}
```

클래스명 바꾸려면 **JvmName** 사용한다.
```kotlin
@file: JvmName("KotlinUtil")
package intro

// Top Level에 function 정의를 함.
fun main(args: Array<String>) {
    // if: expression
    val name = if (args.isNotEmpty()) args[0] else "Kotlin"
    println("Hello, $name!")
    println("Hello, ${args.getOrElse(0) { "Anonymous" }}")

    println("First ${foo()} - Second ${foo()}")
}

fun foo(): String {
    println("Calculating foo")
    return "foo"
}
```

```java
public class JavaMain {

    public static void main(String[] args) {
        String result = KotlinUtil.foo();
        System.out.println(result);
    }
}
```

## Named Arguemnt, Default Value Arguemnt
### Java에서 Kotlin Default Value Argument 활용하는 방법
function 에 **@JvmOverloads** 사용하기
```kotlin
@JvmOverloads
fun displaySeparator(character: Char = '*', size: Int = 10): Unit {
    repeat(size) {
        print(character)
    }
}
```

```java
KotlinUtil.displaySeparator('$');

```
그렇다고, Java에서 `KotlinUtil.displaySeparator(size = 3);` 이런 식으로 사용은 불가능하다.

JVM에서 4가지(2^2) 종류의 function이 만들어진다.
```java
public static void displaySeparator(char character, int size);
public static void displaySeparator(char character);
public static void displaySeparator(int size);
public static void displaySeparator();
```

## if, when clause condition
```kotlin
fun responseToInput(input: String) = when (input) {
    "y", "yes" -> "I'm glad you agree."
    "n", "no" -> "Sorry to hear that."
    else -> "I don't understand you."
}

fun mix(c1: Color, c2: Color) = when (setOf(c1, c2)) {
    setOf(Color.RED, Color.YELLOW) -> Color.ORANGE
    setOf(Color.YELLOW, Color.BLUE) -> Color.GREEN
    setOf(Color.BLUE, Color.VIOLET) -> Color.INDIGO
    else -> throw Exception("Dirty Color")
}

sealed class Pet(open val name: String)

class Dog(override val name: String) : Pet(name) {
    fun woof() = "I'm $name. woof~"
}

class Cat(override val name: String) : Pet(name) {
    fun meow() = "I'm $name. meow~"
}


fun howling(pet: Pet) = when (pet) {
    is Dog -> pet.woof() // Smart Cast
    is Cat -> pet.meow()
}

fun getSound(): String = when (val pet = getMyFavoritePet()) {
    is Cat -> pet.meow()
    is Dog -> pet.woof()
    else -> "no cat, no dog"
}

fun getMyFavoritePet(): Pet {
    return Dog("V")
}

fun updateWeather(degrees: Int) {
    val (description, color) = when { // <- no argument
        degrees < 15 -> "cold" to Color.BLUE
        degrees < 25 -> "mild" to Color.ORANGE
        else -> "hot" to Color.RED
    }
}

```

## Loop
```kotlin
val memberOfBts = mapOf("V" to 27, "Jimin" to 27, "SUGA" to 29)

fun main() {
    for ((name, age) in memberOfBts) { // (key, value) pair
        println("$name is $age years old.")
    }

    for ((index, element) in listOf(1, 2, 3, 4, 5).withIndex()) { // withIndex
        println("[$index] = $element")
    }

    for (i in 100..110) { // including upperBound
        println(i)
    }

    for (i in 1 until 10) { // excluding upperBound
        println(i)
    }

    for (i in 100 downTo 90 step 3) {
        println(i)
    }

    for (c in '0' until  '9') {
        print(c)
    }
}
```

## in
- range

```kotlin
    println("Kotlin" in "Java" .. "Scala") // "Java".compareTo("Kotlin") <= 0 && "Kotlin".compareTo("Scala") <= 0
    println("Kotlin" in setOf("Java", "Scala"))

}

fun recognize(c: Char): String = when (c) {
    in '0'..'9' -> "It's a digit."
    in 'a'..'z', in 'A'..'Z' -> "It's a alphabet."
    else -> "I don't know."
}
```
- belongs to collection

```kotlin
if (element in list) { ... }
```


## Practice
```kotlin
fun main() {
   println(isValidIdentifier("name"))   // true
    println(isValidIdentifier("_name"))  // true
    println(isValidIdentifier("_12"))    // true
    println(isValidIdentifier(""))       // false
    println(isValidIdentifier("012"))    // false
    println(isValidIdentifier("no$"))    // false
}

fun isValidIdentifier(s: String): Boolean {
    fun isValidCharacter(ch: Char) = ch == '_' || ch.isLetterOrDigit()

    if (s.isEmpty() || s[0] in '0'..'9') return false
    for (ch in s) {
        if (!isValidCharacter(ch)) return false
    }

    return true
}
```

## Excpetion
Kotlin에서 정의한 function에서 checked exception으로 정의하여 호출하는 java에서 handling을 원할 때, **@Throws(XXXXException::class)** 을 추가해야 한다.
```kotlin
@Throws(IOException::class)
fun fnBar() {
    throw IOException("foo exception!!!")
}

fun fnFoo() {
    throw IOException("foo exception!!!")
}
```

```java
  try {
      ExceptionsKt.fnBar();
  } catch (IOException e) {
      e.printStackTrace();
  }

  ExceptionsKt.fnFoo(); // <- java에서는 exception을 안 던진다고 판단해서 try..catch로 묶으면 compile error 발생한다.
```

## Extension Functions
- 언제 사용하면 유용한지?
  - 외부 Library에 대해 기능을 확장하고 싶거나 Java Library를 확장하고 싶을 때. 다만 내가 확장을 해놨는데, 나중에 해당 Library에 동일한 Signature에 대해 메서드가 추가될 때, 내가 원하는대로 동작을 안 할 수 있는 문제는 존재한다.


```kotlin
package intro

// this가 Receiver가 된다.
//fun String.lastChar(): Char = this.get(this.length - 1)
// Receiver this를 생략할 수 있다.
fun String.lastChar(): Char = get(this.length - 1)

fun String.repeat(n: Int): String {
    val sb = StringBuilder(n * length)
    for (i in 1..n) {
        sb.append(this)
    }
    return sb.toString()
}

```

Java Class에서 호출할 때
```java
import intro.*; // Kotlin Top-Level에 정의된 Extension Function을 사용하기 위해서 명시적으롤 패키지를 import 해야 한다.
        
public class JavaMain {

    public static void main(String[] args) {
        
        char lastChar = ExtensionFunctionsKt.lastChar("Jimin");
        System.out.println(lastChar);

        String strings = ExtensionFunctionsKt.repeat("SUGA", 5);
        System.out.println(strings);
    }
}
```

## Example from the Standard Library
### Extension Function: **until**
  ```kotlin
    public infix fun Int.until(to: Int): IntRange {
        if (to <= Int.MIN_VALUE) return IntRange.EMPTY
        return this .. (to - 1).toInt()
    }
  ```
### Extension Function: **to**
  ```kotlin
  public infix fun <A, B> A.to(that: B): Pair<A, B> = Pair(this, that)
  ```

### Formatting Multiline Strings
```kotlin
val q1 = """To code,
    #or not to code?..""".trimMargin(marginPrefix = "#")
println(q1)

val q2 = """
    To code,
    or not to code?..
""".trimIndent()
println(q2)
```
Result:
```shell
To code,
or not to code?..
To code,
or not to code?..
```

```kotlin
val regex = """\d{3}.\d{3}.\d{3}""".toRegex()
println(regex.matches("127.0.1"))
println(regex.matches("127.111.123"))
```

### Conversion to Numbers
```kotlin
    // Conversion to Numbers
    println("123".toInt())
    println("1e-10".toDouble())

    println("Jimin".toIntOrNull())
```

### Extention Function: eq
```kotlin
{
   ...
    println("Jimin".toIntOrNull())

    getNumber() eq 27
    getNumber() eq 28
}

fun getNumber() = 27

infix fun <T> T.eq(other: T) {
    if (this == other) println("OK")
    else println("Error: $this != $other")
}
```

### Example: sum()
```kotlin
fun main() {
    val sum = listOf(1, 2, 3).sum()
    println(sum)    // 6
}

fun List<Int>.sum(): Int {
    var result = 0
    for (i in this) {
        result += i
    }
    return result
}
```

### Signature가 동일할 때는 member가 우선한다.
```kotlin
fun main() {
    val a = A()
    println(a.foo()) // signature가 동일할 때는 member가 우선한다.
    println(a.foo(1))
}

class A {
    fun foo() = "abcd"
}

fun A.foo() = "changed!"

fun A.foo(index: Int) = "$index"
```

Result:
```bash
abcd
1
```

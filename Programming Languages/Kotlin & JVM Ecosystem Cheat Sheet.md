# Kotlin & JVM Ecosystem Cheat Sheet

## Kotlin Basics

### Variables
```kotlin
val immutable = "read-only"        // Final variable
var mutable = "can change"         // Mutable variable
const val COMPILE_TIME_CONST = 42  // Compile-time constant

// Type inference
val name = "Kotlin"                // String inferred
val age = 25                       // Int inferred
val height: Double = 1.75          // Explicit type
```

### Basic Types
```kotlin
// Numbers
val int: Int = 42
val long: Long = 42L
val double: Double = 3.14
val float: Float = 3.14f

// Boolean
val isTrue: Boolean = true

// Character & String
val char: Char = 'A'
val str: String = "Hello"
val multiLine = """
    Multi-line
    string
""".trimIndent()
```

### Control Flow
```kotlin
// If-else (expression)
val max = if (a > b) a else b

// When expression
val description = when (x) {
    0, 1 -> "Binary digit"
    in 2..10 -> "Between 2 and 10"
    is String -> "Is string"
    else -> "Something else"
}

// For loop
for (i in 1..10) { }           // 1 to 10 inclusive
for (i in 1 until 10) { }      // 1 to 9
for (i in 10 downTo 1) { }     // 10 down to 1
for (i in 1..10 step 2) { }    // 1,3,5,7,9

// While loops
while (condition) { }
do { } while (condition)
```

## Functions

### Function Declaration
```kotlin
// Basic function
fun greet(name: String): String {
    return "Hello $name"
}

// Single-expression function
fun square(x: Int) = x * x

// Default arguments
fun connect(host: String, port: Int = 8080) { }

// Named arguments
connect(host = "localhost", port = 9090)

// Extension function
fun String.addExclamation() = this + "!"
```

### Higher-Order Functions & Lambdas
```kotlin
// Lambda syntax
val sum = { x: Int, y: Int -> x + y }
val square: (Int) -> Int = { it * it }

// Higher-order function
fun calculate(x: Int, y: Int, operation: (Int, Int) -> Int): Int {
    return operation(x, y)
}

// Usage
calculate(5, 3) { a, b -> a + b }

// Common higher-order functions
listOf(1, 2, 3).map { it * 2 }        // [2, 4, 6]
listOf(1, 2, 3).filter { it > 1 }     // [2, 3]
listOf(1, 2, 3).any { it > 2 }        // true
listOf(1, 2, 3).find { it > 1 }       // 2
```

## Object-Oriented Programming

### Classes & Constructors
```kotlin
// Primary constructor
class Person(val name: String, var age: Int) {
    init {
        // Initialization block
        println("Person created: $name")
    }
}

// Secondary constructor
class Person {
    var name: String = ""
    var age: Int = 0
    
    constructor(name: String) {
        this.name = name
    }
}

// Data class (automatically generates equals, hashCode, toString, copy)
data class User(val id: Long, val name: String, val email: String)

// Sealed class (restricted class hierarchies)
sealed class Result<out T> {
    data class Success<T>(val data: T) : Result<T>()
    data class Error(val exception: Exception) : Result<Nothing>()
}
```

### Properties & Visibility
```kotlin
class BankAccount {
    var balance: Double = 0.0
        private set    // Setter is private
    
    // Custom getter
    val isInDebt: Boolean
        get() = balance < 0
        
    // Late initialization
    lateinit var lateInitVar: String
}

// Visibility modifiers: public (default), private, protected, internal
```

### Inheritance & Interfaces
```kotlin
// Open class (can be inherited)
open class Animal(val name: String) {
    open fun makeSound() = "Some sound"
}

// Interface
interface Swimmable {
    fun swim() = println("Swimming")
}

// Inheritance
class Dog(name: String) : Animal(name), Swimmable {
    override fun makeSound() = "Woof!"
    
    // Overriding property
    override val speed: Int = 10
}

// Abstract class
abstract class Shape {
    abstract fun area(): Double
}
```

## Collections

### List, Set, Map
```kotlin
// Immutable collections
val list = listOf("a", "b", "c")
val set = setOf(1, 2, 3)
val map = mapOf(1 to "one", 2 to "two")

// Mutable collections
val mutableList = mutableListOf("a", "b")
val mutableSet = mutableSetOf(1, 2)
val mutableMap = mutableMapOf(1 to "one")

// Java interop
val javaList = ArrayList<String>()

// Building collections
val numbers = (1..10).toList()
```

### Collection Operations
```kotlin
val numbers = listOf(1, 2, 3, 4, 5)

// Transformation
numbers.map { it * 2 }                    // [2, 4, 6, 8, 10]
numbers.flatMap { listOf(it, it) }        // [1, 1, 2, 2, 3, 3, ...]
numbers.filter { it % 2 == 0 }            // [2, 4]

// Aggregation
numbers.reduce { acc, i -> acc + i }      // 15
numbers.fold(0) { acc, i -> acc + i }     // 15
numbers.sum()                             // 15

// Searching
numbers.find { it > 3 }                   // 4
numbers.first { it > 3 }                  // 4
numbers.any { it > 3 }                    // true
numbers.all { it > 0 }                    // true

// Sorting
numbers.sorted()                          // [1, 2, 3, 4, 5]
numbers.sortedDescending()                // [5, 4, 3, 2, 1]

// Grouping
numbers.groupBy { if (it % 2 == 0) "even" else "odd" }
// {odd=[1, 3, 5], even=[2, 4]}
```

## Null Safety

### Handling Nullability
```kotlin
// Nullable types
var nullableString: String? = null

// Safe calls
val length = nullableString?.length      // Returns null if nullableString is null

// Elvis operator
val length = nullableString?.length ?: 0 // Returns 0 if null

// Safe cast
val asString = nullableString as? String // Returns null if cast fails

// Not-null assertion (use carefully!)
val length = nullableString!!.length     // Throws NPE if null

// let, also, run, with, apply scope functions
nullableString?.let { 
    // Executes block only if not null
    println(it.length)
}
```

### Scope Functions
```kotlin
val person = Person("Alice", 25)

// let - context object as 'it', returns lambda result
val result = person.let { it.age + 5 }

// also - context object as 'it', returns object itself
person.also { println("Created: ${it.name}") }

// apply - context object as 'this', returns object itself
person.apply { 
    age = 30 
    name = "Bob"
}

// run - context object as 'this', returns lambda result
val ageAfterYears = person.run { age + 5 }

// with - not extension, context as 'this', returns lambda result
with(person) {
    println("$name is $age years old")
}
```

## Coroutines

### Basics
```kotlin
import kotlinx.coroutines.*

// Launching coroutines
fun main() = runBlocking { // Creates a coroutine scope
    launch { // Launches new coroutine
        delay(1000L) // Non-blocking delay
        println("World!")
    }
    println("Hello")
}

// Async/await
suspend fun fetchData(): String {
    delay(1000L)
    return "Data"
}

val result = async { fetchData() }.await()
// Or concurrently
val deferred1 = async { fetchData() }
val deferred2 = async { fetchAnotherData() }
val results = awaitAll(deferred1, deferred2)
```

### Coroutine Context & Dispatchers
```kotlin
// Different dispatchers
launch(Dispatchers.IO) { /* I/O operations */ }
launch(Dispatchers.Default) { /* CPU-intensive work */ }
launch(Dispatchers.Main) { /* UI updates (Android) */ }
launch(Dispatchers.Unconfined) { /* Not confined */ }

// With context
withContext(Dispatchers.IO) {
    // Perform I/O operation
}

// Exception handling
val handler = CoroutineExceptionHandler { _, exception ->
    println("Caught $exception")
}
launch(handler) {
    // Coroutine that might throw
}
```

## JVM Ecosystem

### Build Tools

#### Gradle (Kotlin DSL)
```kotlin
// build.gradle.kts
plugins {
    kotlin("jvm") version "1.9.0"
    application
}

repositories {
    mavenCentral()
}

dependencies {
    implementation(kotlin("stdlib"))
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.7.0")
    testImplementation(kotlin("test"))
}

application {
    mainClass.set("MainKt")
}

tasks.test {
    useJUnitPlatform()
}
```

#### Maven
```xml
<!-- pom.xml -->
<properties>
    <kotlin.version>1.9.0</kotlin.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.jetbrains.kotlin</groupId>
        <artifactId>kotlin-stdlib</artifactId>
        <version>${kotlin.version}</version>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-maven-plugin</artifactId>
            <version>${kotlin.version}</version>
            <executions>
                <execution>
                    <id>compile</id>
                    <phase>compile</phase>
                    <goals>
                        <goal>compile</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

### Testing

#### JUnit 5
```kotlin
import org.junit.jupiter.api.Test
import org.junit.jupiter.api.Assertions.*

class CalculatorTest {
    
    @Test
    fun `addition should work correctly`() {
        val calculator = Calculator()
        assertEquals(4, calculator.add(2, 2))
    }
    
    @Test
    fun `division by zero should throw exception`() {
        val calculator = Calculator()
        assertThrows<ArithmeticException> {
            calculator.divide(10, 0)
        }
    }
}
```

#### KotlinTest
```kotlin
import io.kotest.core.spec.style.StringSpec
import io.kotest.matchers.shouldBe

class CalculatorSpec : StringSpec({
    "addition should work correctly" {
        val calculator = Calculator()
        calculator.add(2, 2) shouldBe 4
    }
    
    "division by zero should throw exception" {
        val calculator = Calculator()
        shouldThrow<ArithmeticException> {
            calculator.divide(10, 0)
        }
    }
})
```

### Popular Libraries

#### Ktor (Web Framework)
```kotlin
import io.ktor.server.application.*
import io.ktor.server.engine.*
import io.ktor.server.netty.*
import io.ktor.server.response.*
import io.ktor.server.routing.*

fun main() {
    embeddedServer(Netty, port = 8080) {
        routing {
            get("/") {
                call.respondText("Hello Ktor!")
            }
            get("/user/{id}") {
                val id = call.parameters["id"]
                call.respondText("User $id")
            }
        }
    }.start(wait = true)
}
```

#### Exposed (SQL DSL)
```kotlin
// Define table
object Users : Table() {
    val id = integer("id").autoIncrement()
    val name = varchar("name", 50)
    override val primaryKey = PrimaryKey(id)
}

// Database operations
Database.connect("jdbc:h2:mem:test", driver = "org.h2.Driver")
transaction {
    // Create table
    SchemaUtils.create(Users)
    
    // Insert
    Users.insert { 
        it[name] = "Alice" 
    }
    
    // Query
    Users.selectAll().forEach { 
        println("${it[Users.id]}: ${it[Users.name]}")
    }
}
```

#### Koin (Dependency Injection)
```kotlin
// Define module
val appModule = module {
    single { DatabaseService() }
    factory { UserRepository(get()) }
    factory { UserService(get()) }
}

// Start Koin
fun main() {
    startKoin {
        modules(appModule)
    }
    
    // Get instance
    val userService = get<UserService>()
}
```

## Java Interoperability

### Calling Java from Kotlin
```kotlin
// Java class
public class JavaClass {
    private String name;
    
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}

// Kotlin usage
val javaObj = JavaClass()
javaObj.name = "Kotlin"  // Property syntax for getters/setters
println(javaObj.name)
```

### Calling Kotlin from Java
```kotlin
// Kotlin class
class KotlinClass(val name: String) {
    fun greet() = "Hello $name"
    
    @JvmField
    val constant = "CONSTANT"
    
    companion object {
        @JvmStatic
        fun create() = KotlinClass("Default")
    }
}
```

```java
// Java usage
public class Main {
    public static void main(String[] args) {
        KotlinClass obj = new KotlinClass("Java");
        System.out.println(obj.greet());
        
        // Access companion object function
        KotlinClass defaultObj = KotlinClass.Companion.create();
        // Or with @JvmStatic
        KotlinClass staticObj = KotlinClass.create();
        
        // Access @JvmField
        String constant = obj.constant;
    }
}
```

## Common Annotations

### JVM Annotations
```kotlin
@JvmName("customName")          // Change generated Java name
@JvmOverloads                   // Generate overloads for default parameters
@JvmField                       // Expose as field instead of getter/setter
@JvmStatic                      // Generate static method in companion
@Throws(IOException::class)     // Declare checked exceptions
@JvmWildcard                    // Affect generic type mapping
@JvmSuppressWildcards           // Affect generic type mapping
```

## Memory Management & Performance

### Basic Patterns
```kotlin
// Use sequences for lazy evaluation of large collections
val largeList = (1..1_000_000).toList()
val result = largeList
    .asSequence()           // Convert to sequence
    .filter { it % 2 == 0 }
    .map { it * 2 }
    .take(10)
    .toList()

// Use inline functions for higher-order functions to avoid lambda overhead
inline fun <T> measureTime(block: () -> T): T {
    val start = System.currentTimeMillis()
    val result = block()
    val end = System.currentTimeMillis()
    println("Time: ${end - start}ms")
    return result
}
```

This cheat sheet covers the essential aspects of Kotlin and the JVM ecosystem. Keep it handy for quick reference while developing!
## Introduction to the Series

"Effective Java" by Joshua Bloch helped me in writing robust, efficient, and maintainable Java code. I've recently picked up Kotlin, I'm excited to explore how the principles and best practices outlined in this book can be applied to kotlin. In this series, I'll be summarizing each chapter and item, providing Java and Kotlin implementations where relevant, to help me solidify my understanding of both languages.

## Chapter 2: Creating and Destroying Objects
*   [Item 1: Static Factory Methods](#item-1): Static factory methods provide more flexibility and readability than constructors.
*   [Item 2: Builder Pattern](#item-2): The builder pattern is useful when dealing with many constructor parameters.
*   [Item 3: Singleton Property](#item-3): Implementing singletons using private constructors or enum types.
*   [Item 4: Noninstantiability](#item-4): Enforcing noninstantiability using private constructors.
*   [Item 5: Dependency Injection](#item-5): Prefer dependency injection to hardwiring resources.

### <a id="item-1"></a> Item 1: Consider Static Factory Methods Instead of Constructors


#### Summary

Static factory methods provide an alternative to constructors for creating objects. They offer several benefits, including meaningful method names, flexibility, and the ability to return objects of any subclass.

#### Java Implementation
```java
public class Color {
    private final int value;

    private Color(int value) {
        this.value = value;
    }

    public static Color ofValue(int value) {
        return new Color(value);
    }

    public static Color ofRGB(int r, int g, int b) {
        return new Color((r << 16) | (g << 8) | b);
    }
}
```

#### Kotlin Implementation
```kotlin
class Color private constructor(val value: Int) {
    companion object {
        fun ofValue(value: Int) = Color(value)
        fun ofRGB(r: Int, g: Int, b: Int) = Color((r shl 16) or (g shl 8) or b)
    }
}
```
In Kotlin, the `companion object` is used to define static members or functions that belong to the class itself, rather than instances of the class. It's similar to Java's static members, but with more flexibility. In this example, the `companion object` allows us to define factory methods `ofValue` and `ofRGB` that can be called without creating an instance of the `Color` class.
By using a `companion object`, we can achieve similar functionality to Java's static factory methods, while also taking advantage of Kotlin's concise syntax and features.

### <a id="item-2"></a> Item 2: Consider a Builder When Faced with Many Constructor Parameters

#### Summary
When a class has many constructor parameters, it can be difficult to write and read client code. The builder pattern provides a more flexible and readable alternative.

#### Benefits

Readability: Client code is more readable, as the purpose of each parameter is clear.
Flexibility: Optional parameters can be omitted or specified in any order.
Immutability: The built object can be immutable, providing thread-safety benefits.

#### Java Implementation
```java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        private final int servingSize;
        private final int servings;
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }

        public Builder sodium(int val) {
            sodium = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}

// client code
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
        .calories(100)
        .sodium(35)
        .carbohydrate(27)
        .build();
```

#### Kotlin Implementation
```kotlin
class NutritionFacts(
    val servingSize: Int,
    val servings: Int,
    val calories: Int = 0,
    val fat: Int = 0,
    val sodium: Int = 0,
    val carbohydrate: Int = 0
)

// Client code
val cocaCola = NutritionFacts(
    servingSize = 240,
    servings = 8,
    calories = 100,
    sodium = 35,
    carbohydrate = 27
)
```

In Kotlin, we can leverage default parameter values to simplify the creation of objects with many parameters. This approach eliminates the need for a separate builder class.

### <a id="item-3"></a>Item 3: Enforce the Singleton Property with a Private Constructor or an Enum Type

#### Summary
A singleton is a class designed to have only one instance per JVM, providing a single global point of access. Enforcing this correctly avoids issues like duplicate instances with inconsistent state.

#### Private Constructor Approach
```java
public class DatabaseConnection {
    public static final DatabaseConnection INSTANCE = new DatabaseConnection();

    private DatabaseConnection() {}

    public void connect() {
        System.out.println("Connected to database...");
    }
}
```
In this approach, the private constructor ensures that the class cannot be instantiated from outside. The INSTANCE field provides global access to the singleton instance.
#### Enum Approach
```java
public enum DatabaseConnection {
    INSTANCE;

    public void connected() {
        System.out.println("Connected to database...");
    }
}
```
In this approach, the enum type ensures that only one instance is created. The INSTANCE field is implicitly public and provides global access to the singleton instance.
#### Benefits
Both approaches provide several benefits, including:
- **Thread-safety:** Both approaches ensure that the singleton instance is thread-safe.
- **Serialization safety:** The enum approach provides serialization safety, ensuring that the singleton property is preserved even after serialization and deserialization.

#### Client Code
```java
public class Main {
    public static void main(String[] args) {
        DatabaseConnection dbConn = DatabaseConnection.INSTANCE;
        dbConn.connect();
    }
}
```
In this example, we access the singleton instance using the INSTANCE field and call the sing() method.
#### Kotlin Implementation
```kotlin
object DatabaseConnection {
    fun connect() {
        println("Connected to database...")
    }
}

// Client code
fun main() {
    DatabaseConnection.connect()
}
```
In Kotlin, we can use the `object` keyword to define a singleton. The `object` declaration ensures that only one instance is created, and provides global access to the singleton instance.
By using a private constructor or an enum type (or Kotlin's `object` declaration), you can enforce the singleton property and ensure that your class is instantiated exactly once.

### <a id="item-4"></a>Item 4: Enforce Noninstantiability with a Private Constructor

#### Summary
Some classes, such as utility classes, are designed to be used statically and should not be instantiated. To enforce noninstantiability, you can use a private constructor.

#### Example
```java
public class UtilityClass {
    // Suppress default constructor for noninstantiability
    private UtilityClass() {
        throw new AssertionError();
    }

    public static void utilityMethod() {
        System.out.println("Utility method...");
    }
}
```
In this example, the private constructor ensures that the class cannot be instantiated from outside. The AssertionError thrown in the constructor makes it clear that instantiation is not intended.

#### Benefits
Using a private constructor to enforce noninstantiability provides several benefits, including:
- **Clear intent:** The private constructor clearly communicates that the class is not intended to be instantiated.
 - **Prevents instantiation:** The private constructor prevents accidental instantiation of the class.

#### Best Practices
When enforcing noninstantiability, consider the following best practices:
 - **Make the constructor private:** Ensure that the constructor is private to prevent instantiation.
 - **Throw an AssertionError:** Throwing an AssertionError in the constructor makes it clear that instantiation is not intended.
####Kotlin Implementation
```kotlin
class UtilityClass private constructor() {
    companion object {
        fun utilityMethod() {
            println("Utility method...")
        }
    }
}

// or

object UtilityClass {
    fun utilityMethod() {
        println("Utility method...")
    }
}
```
In Kotlin, you can enforce noninstantiability of a class either by declaring a private constructor or by using an object declaration. A private constructor restricts external code from creating instances of the class, which is useful for utility classes or any class that should not be instantiated. This approach explicitly indicates the intent that instantiation is disallowed. On the other hand, an object declaration defines a singleton class at the language level, guaranteeing a single instance without allowing any constructor calls. This is the preferred idiomatic way in Kotlin to enforce a single instance while preventing any instantiation.
Both approaches convey clear intent and ensure controlled access to the class instances, with the object declaration offering built-in thread safety and simpler syntax.

### <a id="item-5"></a>Item 5: Prefer Dependency Injection to Hardwiring Resources

#### Summary
Dependency injection is a design pattern that allows components to be loosely coupled, making it easier to test, maintain, and extend the system. Instead of hardwiring resources, prefer dependency injection to provide dependencies to a class.

#### Hardwiring Resources
```java
public class Lexicon {
    private static final Dictionary dictionary = new MerriamWebster();

    public Lexicon() {}

    public void lookup(String word) {
        dictionary.define(word);
    }
}
```
In this example, the Lexicon class is tightly coupled to the MerriamWebster dictionary. This makes it difficult to test or use the Lexicon class with a different dictionary.

#### Dependency Injection
```java
public class Lexicon {
    private final Dictionary dictionary;

    public Lexicon(Dictionary dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }

    public void lookup(String word) {
        dictionary.define(word);
    }
}
```

In this example, the Lexicon class is decoupled from the specific dictionary implementation. Instead, it depends on the Dictionary interface, which can be implemented by different dictionaries.

#### Benefits
Dependency injection provides several benefits, including:
- **Loose coupling:** Components are decoupled, making it easier to test, maintain, and extend the system.
- **Testability:** Components can be tested in isolation using mock dependencies.
- **Flexibility:** Components can be used with different dependencies, making it easier to adapt to changing requirements.

#### Best Practices
When using dependency injection, consider the following best practices:
- **Use interfaces:** Define interfaces for dependencies to decouple components from specific implementations.
- **Use constructor injection:** Inject dependencies through constructors to ensure that components are properly initialized.

#### Kotlin Implementation
```kotlin
class Lexicon(private val dictionary: Dictionary) {
    fun lookup(word: String) {
        dictionary.define(word)
    }
}

interface Dictionary {
    fun define(word: String)
}

class MerriamWebster : Dictionary {
    override fun define(word: String) {
        println("Defining $word...")
    }
}
```
In Kotlin, you can use dependency injection in a similar way to Java.
By preferring dependency injection to hardwiring resources, you can write more flexible, testable, and maintainable code.

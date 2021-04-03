<p>
      <h1 style="color:#990000" align="center">Java 8 Features</h1>
      <p style="text-align: center;">Notes</p>
      <p align="center">
      <a href="https://github.com/jtsampson/notes/java">
        <img src="java-8-features.png" width=193 height=206>
      </a>
      </p>
      <hr>


<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Release Date](#release-date)
- [Java forEach loop](#java-foreach-loop)
- [Interfaces: default and static methods](#interfaces-default-and-static-methods)
  - [Keyword: `default`](#keyword-default)
  - [Keyword: `static`](#keyword-static)
  - [Interfaces](#interfaces)
- [Functional Interfaces](#functional-interfaces)
- [Lambda Expressions](#lambda-expressions)
- [Method references](#method-references)
  - [Reference Types](#reference-types)
- [Stream API](#stream-api)
  - [Creating Streams](#creating-streams)
  - [Multithreading Streams](#multithreading-streams)
  - [Stream Operations](#stream-operations)
    - [Iterating](#iterating)
    - [Filtering](#filtering)
    - [Mapping](#mapping)
    - [Matching](#matching)
    - [Reduction](#reduction)
  - [Collecting](#collecting)
- [Base64 Encode/Decode](#base64-encodedecode)
- [Optional Class](#optional-class)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Release Date
 
March 8, 2014 

## Java forEach loop

Defined in the `Iterable` and `Stream` interfaces
 
Method Signature:
```java
default void forEach(Consumer<super T> action)
```

Example:
```java
import java.util.ArrayList;  
import java.util.List;  
public class ForEachExample {  
    public static void main(String[] args) {  
        List<String> sports = new ArrayList<String>();
        sports.add("Football");
        sports.add("Baseball");
        sports.add("Soccer");
        sports.add("Hockey");
        // Iterating by passing lambda expression
        sports.out.println("---- Sports ----");  
        sports.forEach(games -> System.out.println(sport));  
          
    }  
}  
```
## Interfaces: default and static methods

From Java 8 on,  interfaces may have `default` or `static` methods: these methods can have 
implementations!. Default methods allows developers to add a new methods to existing interface
and provide an implementation for them without breaking the existing implementations of the interface.

### Keyword: `default`

If you add the new `default` keyword to the method signature in the  interface and
add an implementation, the implementing classes will get the new behavior. To override in an implementation,
override the method w/o the `default` keyword. 

### Keyword: `static`

Static methods are similar to default methods, but since they are  `static`, they cannot be 
overridden in an implementing class. Static methods in interfaces should be called with the interface name (e.g.
`Interface.staticMethodName` ). Static methods in Interfaces provide security by not allowing implementing classes to
override the implementation. They are good for providing utility methods (like null checking).

Static Methods can be overloaded, but not overridden.

Example:

```java
interface AquaticAnimal {
    // normal interface 
    void sayHi();

    // static method cannot be overridden by implementor
    static void habitat() {
        System.out.println("Lives in Water");
    }

    // default method - can be overridden by implementor
    default void eats() {
        System.out.println("Eats water stuff");
    }
}

class Frog implements AquaticAnimal {
    @java.lang.Override
    public void sayHi() {
        System.out.println("Ribbit");
    }
}

class Toad implements AquaticAnimal {
    @java.lang.Override
    public void sayHi() {
        System.out.println("Croak");
    }
    
    @java.lang.Override
    public void eats() {
        System.out.println("Eats worms"); 
    }
}
```
### Interfaces

As of Java 8, Interfaces can have:

- Abstract Methods
- Constant Variables
- Default Methods (Added in Java 8)
- Static Methods (Added in Java 8)
- Note: Java 9 adds more

## Functional Interfaces

A functional interface has exactly one abstract method. Lambda expressions need functional interfaces.
If you create an interface class with exactly one abstract method, it is, by definition, a functional interface. However,
any developer can come along and add another abstract method, and the compiler will then not know  which abstract method
should be used in the context of the lambda. We can use the `@FunctionalInterface` to mark the interface as functional
to prevent another developer from adding more abstract methods.

Example:
```java
@FunctionalInterface
public interface Greeting {
    void perform();
    // you will get an error if you try to add another abstract method
}
```

## Lambda Expressions

Lambda expression helps us to write anonymous methods. They provide a clear and concise way to implement a 
SAM interface(Single Abstract Method) by using an expression. They are very useful in the collection library where 
they help to iterate, filter and extract data. Lambda Expressions:

  - allow us to write easier-to-use apis 
  - enable support for parallel processing 
  - can result in some very nice concise code
  - allow us to provide implementations of functional interfaces

What if you want to have the behavior passed as an argument. In OOP you can do this by creating a method that accepts
class that implements and an interface, and calls the interface method. However, what if you want to just pass an action 
without creating a class that implements and action? Answer: lambda expressions

Lambda Expressions allow you to create functions that can be treated as values. This means you can assign a block of 
code to a variable. 

Syntax:

```java
// parameter -> expression

// (parameter1, parameter2) -> expression

// (parameter1, parameter2) -> { code block}}

// 
```

## Method references

Method references are used to refer to the method of a functional interface. It is compact and easy form of lambda 
expression. Each time when you are using lambda expression to just referring a method, you can replace your 
lambda expression with method reference.

Example: Suppose you want to sort your fruit, in Java 7 we may have:

```java
Fruit[] fruitArray = basket.toArray(new Fruit[basket.size()]);

class FruitComparator implements Comparator<Fruit> {
  public int compare(Fruit a, Gruit b) {
    return a.getName().compareTo(b.getName());
  }
}
        
Arrays.sort(rfruitAsArray, new FruitComparator());
```

Notice that the Arrays.sort signature is `static <T> void sort(T[] a, Comparator<? super T> c)`, and that
Comparator is a functional interface, so we can rewrite this as...

```java
Arrays.sort(fruitArray, 
  (Fruit a, Fruit b) -> {
     return a.getName().compareTo(b.getName());
  });
```

However, we already have an implementation of `public static int Fruit.compareByName(a,b)` so we can call it directly 
using method references.

```java
Arrays.sort(fruitArray, Fruit::compareByName);
```

### Reference Types

| Type | Example
|:------|:--------
| Reference to a static method | `ContainingClass::staticMethod`
| Reference to an instance method of a particular object | `containingObject::instanceMethodName`
| Reference to an instance method of an arbitrary object of a particular type | `containingType::methodName`
| Reference to a constructor | `ClassName::new`

Examples: 

```java
// Reference to a static method 
Arrays.sort(fruitArray, Fruit::compareByName);

// Reference to an instance method of a particular object
Arrays.sort(rosterAsArray, myComparisonProvider::compareByName);

// Reference to an instance method of an arbitrary object of a particular type
String[] stringArray = {"z", "y", "x" };  
Arrays.sort(stringArray, String::compareToIgnoreCase);

// Reference to a constructor (it can be inferred, but if you want to spe
Set<Person> rosterSet = transferElements(roster, HashSet::new);
// The compiler infers hashset in this instance, if you want to specify you can do...
        Set<Fruit> fruitSet = transferElements(basket, HashSet<Fruit>::new);
```

## Stream API

The `java.util.stream` API contains classes for processing sequences of elements. The central class is `Stream<T>`.

### Creating Streams

Create a stream with `.stream()` or `.of()`. A `.stream()` default method has been added to the `Collection` interface 
and allow creating a `Stream<T>` using any collection as a source. Example:

```java
String arr = new String[]{"x", "y", "z"};
Stream<String> stream = Arrays.stream(arr);
stream = Stream.of("x", "y", "z");
```

### Multithreading Streams

Streams can be multithreaded with the use of `.parrallelStream`. Example:

```java
// peeling fruit in parallel
list.parrallelStream().forEach(fruit -> peal(fruit))
```
### Stream Operations

#### Iterating

Streams can reduce the code necessary for iterating over collections:
```java
for (String string : list){
    if (string.contains("stop")){
        return ;
    }
}

// in java 8
boolean stop = list.stream().anyMatch(item -> item.contains("stop"));
```

#### Filtering

The `'filter()` method allow us to pick a stream of elements that satisfy a predicate.
```java
ArrayList<String> basket = new ArrayList<>();
list.add("Apple");
list.add("Orange");
list.add("Banana");
list.add("Avacado");
// find fruit that starts with 
Stream<String> stream = basket.stream().filter(fruit -> fruit.startsWith("a"));
```

#### Mapping
The `.map()` method allows us to apply a function to each element of a collection and collect it into a new stream.
```java
// Transform a list of type String into a list of Path
List<String> uris = new ArrayList<>();
uris.add("C:\\My.txt");
Stream<Path> stream = uris.stream().map(uri -> Paths.get(uri));
```
The `.flatMap()` method allows us to obtain a stream from the child elements of a collection of parents.
```java
// Create a list of all children's toys
List<Parent> parents = new ArrayList<>();
parents.add(new Child("Tom"));
Stream<String> stream
  = parents.stream().flatMap(child -> child.getToys().stream());
```
#### Matching
The `anyMatch(), allMatch(), noneMatch()` methods allow us to match elements of streams.
```java
boolean isValid = list.stream().anyMatch(element -> element.contains("h")); // true
boolean isValidOne = list.stream().allMatch(element -> element.contains("h")); // false
boolean isValidTwo = list.stream().noneMatch(element -> element.contains("h")); // false

//Empty Stream
Stream.empty().allMatch(Objects::nonNull); // true
Stream.empty().anyMatch(Objects::nonNull); // false
```

#### Reduction
The `.reduce()` method allows us to reduce a stream to a single value with an accumulator (BinaryOperator) function
```java
  int[] numbers = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
  // 1st argument, init value = 0
  int sum = Arrays.stream(numbers).reduce(0, (a, b) -> a + b);
  System.out.println("sum : " + sum); // 55
        
  // if no initial value provided, the result is Optional<T>
  Optional<Integer> sum2 = Arrays.stream(numbers).reduce((a, b) -> a + b);
  if (sum2.isPresent()){
        System.out.println("sum2 : " + sum2); // 55
  }
  
```
### Collecting
The `.collect()` method and the `Collectors` class allow us to collect (or reduce) Steams to Collections.
```java
List<String> normalizedElements = list.stream()
        .map(element -> element.toLoweweCase())
        .collect(Collectors.toList());
```

## Base64 Encode/Decode

The `Base64` class to allow us to encode/decode w/o a third party libraries. Types:
  - Simple encoding/decoding
  - URL encoding/decoding
  - MIME encoding/decoding

## Optional Class

The `Optional` class is a container object which may or may not contain a non-null value. 
 - if `isPresent() == true` then `get()` will return a value
 - if `isPresent() == false` then `get()` wil return null
 - The `orElse()` can be used to return a default value.
> Per Javadocs: This is a value-based class; use of identity-sensitive operations (including reference equality (==), 
  identity hash code, or synchronization) on instances of Optional may have unpredictable results and should be avoided.

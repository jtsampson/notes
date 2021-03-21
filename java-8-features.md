<p align="center">
      <h1 style="color:#990000" align="center">Java 8 Features</h1>
      <p align="center">
              Things I'd like to keep track of.
            </p>
      <p align="center">
      <a href="https://github.com/jtsampson/notes">
        <img src="java-8-features.png" width=193 height=206>
      </a>
      </p>
      <hr>
</p>

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Java 8 Features](#java-8-features)
  - [Java forEach loop](#java-foreach-loop)
  - [Interfaces: default and static methods](#interfaces-default-and-static-methods)
  - [Functional Interfaces](#functional-interfaces)
  - [Lambda Expressions](#lambda-expressions)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->




# Java 8 Features

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

If you add the new `default` keyword to the method signature in the  interface and
add an implementation, the implementing classes will get the new behavior. If you need to override this, 
in a specific implementation, simply override the method w/o the `default` keyword. 

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

## Functional Interfaces

A functional interface has exactly one abstract method. Lambda expressions use functional interfaces.
If someone comes along and adds another abstract method that class, the compiler will not know  which abstract method
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

Lambda Expressions 
are shot blocks of code which take in parameters and return a value. They do not need a name, and they can be 
implemented in the body of a method. They enable functional programming in Java (as apposed to using OOP). They allow 
us to write easier-to-use apis and enable support for parallel processing (and also lets us write nice concise code)

Problem: everything is an object, cannot have code that is not associated with an object. In OOP you are always 
thinking in nouns. If you just need some code and don't want to associate it with a class. 

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




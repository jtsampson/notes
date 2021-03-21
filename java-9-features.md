<p align="center">
      <h1 style="color:#990000" align="center">Java 9 Features</h1>
      <p align="center">
              Things I'd like to keep track of.
            </p>
      <p align="center">
      <a href="https://github.com/jtsampson/notes">
        <img src="java-9-features.jpg" width=193 height=206>
      </a>
      </p>
      <hr>
</p>

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

  - [Jigsaw Project](#jigsaw-project)
  - [Process API](#process-api)
  - [Interface Private Methods](#interface-private-methods)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



## Jigsaw Project

The Jigsaw project allows us to provide the JDK in modules.

Modules are defined in a file called `modules-info.java`.

```json
module com.sampson.java9.modules.bike {
    requires com.sampson.java9.modules.wheels;
    exports com.sampson.java9.modules.riding;
}
```
Our module `bike` requires `wheels` for riding

## Process API

## Interface Private Methods
Interface methods can now be marked `private`

As of Java 9, Interfaces can have:

 - Abstract Methods
 - Constant Variables
 - Default Methods (Added in Java 8)
 - Static Methods (Added in Java 8)
 - Private Methods (Added in Java 9)
 - Private Static Methods (Added in Java 9)

Key Points:

 - **Interface private methods** must have implementation body; they can’t be declared as abstract methods.
 - A **static method** can call only private static method, but a default method can call both the static and non-static(instance) private methods.
 - The **interface private methods** support sharing common code between non-abstract methods(default, static and private).

```java
interface Interface {
    private static String staticPrivateHello(){
        return "Hello from the Class Private!";
    }
    private instancePrivateHello(){
        return "Hello from the Instance Private!";
    }
    default void hello(){
        String staticPrivate = staticPrivateHello();
        Interface i = new Interface() {
            // anonymous class
        };
        instancePrivate = i.instanceinstancePrivateHello();
        System.out.println(staticPrivate);
        System.out.println(instancePrivate);
    }
    
}
```
## JShell Command Line Tool

An interactive tool to evaluate Java statements and expressions. Useful for testing small code snippets.

```shell
C:\jtsampson2000\cust\jsampson\notes>JSHELL
|  Welcome to JShell -- Version 16
|  For an introduction type: /help intro


jshell> /help intro
|
|                                   intro
|                                   =====
|
|  The jshell tool allows you to execute Java code, getting immediate results.
|  You can enter a Java definition (variable, method, class, etc), like:  int x = 8
|  or a Java expression, like:  x + x
|  or a Java statement or import.
|  These little chunks of Java code are called 'snippets'.
|
|  There are also the jshell tool commands that allow you to understand and
|  control what you are doing, like:  /list
|
|  For a list of commands: /help
```

## New JCMD utility sub commands

The jcmd utility is used to send diagnostic command requests to the JVM. It must be used on the same machine on which 
the JVM is running, and have the same effective user and group identifiers that were used to launch the JVM. Each 
diagnostic command has its own set of arguments.

The utility must be used on the same machine that the JVM is running on. The PID number of the JVM is the first argument to the utility.

 - The `VM.class_hierchy` subcommand will give the hierarchy of a class in the running JVM 
 - The `VM.set_vmflag`  subcommand will modify some JVM parameters w/o restarting the JVM process
 - The `VM.falgs -all` subcommand will find all available flags
```text
C:\notes>"C:\jdk-16\bin\jcmd" 20512 VM.class_hierarchy -i -s java.net.Socket
20512:
java.lang.Object/null
|--java.net.Socket/null
|  implements java.io.Closeable/null (declared intf)
|  implements java.lang.AutoCloseable/null (inherited intf)

```

## Variable Handles

 TODO: FOLLOW UP, NOT SURE I UNDERSTAND THESE.

Generally, a variable handle is just a typed reference to a variable. The variable can be an array element, instance, 
or static field of the class.

The goal of VarHandle is to define a standard for invoking equivalents of `java`.util.concurrent.atomic` and 
`sun.misc.Unsafe` operations on fields and array elements.

VarHandles are immutable and have no visible state: they cannot be sub-classed.

Each VarHandle has :

 - a generic type T, which is the type of every variable represented by this VarHandle
 - a list of coordinate types CT, which are types of coordinate expressions, that allow locating variable referenced by this VarHandle
   - The list of coordinate types may be empty.


## Publish-Subscribe Framework

The class `java.util.concurrent.Flow` provides interfaces that support the Reactive Streams publish-subscribe framework. 

Reactive Streams is an initiative to provide a standard for asynchronous stream processing with non-blocking back 
pressure. This encompasses efforts aimed at runtime environments (JVM and JavaScript) as well as network protocols.
These interfaces support interoperability across a number of asynchronous systems running on JVMs and JavaSriprt.

See: [http://www.reactive-streams.org/](http://www.reactive-streams.org/)


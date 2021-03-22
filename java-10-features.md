<p align="center">
      <h1 style="color:#990000" align="center">Java 10 Features</h1>
      <p align="center">
              Things I'd like to keep track of.
            </p>
      <p align="center">
      <a href="https://github.com/jtsampson/notes">
        <img src="java-10-features.png" width=193 height=206>
      </a>
      </p>
      <hr>
</p>
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Java 10 Features](#java-10-features)
    - [Released](#released)
    - [Oracle JDK vs Open JDK](#oracle-jdk-vs-open-jdk)
        - [Oracle JDK Release Cycle](#oracle-jdk-release-cycle)
        - [OpenJDK Release Cycle](#openjdk-release-cycle)
    - [Local Variable Type Inference (JEP 286)](#local-variable-type-inference-jep-286)
    - [Unmodifiable Collections Changes](#unmodifiable-collections-changes)
        - [copyOf()](#copyof)
        - [toUnmodifiable()](#tounmodifiable)
    - [Optional Changes](#optional-changes)
    - [Container Awareness](#container-awareness)
    - [Application Class Data Sharing](#application-class-data-sharing)
    - [Root Certificates (JEP 319)](#root-certificates-jep-319)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Released
March 20, 2018.

## Oracle JDK vs Open JDK

Oracle and the Java Community are now promoting the OpenJDK binaries as the primary JDK going forward. Oracle will
continue to produce their JDK, but only for long term support (LTS) release.
OpenJDK binaries can be distributed as a part of a container (whereas the Oracle JDK cannot be).

### Oracle JDK Release Cycle

- Oracle JDK Binaries will be release every three years (LTS versions)

### OpenJDK Release Cycle

Starting with Java 10, Oracle has implemented a time based release schedule for Java.

 - A new Java is released every 6 months.
 - Support for the feature release will last only 6 months
 - Long-term release support versions (marked as LTS) will last for 3 years
 - The command `java -version` now outputs the GA (General Availability date) 
 - Note: Java 11 will be a LTS release. 
 - Version Number: `$FEATURE.$INTERIM.$UPDATE.$PATCH`
   - `$FEATURE` = Counter, currently 10, then 11 for `JDK 10` and `JDK 11`, etc.
   - `$INTERIM` = Counter, 0 based. Incremented for non feature release that contains backward compatible changes
   - `$UPDATE` = Counter, 0 based. Increments for security issue fixes. Updated one month from initial release and then every 3 months after.
   - `$PATCH` = Counter, 0 based. Increments for emergency fixes for critical issues.

## Local Variable Type Inference ([JEP 286](https://openjdk.java.net/jeps/286))

Begining with Java 10 you can mark local variables (with initializers) using `var` instead of the actual type.
The compiler will infer the proper type for you. The type is set at compile time and cannot be changed later. There is
no overhead for `var` (which is a type and not a keyword).

> A general rule is to not use var when the return type is not obvious as it might make code less readable.
See [Local Variable Type Inference: Style Guidelines](https://openjdk.java.net/projects/amber/LVTIstyle.html)

```java
// Pre Java 10 syntax
String java09 = "Using Java 9";
Map<Integer, String> java09Map = new Hashmap<>();

// Post Java 10 syntax
var java10 = "Using Java 10";
var java10Map = new Hashmap<Integer, String>();

assertTrue(java09 instanceoof String);
assertTrue(java10 intanceof String);
/* 
 * <pre>
 * {@code
 *   //These will not work
 *   var n;               // error: cannot use var w/o initializer
 *   var n = null         // error: variable initializer is null
 *   var a = { 1,2,3,4};  // error: needs explicit target type
 *   public var = "hello" // error: can't use non-local variables
 *   var p -> (String s) s.length() < 10; // error: won't work, lambda need types
 * }
 * </pre>
*/
```

## Unmodifiable Collections Changes

### copyOf()

A new static method, `.copyOf(Collection)` was added to `List, Map and Set`. Any attempt to modify such a Collection
will result in a `java.lang.UnsupportedOperationException` runtime exception.

### toUnmodifiable()

Additional methods added to `java.util.stream.Collectors` to collect a Stream into an unmodifiable `List`, `Map` or `Set`
Any attempt to modify such a Collection will result in a `java.lang.UnsupportedOperationException` runtime exception.

## Optional Changes

In Java 10, `java.util.Optional` got a new method:
```java
public T orElseThrow()
```
This was to avoid confusion using the `.get()` method which throws a `NoSuchElementException` if the value is not
present.  Usually getters usually do not throw exceptions, so going forward the preferred `.orElseThrow()`, and `.get()`
may be deprecated.

## Container Awareness

The JVM is now aware that it is running in a container and will query the container, rather than the host OS, for the 
number of available processors. You can now also attach to a Java process running in a container, which makes debugging 
easier.

The container must first be configured to use a subset of the host operating system’s CPUs, and then JVM will be able
to determine the number of CPUs in use. One can also use the `-XX:ActiveProcessorCount` flag to explicitly specify the
number of processors the containerized JVM is able to see. 

## Application Class Data Sharing

Application Class data sharing has been expanded from system classes to all available classes. On first run the JVM
will collect data about the classes it has loaded. It then makes this data available to other JVMs and subsequent
runs of that JVM, saving time and resources in the JVM initialization process.

## Root Certificates ([JEP 319](https://openjdk.java.net/jeps/319))

Java 10 Provides a default set of root Certification Authority (CA) certificates in the JDK.

This Open-sources the root certificates in Oracle's Java SE Root CA program in order to make OpenJDK builds more 
attractive to developers, and also to reduce the differences between OpenJDK builds and Oracle JDK builds.

Going forward, the `cacerts` keystore will be populated with a set of root certificates issued by the CAs of Oracle's 
Java SE Root CA Program.


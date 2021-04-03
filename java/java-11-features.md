<p align="center">
      <h1 style="color:#990000" align="center">Java 11 Features</h1>
      <p align="center">Notes</p>
      <p align="center">
      <a href="https://github.com/jtsampson/notes/java">
        <img src="java-11-features.jpg" width=193 height=206>
      </a>
      </p>
      <hr>


<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [JEP 181: Nest-Based Access Control](#jep-181-nest-based-access-control)
- [JEP 309: Dynamic Class-File Constants](#jep-309-dynamic-class-file-constants)
- [JEP 315: Improve Aarch64 Intrinsics](#jep-315-improve-aarch64-intrinsics)
- [JEP 318: Epsilon: A No-Op Garbage Collector (Experimental)](#jep-318-epsilon-a-no-op-garbage-collector-experimental)
- [JEP 320: Remove the Java EE and CORBA Modules](#jep-320-remove-the-java-ee-and-corba-modules)
- [JEP 321: HTTP Client (Standard)](#jep-321-http-client-standard)
- [JEP 323: Local-Variable Syntax for Lambda Parameters](#jep-323-local-variable-syntax-for-lambda-parameters)
- [JEP 324: Key Agreement with Curve25519 and Curve448](#jep-324-key-agreement-with-curve25519-and-curve448)
- [JEP 327: Unicode 10](#jep-327-unicode-10)
- [JEP 328: Flight Recorder](#jep-328-flight-recorder)
- [JEP 329: ChaCha20 and Poly1305 Cryptographic Algorithms](#jep-329-chacha20-and-poly1305-cryptographic-algorithms)
- [JEP 330: Launch Single-File Source-Code Programs](#jep-330-launch-single-file-source-code-programs)
  - [Using The Command Tool](#using-the-command-tool)
  - [Using a Script](#using-a-script)
- [JEP 331: Low-Overhead Heap Profiling](#jep-331-low-overhead-heap-profiling)
- [JEP 332: Transport Layer Security (TLS) 1.3](#jep-332-transport-layer-security-tls-13)
- [JEP 333: ZGC: A Scalable Low-Latency Garbage Collector (Experimental)](#jep-333-zgc-a-scalable-low-latency-garbage-collector-experimental)
- [JEP 335: Deprecate the Nashorn JavaScript Engine](#jep-335-deprecate-the-nashorn-javascript-engine)
- [JEP 336: Deprecate the Pack200 Tools and API](#jep-336-deprecate-the-pack200-tools-and-api)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## [JEP 181](https://openjdk.java.net/jeps/181): Nest-Based Access Control

Summary per link above:

> Introduce nests, an access-control context that aligns with the existing notion of nested types in the Java programming 
language. Nests allow classes that are logically part of the same code entity, but which are compiled to distinct class
files, to access each other's private members without the need for compilers to insert accessibility-broadening bridge 
methods.

## [JEP 309](https://openjdk.java.net/jeps/309): Dynamic Class-File Constants
## [JEP 315](https://openjdk.java.net/jeps/315): Improve Aarch64 Intrinsics
## [JEP 318](https://openjdk.java.net/jeps/318): Epsilon: A No-Op Garbage Collector (Experimental)
## [JEP 320](https://openjdk.java.net/jeps/320): Remove the Java EE and CORBA Modules

Summary from link above:
>Remove the Java EE and CORBA modules from the Java SE Platform and the JDK. These modules were deprecated in Java 
> SE 9 with the declared intent to remove them in a future release.

## [JEP 321](https://openjdk.java.net/jeps/321): HTTP Client (Standard)

Adds the new `java.net.http.HttpClient` class, updated with feedback.

Before Java 11, developers had to use the `URLConnection`, or use third-party library such as Apache HttpClient, or 
OkHttp.  The new Java HTTP Client supports both HTTP/1.1 and HTTP/2. The client will send requests using HTTP/2. 
Requests sent to servers that do not yet support HTTP/2 will automatically be downgraded to HTTP/1.1.

To create an `HttpClient` with default settings.
```java
var client1 = HttpClient.newHttpClient();
var client2 = HttpClient.newBuilder().build();
```
>A `BodyHandler` must be supplied for each `HttpRequest` sent. The BodyHandler determines how to handle the response body, 
> if any. Once an `HttpResponse` is received, the headers, response code, and body (typically) are available. Whether 
> the response body bytes have been read or not depends on the type, `T`, of the response body.

Available BodyHandlers:

   - `.ofString()`
   - `.ofFile()`
   - `.ofInputStream()`
   - `.discarding()`

Available BodyPublishers:

   - `.ofString()`
   - `.ofFile()`
   - `.ofInputStream()`

>Requests can be sent either synchronously or asynchronously
>
> - `send(HttpRequest, BodyHandler)` blocks until the request has been sent and the response has been received.
> - `sendAsync(HttpRequest, BodyHandler)` sends the request and receives the response asynchronously. The sendAsync 
> method returns immediately with a CompletableFuture<HttpResponse>. The CompletableFuture completes when the response becomes available. The returned CompletableFuture can be combined in different ways to declare dependencies among several asynchronous tasks.

Example:
```java
   // Synchronous Example
   HttpClient client = HttpClient.newBuilder()
        .version(Version.HTTP_1_1)
        .followRedirects(Redirect.NORMAL)
        .connectTimeout(Duration.ofSeconds(20))
        .proxy(ProxySelector.of(new InetSocketAddress("proxy.example.com", 80)))
        .authenticator(Authenticator.getDefault())
        .build();
   HttpResponse<String> response = client.send(request, BodyHandlers.ofString());
   System.out.println(response.statusCode());
   System.out.println(response.body());  
   
   // Asynchronous Example
   HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create("https://foo.com/"))
        .timeout(Duration.ofMinutes(2))
        .header("Content-Type", "application/json")
        .POST(BodyPublishers.ofFile(Paths.get("file.json")))
        .build();
   client.sendAsync(request, BodyHandlers.ofString())
        .thenApply(HttpResponse::body)
        .thenAccept(System.out::println);  
```
Simple Get Example:
```java
        // Get Request
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("http://webcode.me"))
                .build();

        HttpResponse<String> response = client.send(request,
                HttpResponse.BodyHandlers.ofString());

        System.out.println(response.body());
```
Simple Post Example:
```java
        // jackson required to write to string
        var values = new HashMap<String, String>() {{
        put("name", "John Doe");
        put ("occupation", "gardener");
        }};

        var objectMapper = new ObjectMapper();
        String requestBody = objectMapper
        .writeValueAsString(values);

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create("https://httpbin.org/post"))
        .POST(HttpRequest.BodyPublishers.ofString(requestBody))
        .build();

        HttpResponse<String> response = client.send(request,
        HttpResponse.BodyHandlers.ofString());

        System.out.println(response.body());
```
## [JEP 323](https://openjdk.java.net/jeps/323): Local-Variable Syntax for Lambda Parameters

This makes the usage of `var` uniform in both local variables and lambda variables.
```java
// Allowed
(String s1, String s2) -> s1 + s2

// Allowed - skip parameters
(s1, s2) -> s1 + s2

// Not Allowed in 10, but allowed in 11
(var s1, var s2) -> s1 + s2

// Why? Uniformity....and you can now add annotations when specifying var.
(@Nonnull var s1, @Nonnull var s2) -> s1 + s2

// Notes
// cannot mix and match using var (when multiple parameters some cannot be declared and others not)
// 
```

## [JEP 324](https://openjdk.java.net/jeps/324): Key Agreement with Curve25519 and Curve448
## [JEP 327](https://openjdk.java.net/jeps/327): Unicode 10
## [JEP 328](https://openjdk.java.net/jeps/328): Flight Recorder
## [JEP 329](https://openjdk.java.net/jeps/329): ChaCha20 and Poly1305 Cryptographic Algorithms
## [JEP 330](https://openjdk.java.net/jeps/330): Launch Single-File Source-Code Programs

As of JDK 10, the java launcher operates in three modes: launching a class file, launching the main class of a JAR 
file, or launching the main class of a module. Java 11 adds a new, fourth mode: launching a class declared in a source 
file.

You can do this two ways...using the java command tool directly and in a Command Line Script (Bash, KSH, DOS etc.).

### Using The Command Tool

Example:
```Text
>c:\foo java MyScript.java
```
The static void main of MyScript is run.

### Using a Script
The Rules 

1. You may not mix java code with your desired shell scripting language.
2. Should you include VM options, you must specify the --source as the first option following the filename in the shebang file.
3. If you need to specify the source version of the file (.java).
4. The shebang must be the first line of the file, of which the first two characters must strictly be ASCII character 0x23 followed by ASCII character 0x21 (#!).
5. The name of the shebang file does not follow the standard naming conventions for Java source files.
6. Files ending in .java are not permitted to include the `#!` (shebang).
7. Java will split arguments for the --sourceoption should it contain whitespace. You may not override this by using quotes to preserve whitespace.
8. The shebang line (the first line) is ignored when evaluating source code against the JLS9.
9. Single file programs only
10 No class files are generated to the file system (they are compiled into memory)
11. If MyScript.class exists, you cannot invoke MyScript.java
12. You must use file name and not class name.
13 The first class in the script will be run (so check order if necessary)

The Script can be invoked like:
```shell
#The file can also be invoked as follows
java -Dtrace=true --source XX -<Options> <FileName> <Argument List>
#For example
java -Dtrace=true --source 11 Countdown.sh
```

## [JEP 331](https://openjdk.java.net/jeps/331): Low-Overhead Heap Profiling
## [JEP 332](https://openjdk.java.net/jeps/322): Transport Layer Security (TLS) 1.3
## [JEP 333](https://openjdk.java.net/jeps/333): ZGC: A Scalable Low-Latency Garbage Collector (Experimental)
## [JEP 335](https://openjdk.java.net/jeps/335): Deprecate the Nashorn JavaScript Engine
## [JEP 336](https://openjdk.java.net/jeps/336): Deprecate the Pack200 Tools and API

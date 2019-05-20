---
title: "Kotlin on command line"
date: 2019-05-20T12:52:50+05:30
draft: true
---

This post introduces you to basic kotlin command line tools for compiling and running kotlin programs.

We will:

1. Download kotlin command line tools
2. Write a basic hello-world program in kotlin
3. Compile and run the kotlin program

## Download kotlin distribution

On OS X, the preferred way is through Homebrew.

```
$ brew update
$ brew install kotlin
```

## Verify install

Run the following command on your terminal to verify if the installation succeeded.

```
$ kotlin -version
```

## Available commands

You should now have access to tools included in the distribution. The most basic and important are kotlin and kotlinc.

The `kotlin` command is to run kotlin programs, or REPL (kotlin interactive shell).

The `kotlinc` command is to compile kotlin source code.

Run the following commands to see how to use them.

```
$ kotlin -help
$ kotlinc -help
```

## Kotlin hello world

Let's write a basic hello world program in kotlin that we will compile and run.

Our program will print out 'Hello, World!' on the standard output, and also print greetings to any arguments passed to it.

Let's create a new directory for this exercise. Our program will reside in a file named **hello.kt**.

```
$ mkdir kotlin-hello
$ cd kotlin-hello
$ touch hello.kt
```

Use your favorite editor to write the following program into hello.kt file.

_hello.kt_

```
fun main(args: Array<String>) {
    println(“Hello, World!”)
    for (arg in args) {
        println(“Hello, $arg!”)
    }
}
```

## Compile

### Compile to class file

Use the following command to compile your **hello.kt** program to a class file.

```
$ kotlinc hello.kt -d output
```

The `-d` option specifies the output directory for generated files.

The output directory should now contain a class file named **HelloKt.class**, and some meta information inside a **META-INF** directory.

The generated class file contains a class named `HelloKt`.

You can run the program, i.e. execute the main function in the generated `HelloKt` class, using the `kotlin` command.

You will also need to specify where to find the class, which is done using the `-classpath` option. The default value of classpath is "." (current directory).

Try the following command:

```
$ kotlin HelloKt
error: could not find or load main class HelloKt
```

This resulted in an error because the `kotlin` command couldn't find any `HelloKt` class in the current directory (default classpath value).

To run the command from the current directory, specify the classpath:

```
$ kotlin -classpath output HelloKt
Hello, World!
```

Or, you can go to the output directory and run the command without specifying the classpath:

```
$ kotlin HelloKt
Hello, World!
```

Our program can also print greetings to any arguments passed to it. Specify a few arguments spearated by space in the command:

```
$ kotlin HelloKt Bob Alice
Hello, World!
Hello, Bob!
Hello, Alice!
```

Again, you can run the same command from the **kotlin-hello** directory by specifying the classpath:

From the kotlin-hello directory:

```
$ kotlin -classpath output HelloKt Bob Alice
Hello, World!
Hello, Bob!
Hello, Alice!
```

### Compile to jar

You can also use the `-d` option to compile your program to a jar archive, which is useful if you are creating a library.
Jar format is just an archive for compiled class files.

First, lets clear things up by deleting the outout directory containing the generated class files.

```
$ rm -rf output
```

Use the kotlinc command to compile to a .jar file:

```
$ kotlin hello.kt -d hello.jar
```

This generates a .jar file named **hello.jar**, which contains the compiled class `HelloKt` and some meta data.

You can read the contents of a .jar file using the `jar` command. You'll need to have the jdk installed for this.

```
$ jar -tf hello.jar
META-INF/MANIFEST.MF
HelloKt.class
META-INF/main.kotlin_module
```

Use the `kotlin` command to execute the program. You'll need to specify the .jar file as classpath.

```
$ kotlin -classpath hello.jar HelloKt
Hello, World!
```

Again, specify some arguments for the program:

```
$ kotlin -classpath hello.jar HelloKt Bob Alice
Hello, World!
Hello, Bob!
Hello, Alice!
```

Till now, we have compiled the kotlin source code we wrote without the kotlin runtime.
Hence, the hello.jar library can be executed by the kotlin command, but the java launcher cannot run it.

Try running the program with java launcher.

```
$ java -jar hello.jar
Exception in thread "main" java.lang.NoClassDefFoundError: kotlin/jvm/internal/Intrinsics
    at HelloKt.main(hello.kt)
Caused by: java.lang.ClassNotFoundException: kotlin.jvm.internal.Intrinsics
    at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
    at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
    at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:335)
    at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
    ... 1 more
```

This resulted in an error because the necessary kotlin runtime is not present in the compiled output or on the classpath.

You can specify `-incude-runtime` option while compiling with kolinc to include the kotlin runtime with the compilation output.

```
$ kotlinc hello.kt -include-runtime -d hello.jar
```

Now, the generated hello.jar contains the kotlin runtime classes; its size is also considerably larger than the previous output.
This archive is now a self-contained jvm application runnable by the java launcher.

```
$ java -jar hello.jar
Hello, World!
```

Again, with arguments:

```
$ java -jar hello.jar Bob Alice
Hello, World!
Hello, Bob!
Hello, Alice!
```

If you generate the library without the `-include-runtime` option, the kotlin runtime must be present on the classpath when using the library.

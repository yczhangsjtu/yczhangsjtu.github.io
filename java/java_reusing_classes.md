---
title: Resuing Classes in Java
---

# Reusing Classes in Java

## Package Name

If you want to start writing Java code, and publish them on the internet, you had better get yourself a domain name.
Then reverse it to be the prefix of all the package names you create.
For example, if your domain name is `alice.net`, and you want to develop a library called `matrix`, then the package name should be `net.alice.matrix`.

## Class Path

The environment variable `CLASSPATH` is usually automatically set when you install Java on your machine.
If not, set it manually. Usually it contains current directory `.` and path for standard library.

When trying to interpret a `.class` file which references another package, the interpretor will take the package name, split it by `.`, concatenate by `/`, then append it to all the paths specified in `CLASSPATH`. For any class in the package, the interpretor just find the corresponding `.class` file in that location.

If some `.class` files are packed into a `.jar` file, you have to put the `.jar` file name into the `CLASSPATH`, as if the `.jar` package is a directory (in some sense, it actually is).

Once the `CLASSPATH` is set properly, the source file to be compiled can be put anywhare.

## Static Import

You can `import static` the static methods in a class.
For example, you can implement a `Math` class with static methods like `square`, `sqrt`, `sin` and `cos`.
Then you import it by
```java
import static net.alice.Math.*
```
Then you can at anywhare directly call `square` and other methods.

Package import can be used to turn debug on or off, like switches in C preprocessing micros.
You can implement two packages `debug` and `debugoff`, in them put methods with exactly the same names, but those methods in `debugoff` actually does not do anything.

## Package Access

Any method in a Java class has access modifier, like `public`, `private` or `protected`.
If not, then the access of it is default to **package access**. This means it can be accessed anywhere inside the package.

If not containing a `package` declaration, a class belongs to the **default** package under that directory. Such classes in the same directory can access members of each other which have package accesses.

Note that `protected` access automatically grant **package access**. So, classes in the same package does not have to inherit a class to be able to access its protected methods.

## The **@override** Annotation

JavaSE5 introduces the `@override` annotation. This does not change any logic of the program, except that the compiler will help you make sure that you are actually **overriding** a method in the base class, not just **overloading** it, perhaps with some mistakes in the argument list.

## Composition vs. Inheritance

You want to make use of an existing class to implement a new class, is it better choice to inherit the old class or include a `private` object of the old class in the new class?

The answer depends on whether you want to expose the interface of the old class to the user of your new class, or whether an object of the new class **is** one of the old class, or **has** one of the old class.

Another clear way to determine whether to use composition or inheritance is to ask whether you will need to upcast from your new class to old class. If no need to upcast, then better not use inheritance.

## The **final** Keyword

In general, the keyword `final` means **this cannot change**.

There are three places where `final` can be used.

### **final** data

Basically speaking, `final` is similar to `const` in C. The difference is, `final` does not always require the variable to be initialized immediately at declaration. So the compiler does not necessarily know the value of a `final` variable at compile time. A `final` variable must be initialized before being used, and the compiler ensures that.

Note that a `final` reference means the reference cannot point to any other objects once initialized. But the object it points to **can** be modified! Java does not provide a method to prevent an object from being modified.

Similar to C, arguments declared `final` cannot be modified inside the method.

### **final** methods

A `final` method cannot be overriden. So `private` methods are automatically `final`. You can add a `final` to the declaration, but that does not change anything. If you do not want your method to be overriden by inheritants, declare it as `final`. You cannot implement a `final` method in the base class with same name and argument list, but you can do so with a `private` method in the base class, since `private` method is completely hidden in the base class.

### **final** classes

A `final` class cannot be inherited. The `final` modifer on the class does not influence anything inside the class.



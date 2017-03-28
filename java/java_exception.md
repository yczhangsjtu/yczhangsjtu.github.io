---
title: Exception Handling in Java
---

# Exception Handling in Java

## Throw an exception whenever something that shouldn't have happened happens, and assume that it will be handled well.

```java
public class BasicException {
	String msg;
	public BasicException(String msg) {
		this.msg = msg;
	}
	public void Print() {
		if(this.msg == null) {
			throw new NullPointerException("msg is null");
		}
		System.out.println(msg);
	}
}

```

## The most trivial way to create your own exception requires almost no code at all.

```java
class MyException extends Exception {}
```

## To print the trace to logger, call the printStackTrace method with PrintWriter parameter.

```java
import java.io.PrintWriter;
import java.io.StringWriter;
import java.util.logging.Logger;


StringWriter trace = new StringWriter();
e.printStackTrace(new PrintWriter(trace));
Logger.getLogger("Loggin Exception").severe(trace.toString());
```

## Exceptions have to be specified in the method throwing it.

```java
class MyException extends Exception {}

public class BasicException {
	public void throwMyException() throws MyException {
		throw new MyException();
	}
}
```

## Exceptions inheriting RuntimeException do not have to be specified.

```java
public class BasicException {
	public void throwMyException() throws MyException {
		throw new MyException();
	}
	public void throwRuntimeException() {
		throw new RuntimeException();
	}
}
```
> `NullPointerException` inherits `RuntimeException`.

> `RuntimeException` represents the kind of exceptions that does not have to be checked, i.e. checked automatically by the JRE, and thrown automatically. If not, the program would be bloated by the great number of throwing exceptions.

> Since the compiler allows it to not get caught, `RuntimeException` can get all the way out of the `main` function and cause the program to terminate, and `printStackTrace()` automatically.

> `RuntimeException` is often useful to debugging, since they are usually caused by programming errors, like referencing null pointer, inappropriate IO operations, etc.

## If you catch some exception `e`, and want to throw a new exception of your own type, but still want to keep the information in `e`, you can use the `initCause` method to set `e` as the `cause` of your exception. As the `cause` of `e` might be another exception, this chains a list of exceptions together.

```java
class MyException extends Exception {}

public class BasicException {
	String msg;
	public BasicException(String msg) {
		this.msg = msg;
	}
	public void print() {
		if(this.msg == null) {
			throw new NullPointerException("msg is null");
		}
		System.out.println(msg);
	}
	public void throwAnotherException() throws MyException {
		try {
			print();
		} catch (NullPointerException e) {
			MyException myException = new MyException();
			myException.initCause(e);
			throw myException;
		}
	}
}
```
> All the information in the chain will be output by the `printStackTrace()` of the outmost exception in the chain.

## The code in `finally` will always be executed! No matter what happens in `try` block or `catch` block, even if the method is returned in these blocks.

```java
public class BasicException {
	String msg;
	public BasicException(String msg) {
		this.msg = msg;
	}
	public void print() {
		if(this.msg == null) {
			throw new NullPointerException("msg is null");
		}
		System.out.println(msg);
	}
	public void setMessage() {
		try {
			print();
		} finally {
			msg = "message";
		}
	}
}
```

## An exception can be lost in the `finally` block, if it throws another exception or simply returns.

```java
public class BasicException {
	public void loseException() {
		try {
			throw new Exception();
		} finally {
			return;
		}
	}
}
```

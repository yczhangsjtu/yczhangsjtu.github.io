---
title: Java Generics
---

# Java Generics

## Generic allows class to contain arbitray type attributes.

```java
public class BasicGeneric<T> {
	private T content;
	public void set(T c) {
		content = c;
	}
	public T get() {
		return content;
	}
}
```

## A class can have more than one generic types.

```java
public class GenericPair<A,B> {
	private final A first;
	private final B second;
	public GenericPair(A a, B b) {
		first = a;
		second = b;
	}
	public String toString() {
		return "("+first+","+second+")";
	}
}
```

## Interface can also be generic.

```java
interface Interface<T> {
	T get();
}

public class GenericInterface implements Interface<String> {
	private String content;
	public void set(String c) {
		content = c;
	}
	public String get() {
		return content;
	}
}
```

## Method can also be generic.

```java
public class GenericMethod {
	public <T> String f(T x) {
		return x.toString();
	}
}
```

> Do not have to call the method with <>, the compiler can tell from the argument passed to it.

## Generic type can be combined with variable argument list.

```java
public class BasicGeneric<T> {
	private List<T> list;
	public void append(T... args) {
		list = new ArrayList<T>();
		for(T x: args) {
			list.add(x);
		}
	}
}
```

## Generic type can be used with anonymous inner class.

```java
interface Interface<T> {
	T get();
}

public class GenericInnerClass {
	public String get() {
		return (new Interface<String>() {
			public String get() {
				return "test";
			}
		}).get();
	}
}
```

## Generic type can be specified to extend some class.

```java
class Test1 {
	public String get() {
		return "test1";
	}
}

class Test2 extends Test1 {
	public String get() {
		return "test2";
	}
}

public class BasicGeneric<T extends Test1> {
	private T content;
	public void set(T c) {
		content = c;
	}
	public T get() {
		return content;
	}
}
```
> The use of generic in this example does not seem reasonable at first glance. Why not just replace `T` by `Test1` here? The difference is, the object returned by `get()` is of type `T` which can be `Test1` or `Test2`, depending on the definition of `T`. You do not have to make upcast or downcast.
>
> Without the `extends`, object of type `T` can only call methods of type `Object`.

## You can use wildcard to specify the generic type of a reference can be anything extending some basic type, but the instantiation must be specific. To pass argument of type T to the class, you cannot use T, however, but declare the argument as Object, then cast it to T.

```java
class Test1 {
	public String get() {
		return "test1";
	}
}

class Test2 extends Test1 {
	public String get() {
		return "test2";
	}
}

public class BasicGeneric<T extends Test1> {
	private T content;
	@SuppressWarnings("unchecked")
	public void set(Object c) {
		content = (T)c;
	}
	public T get() {
		return content;
	}
	public static void main(String []args) {
		BasicGeneric<? extends Test1> basicGeneric = new BasicGeneric<Test1>();
		basicGeneric.set(new Test2());
	}
}
```



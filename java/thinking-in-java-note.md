---
title: Thinking in Java Study Note
---

# Thinking in Java Study Note

## Inner classes

### Inner classes have direct access to private members of enclosing class

```java
public class InnerClassExample {
	private String test = "test";
	class InnerClass {
		public String get() {
			return test;
		}
	}
}
```

### An object of inner class can be declared by compund of enclosing class name and inner class name, but has to be instantiated by an instance of the enclosing class.

```java
InnerClassExample innerClassExample = new InnerClassExample();
InnerClassExample.InnerClass innerClass = innerClassExample.new InnerClass();
```

> The reason for this is that an object of the inner class must have an implicit reference to an object of the enclosing class, which has to exist.

### Inner class field can shadow field in enclosing class of same name, refer to the shadowed field in enclosing class by `.this`

```java
public class InnerClassExample {
	private String test = "test";
	class InnerClass {
		private String test = "test1";
		public String get() {
			return test;
		}
		public String getEnclosing() {
			return InnerClassExample.this.test;
		}
	}
}
```

### Inner class can be declared in method, then it can be accessed only in that method

```java
public class InnerClassExample {
	public String get() {
		class MethodInnerClass {
			private String test = "test2";
			public String get() {
				return test;
			}
		}
		MethodInnerClass innerClass = new MethodInnerClass();
		return innerClass.get();
	}
}
```

### Inner class can be declared anonymously

```java
public class InnerClassExample {
	private String test = "test";
	class InnerClass {
		public String get() {
			return test;
		}
	}
	public String get() {
		InnerClass innerClass = new InnerClass() {
			public String get() {
				return "test4";
			}
		};
		return innerClass.get();
	}
}
```

### Inner class cannot have static fields or methods, but static inner class (called nested class) can.

```java
public class InnerClassExample {
	static class StaticInnerClass {
		static String test = "test5";
		static String get() {
			return test;
		}
	}
	public String get() {
		return StaticInnerClass.get();
	}
}
```

### Nested class does not have to be declared by an instance of the enclosing class.

```java
InnerClassExample.StaticInnerClass staticInnerClass = new InnerClassExample.StaticInnerClass();
```

### Classes nested in interface is automatically static and public, the interface is just used as a namespace for the nested class.

```java
public interface EnclosingInterface {
	String get();
	class ClassInInterface implements EnclosingInterface {
		String test = "test";
		@Override
		public String get() {
			return test;
		}
	}
}
```

### Nested class can be used to create classes having access to the fields of the enclosing class. This is particularly useful when implementing event system.

```
abstract class Event {
	protected final String message;
	public Event(String message) {
		this.message = message;
	}
	String getIntegerMessage(int i) {
		return this.message + ": " + Integer.toString(i);
	}
	abstract String getMessage();
}

public class InnerClassCar {
	int speed;
	int oil;
	String log;
	class SpeedHighEvent extends Event {
		public SpeedHighEvent() {
			super("Speed is too high");
		}
		@Override
		String getMessage() {
			return getIntegerMessage(speed);
		}
	}
	class OilLowEvent extends Event {
		public OilLowEvent() {
			super("Oil is low");
		}
		@Override
		String getMessage() {
			return getIntegerMessage(oil);
		}
	}
	
	public InnerClassCar() {
		this.speed = 0;
		this.oil = 100;
		this.log = "";
	}
	
	public void SpeedUp(int dv) {
		this.speed += dv;
		this.oil -= dv;
		if(this.speed > 80) {
			this.log += new SpeedHighEvent().getMessage() + "\n";
		}
		if(this.oil < 10) {
			this.log += new OilLowEvent().getMessage() + "\n";
		}
	}
	
	public String getLog() {
		return this.log;
	}
}
```

### To extend an inner class, the implicit referece to the enclosing class has to be explicitly instantiated.

```java
class InheritInner extends InnerClassExample.InnerClass {
	public InheritInner(InnerClassExample innerClassExample) {
		innerClassExample.super();
	}
}
```


### After compilation, a `.class` file will be generated for the inner class, with name scheme `EnclosingClass$InnerClass.class`.

## Exception Handling

### Throw an exception whenever something that shouldn't have happened happens, and assume that it will be handled well.

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

### The most trivial way to create your own exception requires almost no code at all.

```java
class MyException extends Exception {}
```

### To print the trace to logger, call the printStackTrace method with PrintWriter parameter.

```java
import java.io.PrintWriter;
import java.io.StringWriter;
import java.util.logging.Logger;


StringWriter trace = new StringWriter();
e.printStackTrace(new PrintWriter(trace));
Logger.getLogger("Loggin Exception").severe(trace.toString());
```

### Exceptions have to be specified in the method throwing it.

```java
class MyException extends Exception {}

public class BasicException {
	public void throwMyException() throws MyException {
		throw new MyException();
	}
}
```

### Exceptions inheriting RuntimeException do not have to be specified.

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

### If you catch some exception `e`, and want to throw a new exception of your own type, but still want to keep the information in `e`, you can use the `initCause` method to set `e` as the `cause` of your exception. As the `cause` of `e` might be another exception, this chains a list of exceptions together.

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

### The code in `finally` will always be executed! No matter what happens in `try` block or `catch` block, even if the method is returned in these blocks.

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

### An exception can be lost in the `finally` block, if it throws another exception or simply returns.

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

## Strings

### Use string builder to accumulate strings.

```java
public class BasicStrings {
	public String useStringBuilder() {
		StringBuilder stringBuilder = new StringBuilder();
		stringBuilder.append("a");
		stringBuilder.append('b');
		stringBuilder.append(0);
		stringBuilder.append(false);
		return stringBuilder.toString();
	}
}
```

### The method `format()` in `System.out` is equivalent to `printf()`.

```java

public class BasicStrings {
	public void useFormat() {
		System.out.format("Hello %s!\n", "World");
	}
}
```

### Use a Formatter to specify where the output goes.

```java
import java.util.Formatter;

public class BasicStrings {
	public void useFormatter() {
		Formatter f = new Formatter(System.out);
		f.format("Hello %s!\n", "World");
		f.close();
	}
}
```

### Use `String.format` to generate a string with format string.

```java
public class BasicStrings {
	public String useStringFormat() {
		return String.format("Hello %s!\n", "World");
	}
}
```

### Use `Pattern` and `Matcher` to perform regular expression matching.

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class BasicStrings {
	public String usePatternMatcher(String input) {
		StringBuilder stringBuilder = new StringBuilder();
		Pattern p = Pattern.compile("\\b\\w+\\b");
		Matcher m = p.matcher(input);
		while(m.find()) {
			stringBuilder.append(m.group()+" ");
		}
		return stringBuilder.toString();
	}
}
```

### Use `Pattern.matches` to check if a string is an exact match of a regular expression.

```java
import java.util.regex.Pattern;

public class BasicStrings {
	public boolean usePatternMatches(String input) {
		return Pattern.matches(".*abcdef.*", input);
	}
}
```

### Each match found by `matcher.find()` could have more than one groups.

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class BasicStrings {
	public String useGroups(String input) {
		StringBuilder stringBuilder = new StringBuilder();
		Pattern p = Pattern.compile("\\b(\\w+)=(\\w+)\\b");
		Matcher m = p.matcher(input);
		while(m.find()) {
			stringBuilder.append(m.group(1)+":"+m.group(2)+" ");
		}
		return stringBuilder.toString();
	}
}
```

### After each successful match, `matcher.start()` and `matcher.end()` gives the start and end index of the matched portion in the entire string. Note that the `end` points to the first position after the matched portion.

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class BasicStrings {
	public String useMatcherStart(String input) {
		Pattern p = Pattern.compile("\\b\\w+\\b");
		Matcher m = p.matcher(input);
		if(m.find()) {
			return String.format("start: %d, end: %d", m.start(), m.end());
		} else {
			return "";
		}
	}
}
```
> Calling `matcher.start()` after a failed match will cause an exception.

### `pattern.split()` can be used to split strings by the regular expression represented by `pattern`.

```java
import java.util.Arrays;
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class BasicStrings {
	public String usePatternSplit(String input) {
		Pattern p = Pattern.compile("\\s+");
		return Arrays.toString(p.split(input));
	}
}
```


### Use `string.replaceAll()` to replace all occurrences of a regular expression to something else.

```java
public class BasicStrings {
	public String useReplace(String input) {
		return input.replaceAll("\\b\\w+\\b", "TOKEN");
	}
}
```

### Use `matcher.appendReplacement()` and `matcher.appendTail()` to replace each occurrence by a different replacement, and accumulate  the replacements in a `StringBuffer`.

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class BasicStrings {
	public String useAppendReplacement(String input) {
		int count = 0;
		StringBuffer stringBuffer = new StringBuffer();
		Pattern p = Pattern.compile("\\b[a-z]+\\b");
		Matcher m = p.matcher(input);
		while(m.find()) {
			m.appendReplacement(stringBuffer, "["+m.group().toUpperCase()+"]"+count);
			count++;
		}
		m.appendTail(stringBuffer);
		return stringBuffer.toString();
	}
}
```

### Use `matcher.reset()` to set a matcher to match another string.

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

public class BasicStrings {
	public String useMatcherReset(String input1, String input2) {
		StringBuilder stringBuilder = new StringBuilder();
		Pattern p = Pattern.compile("\\b[a-z]+\\b");
		Matcher m = p.matcher(input1);
		while(m.find()) {
			stringBuilder.append(m.group()+" ");
		}
		m.reset(input2);
		while(m.find()) {
			stringBuilder.append(m.group()+" ");
		}
		return stringBuilder.toString();
	}
}
```

### Use `Scanner` to read a stream of input string and evaluate them into integers (or other types of variables).

```java
import java.io.BufferedReader;
import java.io.StringReader;
import java.util.Scanner;

public class ScannerExample {
	public void basicUsage(String input) {
		Scanner scanner = new Scanner(new BufferedReader(new StringReader(input)));
		System.out.println(scanner.nextLine());
		System.out.printf("Int: %d\n", scanner.nextInt());
		scanner.close();
	}
}
```

### You can set a different delimiter for the scanner instead of the default white space.

```java
import java.io.BufferedReader;
import java.io.StringReader;
import java.util.Scanner;

public class ScannerExample {
	public void setDelimiter(String input) {
		Scanner scanner = new Scanner(new BufferedReader(new StringReader(input)));
		scanner.useDelimiter("\\s*,\\s*");
		System.out.println(scanner.nextLine());
		System.out.printf("Int: %d\n", scanner.nextInt());
		scanner.close();
	}
}
```
> Scanner also has a `next(regex)` method to find the next regular expression.

> Scanner obsoletes `StringTokenizer`.

## Runtime Type Information

### A special class in Java is named `Class`, objects of this class represents the classes used in the program. They can be manipulated like normal objects.

```java
class Test {
	static {
		System.out.println("Loading test");
	}
}

public class BasicRTTI {
	public void printClassName() {
		Test test = new Test();
		Class c = test.getClass();
		Class s = c.getSuperclass();
		System.out.println(c.getName());
		System.out.println(c.getSimpleName());
		System.out.println(c.getCanonicalName());
		System.out.println(s.getName());
		System.out.println(s.getSimpleName());
		System.out.println(s.getCanonicalName());
	}
}
```

### Class objects can be easily obtained by `ClassName.class`.

```java
class Test {
	static {
		System.out.println("Loading test");
	}
}

public class BasicRTTI {
	public void printClassName() {
		Class c = Test.class;
		Class s = c.getSuperclass();
		System.out.println(c.getName());
		System.out.println(c.getSimpleName());
		System.out.println(c.getCanonicalName());
		System.out.println(s.getName());
		System.out.println(s.getSimpleName());
		System.out.println(s.getCanonicalName());
	}
}
```

### Class objects can be specified to point to limited types of classes.

```java
class Test {
	static {
		System.out.println("Loading test");
	}
}

public class BasicRTTI {
	public void printClassName() {
		Class<Test> c = Test.class;
		Class s = c.getSuperclass();
		System.out.println(c.getName());
		System.out.println(c.getSimpleName());
		System.out.println(c.getCanonicalName());
		System.out.println(s.getName());
		System.out.println(s.getSimpleName());
		System.out.println(s.getCanonicalName());
	}
}
```

### If you put `?` in the `<>` the class object can point to any type of classes.

```java
class Test {
	static {
		System.out.println("Loading test");
	}
}

public class BasicRTTI {
	public void printClassName() {
		Class<?> c = Test.class;
		Class s = c.getSuperclass();
		System.out.println(c.getName());
		System.out.println(c.getSimpleName());
		System.out.println(c.getCanonicalName());
		System.out.println(s.getName());
		System.out.println(s.getSimpleName());
		System.out.println(s.getCanonicalName());
	}
}
```

### To specify a class object to point to a specific type and any subtype, use `? extends ClassName`.

```java
class Test {
	static {
		System.out.println("Loading test");
	}
}

public class BasicRTTI {
	public void printClassName() {
		Test test = new Test();
		Class<? extends Test> c = test.getClass();
		Class<? extends Object> s = c.getSuperclass();
		System.out.println(c.getName());
		System.out.println(c.getSimpleName());
		System.out.println(c.getCanonicalName());
		System.out.println(s.getName());
		System.out.println(s.getSimpleName());
		System.out.println(s.getCanonicalName());
	}
}
```

### Use `newInstance()` of a class object to get an instance of that class.

```java
class Test {
	static {
		System.out.println("Loading test");
	}
}

public class BasicRTTI {
	public String getNewInstance() {
		Class<Test> c = Test.class;
		try {
			c.newInstance();
			return "test";
		} catch (InstantiationException e) {
			return "InstantiationException";
		} catch (IllegalAccessException e) {
			return "IllegalAccessException";
		}
	}
}
```

### Use `cast()` of a class object to perform a runtime cast.

```java
class Test {
	static {
		System.out.println("Loading test");
	}
}

class SubTest extends Test {
	
}

public class BasicRTTI {
	public String useCast() {
		Class<Test> c = Test.class;
		SubTest sub = new SubTest();
		Test test = c.cast(sub);
		return "Test";
	}
}
```

### Use `classObject.isInstance` to replace `instanceof`.

```java
class Test {
	static {
		System.out.println("Loading test");
	}
}

class SubTest extends Test {
	
}

public class BasicRTTI {
	public String useIsInstance() {
		Class<SubTest> c = SubTest.class;
		Test test = new SubTest();
		if (c.isInstance(test)) {
			return "SubTest";
		} else {
			return "";
		}
	}
}
```

### Use `classObject.isAssignableFrom(anotherClassObject)` to check if an object of the type of the class object passed in can be assigned to a reference of the type of this class object.


```java
class Test {
	static {
		System.out.println("Loading test");
	}
}

class SubTest extends Test {
	
}

public class BasicRTTI {
	public String useIsAssignableFrom() {
		Test test = new Test();
		SubTest subtest = new SubTest();
		Class<?> testClass = test.getClass();
		Class<?> subtestClass = subtest.getClass();
		if (testClass.isAssignableFrom(subtestClass)) {
			return "Test";
		} else {
			return "";
		}
	}
}
```

### Use the `classObject.getMethods()` and `classObject.getConstructors()` to obtain the methods and constructors of a class, which are of types `Method` and `Constructor` classes in `java.lang.reflect` package.

```java
import java.lang.reflect.Constructor;
import java.lang.reflect.Method;

public class ReflectExample {
	public void MethodExtractor(String className) {
		try {
			Class<?> c = Class.forName(className);
			Method[] methods = c.getMethods();
			Constructor<?>[] constructors = c.getConstructors();
			for (Constructor<?> constructor: constructors) {
				System.out.println(constructor.toGenericString());
			}
			for (Method method: methods) {
				System.out.println(method.toGenericString());
			}
		} catch (ClassNotFoundException e) {
			System.err.println("No such class: "+className);
		}
	}
}
```

> In this way, you can implement java code autocomplete like in IDE.

### Generate objects of a particular interface at runtime using `Proxy`.

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

class ProxyHandler implements InvocationHandler {
	private Object object;
	ProxyHandler(Object object) {
		this.object = object;
	}
	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		return method.invoke(object, args);
	}
}

interface ProxyTest {
	String get();
}

class ProxyTestImplement implements ProxyTest {
	public String get() {
		return "test";
	}
}

public class ReflectExample {
	public String useProxy() {
		ProxyTest test = (ProxyTest) Proxy.newProxyInstance(
				ProxyTestImplement.class.getClassLoader(),
				new Class[] {ProxyTest.class},
				new ProxyHandler(new ProxyTestImplement()));
		return test.get();
	}
}
```

> In this example the result is no different from using the interface directly. But you can perform additional tasks if you like, in the `invoke` method of your own invocation handler.
> 
> An array of interfaces is passed to the `newProxyInstance` method, because the class may implement more than one interface.

## Generics

### Generic allows class to contain arbitray type attributes.

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

### A class can have more than one generic types.

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

### Interface can also be generic.

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

### Method can also be generic.

```java
public class GenericMethod {
	public <T> String f(T x) {
		return x.toString();
	}
}
```

> Do not have to call the method with <>, the compiler can tell from the argument passed to it.

### Generic type can be combined with variable argument list.

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

### Generic type can be used with anonymous inner class.

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

### Generic type can be specified to extend some class.

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

### You can use wildcard to specify the generic type of a reference can be anything extending some basic type, but the instantiation must be specific. To pass argument of type T to the class, you cannot use T, however, but declare the argument as Object, then cast it to T.

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

## Arrays

### Reference to multidimensional array does not contain size information in declaration. Instantiation should at least contain size information of the first dimension, which can be dynamic.

```java
import java.util.Arrays;

public class MultidimensionalArray {
	private int[][] array = {
			{0,1},
			{2,3}
	};
	public String get() {
		return Arrays.deepToString(array);
	}
	public void setArray(int size) {
		array = new int[size][];
		for(int i = 0; i < size; i++) {
			array[i] = new int[size];
		}
		// This is equivalent to
		// array = new int[size][size];
	}
}
```

### Multidimensional array can have different second dimensional sizes for different first dimensional index. Consider multidimensional array as array of reference of arrays.

```java
import java.util.Arrays;

public class MultidimensionalArray {
	private int[][] array = {
			{0,1},
			{2,3}
	};
	public String get() {
		return Arrays.deepToString(array);
	}
	public void setArray(int size) {
		array = new int[size][];
		for(int i = 0; i < size; i++) {
			array[i] = new int[size+i];
			for(int j = 0; j < size+i; j++) {
				array[i][j] = i+j;
			}
		}
	}
}
```

### The package `java.util.Arrays` contains a lot of methods to operate on arrays. To copy elements of array to another array of same type, `System.arraycopy()` is faster than `for` loop.

```java
import java.util.Arrays;

public class ArrayUtils {
	private int[] A;
	private int[] B;
	public void set(int size) {
		A = new int[size];
		B = new int[size];
		for(int i = 0; i < size; i++) {
			A[i] = i;
			B[i] = size-i;
		}
	}
	public void copyA2B() {
		System.arraycopy(A, 0, B, 0, A.length);
	}
	public boolean equalA2B() {
		return Arrays.equals(A, B);
	}
	public void sortB() {
		Arrays.sort(B);
	}
	public int searchB(int i) {
		return Arrays.binarySearch(B, i);
	}
	public String get() {
		return Arrays.toString(A)+Arrays.toString(B);
	}
}
```

## Java Containers

### Functions of the collection interface.

```java
import java.util.Collection;
import java.util.Iterator;

public class CollectionFunc {
	private final Collection<String> collection;
	public CollectionFunc(Collection<String> collection) {
		this.collection = collection;
	}
	public void add(String s) {
		collection.add(s);
	}
	public void addAll(Collection<String> collection) {
		this.collection.addAll(collection);
	}
	public void clear() {
		this.collection.clear();
	}
	public boolean contains(String s) {
		return collection.contains(s);
	}
	public boolean containsAll(Collection<String> collection) {
		return this.collection.containsAll(collection);
	}
	public boolean isEmpty() {
		return collection.isEmpty();
	}
	Iterator<String> iterator() {
		return collection.iterator();
	}
	public boolean remove(String s) {
		return collection.remove(s);
	}
	public boolean removeAll(Collection<String> collection) {
		return this.collection.removeAll(collection);
	}
	public boolean retainAll(Collection<String> collection) {
		return this.collection.retainAll(collection);
	}
	public int size() {
		return collection.size();
	}
	public Object[] toArray() {
		return collection.toArray();
	}
	public String[] toStringArray() {
		return collection.toArray(new String[0]);
	}
}
```

## Java I/O

### Use the list() method of an object of File class to list the content of a directory.

```java
import java.io.File;

public class FileClass {
	public void list(String dir) {
		File path = new File(dir);
		String [] list = path.list();
		for(String filename: list) {
			System.out.println(filename);
		}
	}
}
```

### Use FilenameFilter interface to apply filter to the list() method.

```java
import java.io.File;
import java.io.FilenameFilter;

class Filter implements FilenameFilter {

	@Override
	public boolean accept(File dir, String name) {
		return !name.startsWith(".");
	}
}

public class FileClass {
	public void list(String dir) {
		File path = new File(dir);
		String [] list = path.list(new Filter());
		for(String filename: list) {
			System.out.println(filename);
		}
	}
}
```

### File object can be used to obtain a lot of information of the file.

```java
import java.io.File;

public class FileClass {
	public boolean checkFileExist(String filename) {
		File file = new File(filename);
		return file.exists();
	}
	public void getFileInfo(String filename) {
		File file = new File(filename);
		System.out.println(file.getAbsolutePath());
		System.out.println(file.getName());
		System.out.println(file.getFreeSpace());
		System.out.println(file.getTotalSpace());
	}
}
```

### Use FileReader to open a file in character mode, then pass the reader to a FilterReader such as BufferedReader for particular operations.

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class ReaderClass {
	public String readFile(String filename) throws IOException {
		BufferedReader in = new BufferedReader(
				new FileReader(filename));
		StringBuilder stringBuilder = new StringBuilder();
		String s;
		while((s = in.readLine()) != null) {
			stringBuilder.append(s+"\n");
		}
		in.close();
		return stringBuilder.toString();
	}
}
```

### Use StringReader to create a reader reading from string, which is similar to FileReader.

```java

import java.io.BufferedReader;
import java.io.StringReader;
import java.io.IOException;

public class ReaderClass {
	public String readString(String content) throws IOException {
		BufferedReader in = new BufferedReader(
				new StringReader(content));
		StringBuilder stringBuilder = new StringBuilder();
		String s;
		while((s = in.readLine()) != null) {
			stringBuilder.append(s+"\n");
		}
		in.close();
		return stringBuilder.toString();
	}
}
```

### Use FileInputStream to open a file in byte mode, pass it to constructor of BufferedInputStream, then to DataInputStream to read by bytes.

```java
import java.io.BufferedInputStream;
import java.io.DataInputStream;
import java.io.FileInputStream;
import java.io.IOException;

public class StreamClass {
	public void readFile(String filename) throws IOException {
		DataInputStream in = new DataInputStream(
				new BufferedInputStream(new FileInputStream(filename)));
		while(in.available() != 0) {
			System.out.print((char)in.readByte());
		}
		in.close();
	}
}
```

### Use FileWriter to open a file to write characters, pass it to constructor of BufferedOutputStream, then decorate it by a PrintWriter to use the formatted print method.

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;

public class WriterClass {
	public void writeFile(String filename) throws IOException {
		PrintWriter out = new PrintWriter(new BufferedWriter(new FileWriter(filename)));
		out.printf("Hello, %s!\n", "world");
		out.close();
	}
}
```

### The decorations are automatically done when you pass the file name directly to the PrintWriter construction.

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;

public class WriterClass {
	public void writeFile(String filename) throws IOException {
		PrintWriter out = new PrintWriter(filename);
		out.printf("Hello, %s!\n", "world");
		out.close();
	}
}
```

### Use FileOutputStream to open file to write bytes, buffer it by BufferedOutputStream and decorate it by DataOutputStream.

```java

import java.io.BufferedOutputStream;
import java.io.DataOutputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class StreamClass {
	public void writeFile(String filename) throws IOException {
		DataOutputStream out = new DataOutputStream(
				new BufferedOutputStream(new FileOutputStream(filename)));
		out.writeInt(1);
		out.writeDouble(2.1);
		out.writeUTF("Hello");
		out.close();
	}
}
```

### To readLine() from standard input, first use InputStreamReader to convert this InputStream to Reader, then decorate it by BufferedReader.

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class StandardIO {
	public void readLineFromStdin() {
		BufferedReader br = new BufferedReader(
				new InputStreamReader(System.in));
		String line;
		try {
			while(!(line = br.readLine()).equals("")) {
				System.out.println(line);
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

### Redirect System.stdin by System.setIn(InputStream).

```java
import java.io.BufferedInputStream;
import java.io.BufferedReader;
import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStreamReader;

public class StandardIO {
	public void redirectStdin() {
		BufferedInputStream in = new BufferedInputStream(
				new ByteArrayInputStream("Input string\nHello\n".getBytes()));
		System.setIn(in);
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		String line;
		try {
			while((line = br.readLine()) != null) {
				System.out.println(line);
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

> Redirect System.stdout and System.stderr by System.setOut(**PrintStream**) and System.setErr(**PrintStream**).

## Concurrency

### The most basic way to multithread is to write a class implementing Runnable, then put it in a Thread object to start.

```java
public class BasicThread implements Runnable {

	@Override
	public void run() {
		System.out.println("Hello world");
	}
	
	public static void test() {
		BasicThread basicThread = new BasicThread();
		Thread thread = new Thread(basicThread);
		thread.start();
	}
}
```

### Use Executor to manage the thread lifetime.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class BasicThread implements Runnable {

	@Override
	public void run() {
		System.out.println("Hello world");
	}
	
	public static void useExecutor() {
		ExecutorService exec = Executors.newCachedThreadPool();
		for(int i = 0; i < 5; i++)
			exec.execute(new BasicThread());
		exec.shutdown();
	}
}
```

> After shutdown, exec stops accepting new threads, and waits until all threads finish.


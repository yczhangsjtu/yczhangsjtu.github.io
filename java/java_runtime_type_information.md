---
title: Java Runtime Type Information
---

# Runtime Type Information

## A special class in Java is named `Class`, objects of this class represents the classes used in the program. They can be manipulated like normal objects.

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

## Class objects can be easily obtained by `ClassName.class`.

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

## Class objects can be specified to point to limited types of classes.

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

## If you put `?` in the `<>` the class object can point to any type of classes.

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

## To specify a class object to point to a specific type and any subtype, use `? extends ClassName`.

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

## Use `newInstance()` of a class object to get an instance of that class.

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

## Use `cast()` of a class object to perform a runtime cast.

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

## Use `classObject.isInstance` to replace `instanceof`.

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

## Use `classObject.isAssignableFrom(anotherClassObject)` to check if an object of the type of the class object passed in can be assigned to a reference of the type of this class object.


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

## Use the `classObject.getMethods()` and `classObject.getConstructors()` to obtain the methods and constructors of a class, which are of types `Method` and `Constructor` classes in `java.lang.reflect` package.

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

## Generate objects of a particular interface at runtime using `Proxy`.

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


---
title: Java Inner Classes
---

# Inner classes

## Inner classes have direct access to private members of enclosing class

```Java
public class InnerClassExample {
	private String test = "test";
	class InnerClass {
		public String get() {
			return test;
		}
	}
}
```

## An object of inner class can be declared by compund of enclosing class name and inner class name, but has to be instantiated by an instance of the enclosing class.

```Java
InnerClassExample innerClassExample = new InnerClassExample();
InnerClassExample.InnerClass innerClass = innerClassExample.new InnerClass();
```

> The reason for this is that an object of the inner class must have an implicit reference to an object of the enclosing class, which has to exist.

## Inner class field can shadow field in enclosing class of same name, refer to the shadowed field in enclosing class by `.this`

```Java
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

## Inner class can be declared in method, then it can be accessed only in that method

```Java
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

## Inner class can be declared anonymously

```Java
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

## Inner class cannot have static fields or methods, but static inner class (called nested class) can.

```Java
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

## Nested class does not have to be declared by an instance of the enclosing class.

```Java
InnerClassExample.StaticInnerClass staticInnerClass = new InnerClassExample.StaticInnerClass();
```

## Classes nested in interface is automatically static and public, the interface is just used as a namespace for the nested class.

```Java
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

## Nested class can be used to create classes having access to the fields of the enclosing class. This is particularly useful when implementing event system.

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

## To extend an inner class, the implicit referece to the enclosing class has to be explicitly instantiated.

```Java
class InheritInner extends InnerClassExample.InnerClass {
	public InheritInner(InnerClassExample innerClassExample) {
		innerClassExample.super();
	}
}
```


## After compilation, a `.class` file will be generated for the inner class, with name scheme `EnclosingClass$InnerClass.class`.

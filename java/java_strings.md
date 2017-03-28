---
title: Java Strings
---

# Java Strings

## Use string builder to accumulate strings.

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

## The method `format()` in `System.out` is equivalent to `printf()`.

```java

public class BasicStrings {
	public void useFormat() {
		System.out.format("Hello %s!\n", "World");
	}
}
```

## Use a Formatter to specify where the output goes.

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

## Use `String.format` to generate a string with format string.

```java
public class BasicStrings {
	public String useStringFormat() {
		return String.format("Hello %s!\n", "World");
	}
}
```

## Use `Pattern` and `Matcher` to perform regular expression matching.

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

## Use `Pattern.matches` to check if a string is an exact match of a regular expression.

```java
import java.util.regex.Pattern;

public class BasicStrings {
	public boolean usePatternMatches(String input) {
		return Pattern.matches(".*abcdef.*", input);
	}
}
```

## Each match found by `matcher.find()` could have more than one groups.

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

## After each successful match, `matcher.start()` and `matcher.end()` gives the start and end index of the matched portion in the entire string. Note that the `end` points to the first position after the matched portion.

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

## `pattern.split()` can be used to split strings by the regular expression represented by `pattern`.

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


## Use `string.replaceAll()` to replace all occurrences of a regular expression to something else.

```java
public class BasicStrings {
	public String useReplace(String input) {
		return input.replaceAll("\\b\\w+\\b", "TOKEN");
	}
}
```

## Use `matcher.appendReplacement()` and `matcher.appendTail()` to replace each occurrence by a different replacement, and accumulate  the replacements in a `StringBuffer`.

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

## Use `matcher.reset()` to set a matcher to match another string.

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

## Use `Scanner` to read a stream of input string and evaluate them into integers (or other types of variables).

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

## You can set a different delimiter for the scanner instead of the default white space.

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

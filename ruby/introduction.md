---
title: Introduction to Ruby
---

Like any other intepreted languages, to run ruby program, simply store
it in a .rb file, and execute
```ruby
    ruby filename.rb
```

However, unlike python, you cannot get an interactive interface by
executing ruby command without any command line argument. Instead, you
should use irb.
### Everything is Object

Everything in ruby is object. All the objects have methods, including
the integers. This feature is rarely seen in other objected-oriented
languages. For example, you can call methods on 1 like 1.next, which
returns 2. Note that there is no () after the method name, if no
argument is needed.\
\
In fact, if there are arguments, the parenthesis is still unnecessary.
Replacing the parenthesis with a space between the method name and the
argument list is okay.
### More Characters Allowed in Method Name

Try puts 1.methods to have a look at all the methods 1 can invoke. You
will find a lot of special characters in the methods name which would
cause the names to be unlegal in other languages.\
\
For example, 1.even? will return false since 1 is not an even integer.\
\
Note that all the operators on integers are considered as methods. And
both 1.+(2) and 1+2 are valid and will return 3.\
\
Another example is the \[\] method of an array. Both of the following
expressions are okay
```ruby
    ["zero","one","two"][1]

    ["zero","one","two"].[](1)
```

### String

String in ruby is nothing different from that in most languages like
python. They can be enclosed in either double or single quotes. And, as
you expect, the length method returns the length of the string.
```ruby
    "Sample string".length
```

The essential difference between using single or double quotes is that
double quotes allow for escape sequences while single quotes do not.
#### String Interpolation

It is essential to be able to replace placeholders within a string with
values they represent. In the programming paradigm, this is called
"string interpolation".\
\
In ruby, variable in a string enclosed with \#{} will be replaced by its
value.\
\
For example, the following code
```ruby
    a = 1
    b = 4
    puts "The number #{a} is less than #{b}."
```

will print "The number 1 is less than 4".\
\
This is another situation where the single quote is different from
double quote. Replace the double quote by single quote, what is printed
is exactly what is in the string.\
\
Of course, not only variables, but any expressions can be put in the
\#{}.
#### Search in a String

To see if a string contains a certain substring, use the include? method
of the string.\
\
Ruby is an intuitive language. That is, if you try to guess what method
of a string implements some functionality, like telling whether or not
it starts with a specific substring, the name of method is probably just
how you discribe the functionality, like start\_with?.\
\
Now guess what method of string tells you whether a string ends with
'ruby'?
#### Advanced String Operations

The use of split method is quite straightfoward.
```ruby
    'a b;c d'.split
```

will split the string by space and give \['a','b;c','d'\] as the answer.
While
```ruby
    'a b;c d'.split ';'
```

returns \['a b','c d'\].\
\
As for string concatenation, the two ways provided by ruby are both
intuitive.
```ruby
    'a'+'b'
    'a'.concat 'b'
```

However, the two ways are actually not equivalent. If you replace 'a' by
a string variable that equals 'a', the returned string will be the same
but the string variable will be updated into the concatenated string by
concat method. While the operator + does not change the string variable
itself. The operator that is equivalent to concat method is &lt;&lt;.\
\
To replace the first occurrence of a substring with another string, use
the sub method.
```ruby
    'I will go.'.sub 'I','We'
```

To replace all the occurences, use gsub instead.\
\
The first argument of these methods could be a regular expression.
#### Regular Expression

Regular expression in ruby is enclosed in double slash//. Note that
there is no quote.\
\
The details of regular expression are no different from those in most
programming languages.\
\
The match method will find the first occurrence of a substring specified
by the first argument, which could be a regular expression. If the
second argument is passed, the search will start from the position
specified by the second argument.\
\
The return value of match method is the substring itself.
### Logical Operators

The logical operators in ruby are just like those in C++.
### Control Flows in Ruby

#### Conditional Branch

The if statement is used as follows
```ruby
    if n > 0
        puts "#{n} is positive"
    elsif
        puts "#{n} is negative"
    else
        puts 0
    end
```

There is also an unless keyword which works equivalent to if !.\
\
The ternary operator ?: in C++ is also supported by ruby.\
\
In ruby, the objects false and nil equates to false. Every other objects
are all evaluted to true when used as boolean. Note that 0 is also true
contrary to that in C++.
#### Loops

An infinite loop is created as follows.
```ruby
    loop do
        puts "This will be printed for infinite many times."
    end
```

Such loop can be exited by break statement, just like in python.\
\
A way to repeat something for a specific number of times in ruby is
shown below
```ruby
    5.times do
        puts "This will be printed 5 times."
    end
```

The for loop is just like that in python.
```ruby
    for i in [1,2,3,4,5]
        puts i
    end
```

This is equivalent to
```ruby
    [1,2,3,4,5].each do |i|
        puts i
    end
```

### Arrays

An empty array is created either by \[\] or Array.new.\
\
You can create the array by enumerating the elements, like
```ruby
\[1,2,3,4,5\].\
```
\
Ruby variables are typeless. So the elements in an array can be a mix of
strings and integers.\
\
An element can be inserted into an array by either the push method or
the &lt;&lt; operator.\
\
The map method of an array creates another array whose elements are
obtained by mapping the elements of the original array. The map is
specified like in the following example
```ruby
    [1,2,3,4,5].map{|k| k*k}
```

which returns the array \[1,4,9,16,25\].\
\
Likewise, the method select filters the array by a boolean expression.
```ruby
    [1,2,3,4,5,6].select{|num| num.even?}
```

returns \[2,4,6\].\
\
The method delete\_if does exactly the opposite. Note there is another
difference in the two methods. a.select will not change the array
itself, while a.delete actually update the array.
### Hash

Hashes in ruby act as dictionaries. A hash is created as follows
```ruby
    age = {
        "Alice" => 10,
        "Bob" => 12,
        "Eve" => 14
    }
```

To retrieve the value from a hash, use the \[\] operator. This operator
can also be used to insert or alter values in a hash. If you try to
retrieve the value of some non-exist key, nil will be returned.\
\
The hash can also be iterated by the each method. However, it passes two
values instead of one as the iterator.
```ruby
    age.each do |name,number|
        puts "#{name} is #{number} years old."
    end
```

The keys and values methods return arrays of all the keys or values in
the hash respectively.\
\
An empty hash is created by {} or Hash.new. If you pass an argument to
the new method, it will be used as the default value for the hash. That
is, when you try to retrieve the value of a key that is not specified
before, the default value will be returned instead of nil.\
\
A hash can also be created by the following ways
```ruby
    age = Hash["Alice",10,"Bob",12,"Eve",14]
    age = Hash[[["Alice",10],["Bob",12],["Eve",14]]]
```

### Methods

Functions in ruby are called methods, no matter whether they belong to
some object. A method is defined like in the following example
```ruby
    def func(arg1,arg2)
        # Some code
        return something # This is optional
    end
```

If no argument is required, the parenthesis is optional. If no return
expression is provided, the last object evaluated is taken as the
returned value. If there is nothing in the function body, a nil will be
returned.\
\
The \* operator can transform an array into an argument list, as well as
in the opposite direction. For example
```ruby
    def sum(*nums)
        s = 0
        for i in nums
            s = s + i
        end
        s
    end

    puts sum(1,2,3)

    def max(a,b)
        if a > b
            a
        else
            b
        end
    end

    puts max(*[1,2])
```

Methods can get named options. For example
```ruby
    def add(a_number, another_number, options = {})
        sum = a_number + another_number
        sum = sum.abs if options[:absolute]
        sum = sum.round(options[:precision]) if options[:round]
        sum
    end

    puts add(1.0134, -5.568)
    puts add(1.0134, -5.568, absolute: true)
    puts add(1.0134, -5.568, absolute: true, round: true, precision: 2)
```

### Lambda Function

By convention, the body of the lambda function in ruby is enclosed in {}
if it takes only one line, or in a pair of do and end otherwise.\
\
Examples are given below.
```ruby
    l = lambda {|x| x*x}
    puts l.call 10 
    h = lambda do |n|
        if n > 1
            return n*(h.call n-1)
        else
            return 1
        end
    end
    puts l.call 10
```

### Classes

An important feature of classes in Ruby is that they too adhere to the
"everything is an object philosophy." This may be of interest to those
already familiar with C++ and similar languages where classes are
special constructs that cannot be interacted with like normal objects.\
\
The class method of an object will return the class it belongs to. A
class object also has such a method. A class object belongs to the class
Class.\
\
The new method of a class object creates an object that belongs to that
class.\
\
The following example shows how to define a class of your own
```ruby
    class Rectangle
        def initialize(length,breadth)
            @length = length
            @breadth = breadth
        end

        def perimeter
            2 * (@length+@breadth)
        end

        def area
            @length * @breadth
        end
    end
```

As you may have noted, the "states" (called attributes in other
object-oriented languages) in a class are prefixed with @.\
\
Since "everything is an object" in ruby, so are the methods of an
object. All objects in ruby has the method method, which returns any
method as an object, given the string specifying the method name. A
method object has the method call.
```ruby
    method_object = 1.method("next")
    puts method_object.call
```

### Libraries in Ruby

The keyword that corresponds to import in python is require. And the
library name should be put in a string.\
\
Two different libraries written by different people might define classes
of the same name. Then the class in the later loaded library will
override that in the previous library.\
\
To solve this problem, creators of the libraries would put all the
classes in the library in a namespace. Namespace in ruby is defined by
module.\
\
Classes in different namespaces are accessed via :: operator.
```ruby
    module Math
        class Complex
            def get
                "Math"
            end
        end
    end

    module Geom
        class Complex
            def get
                "Geom"
            end
        end
    end

    mcomp = Math::Complex.new
    gcomp = Geom::Complex.new
    puts mcomp.get
    puts gcomp.get
```

Variable prefixed with :: and nothing else is scoped in the topmost
level.
### Streams and the IO Class

A file descriptor in ruby is created by the sysopen method of IO class.
For example
```ruby
    fd = IO.sysopen("newfd","w")
```

An I/O stream can be created from the file descriptor.
```ruby
    io_stream = IO.new(fd)
```

Originally there are several I/O streams created by the interpreter,
including the STDIN and STDOUT.
### The File Class

The open method of the File object opens a file and return a file
object.
```ruby
    file = File.open("filename","r+")
    puts file.read
    file.close
```

The method can also take a block and in the end of the block the file
will be closed automatically.
```ruby
    File.open("filename","r+") do |f|
        puts f.read
    end
```

The read method could take arguments length and buffer, indicating how
much to read and store the content read in a buffer.\
\
You can directly call the read method of File to get the content of a
file. Or the readlines method which splits the file content into array
of lines.

---
title: Ruby Blocks
---

# Ruby Blocks

## Diving into blocks

Blocks are often the first Ruby-ism that new Rubyists run into, and are
confused by. What are blocks? Why do they exist? How do I use them?

I'll do my best to answer all these questions and more in this lesson.
## What are blocks?

Blocks aren't unique to Ruby. The official (language agnostic)
definition of blocks is "A section of code which is grouped together."
Of course, I'm guessing this doesn't help you much.

A simpler way to describe blocks is "A block is code that you can store
in a variable like any other object and run on demand."

Let me help you build a mental model for this by showing you some code,
then refactoring it to a block in Ruby. We can start by writing some
code that does something trivial, but meaningful.
```ruby
    puts 5+6
```

Sweet. That works! However, this only covers the first part of the
definition - it's a section of code. It isn't "grouped together" though,
nor is it stored in a variable.

Let's work on it some more to make it more generic before we "group it."
```ruby
    a = 5
    b = 6
    puts a+b
```

Great. That works - we've replaced the numbers with variables. The code
that does the addition, however, is still not stored in a variable.

Let's make it happen!
```ruby
    addition = lambda {|a,b| return a+b}
    puts addition.call(5,6)
```

And there you have it all nice and grouped - a block!

The 'lambda' keyword is what is most commonly used to create a block in
Ruby. There are other ways to do it, but lets keep things simple for
now.

At this point if you're thinking "Wait, that looks pretty much like a
method, except there's no class or object" then you're absolutely right.
Try thinking of it like this: A block is like a method, but one that
isn't associated with any object.

Let's examine blocks more closely.

Are blocks objects? Yes, like almost everything else in Ruby, they are.
```ruby
    empty_block = lambda { }
    puts empty_block.object_id
    puts empty_block.class
    puts empty_block.class.superclass
```

As you can see, the block we just created has an object\_id, belongs to
the class Proc (which is what a block is called in Ruby), which is
itself a subclass of Object.

We can even flip the definition around and define methods in terms of
blocks. A method is simply a block bound to an object, with access to
the object's state.

Let me illustrate the idea by reverse engineering a Ruby method to
produce a block. Here's a more traditional approach to the previous
problem (and please forgive my crappy object modeling):
```ruby
    class Calculator
        def add(a,b)
            return a+b
        end
    end

    puts Calculator.new.add(5,6)
```

There, that works as you'd expect. Now lets take it further.
```ruby
    class Calculator
        def add(a,b)
            return a+b
        end
    end

    addition_method = Calculator.new.method("add")
    addition = addition_method.to_proc

    puts addition.call(5,6)
```

And there you have it - a regular, old fashioned method converted to a
fancy-pants block!
## Yielding to Blocks

As you use blocks, you will discover that the most common usage involves
passing exactly one block to a method. This pattern is extremely popular
in the Ruby world, and you'll find yourself using it all the time.

Ruby has a special keyword and syntax to make this pattern easier to
use, and yes, faster! Meet the yield keyword, Ruby's implementation of
the most common way of using blocks.

Here's an example that uses regular block syntax.
```ruby
    def calculation(a, b, operation)
        operation.call(a, b)
    end

    puts calculation(5, 6, lambda { |a, b| a + b }) # addition
    puts calculation(5, 6, lambda { |a, b| a - b }) # subtraction
```

As you can see, the calculation method accepts two numbers and a block
that can perform a mathematical operation.

Let's now do this using yield:
```ruby
    def calculation(a, b)
        yield(a, b)
    end

    puts calculation(5, 6) { |a, b| a + b } # addition
    puts calculation(5, 6) { |a, b| a - b } # subtraction
```

As you can see, the results are identical. Feel free to play around with
the example to get a better feel for the new syntax.

Let me call out how the example using yield is different from the
regular approach.
-   The block is now no longer a parameter to the method. The block has
    been implicitly passed to the method - note how it's outside the
    parentheses.
-   Yield makes executing the block feel like a method invocation within
    the method invocation rather than a block that's being explicitly
    called using Proc\#call.
-   You have no handle to the block object anymore - yield "magically"
    invokes it without any object references being involved.

Note that blocks can be passed implicitly to methods without any
parameters. The syntax remains the same.

Here's an example where neither the method nor the block take any
parameters.
```ruby
    def foo
        yield
    end
    foo { puts "sometimes shortcuts do get you there faster"  }
```

## Magic Blocks

I call yield "magical" because every object oriented rule in Ruby is
suspended for this special mode of block invocation.

Let's see what rules are bent, and what broken.

1) Yield is not a method
```ruby
    def foo
        puts yield
        puts method(:foo)
        # uncomment the following line and see what happens! 
        # puts method(:yield)
    end

    foo { "I expect to be heard." }
```

As you can see, the program blows up with an exception on the last line
in the method. Clearly yield calls the block, the method method
correctly returns the object that represents foo as expected, but then
blows up on yield. So yield isn't really a method even though it looks
like one (turns out, it's a keyword).

2) Objects are abandoned

Everything in Ruby is an object. Now where's the object that represents
the block? How is yield getting access to it and seemingly invoking the
call method on it?

We don't know. As programmers using the language, all we can tell is
that the normal rules have been suspended.

We'll see exactly why all these rules are broken later in this lesson.

With implicit blocks, it's hard to be sure if a block has actually
passed. Doing a yield when there's no block can have unfortunate
consequences - an exception (a LocalJumpError) with the message "no
block given" is raised .
```ruby
    def foo
        yield
    end

    foo
```

To defend against this outcome, Ruby offers the block\_given? method
that tells you if a block has been passed to a method implicitly. Make
the call to yield conditional on this method returning true and you'll
```ruby
be fine.
    def foo
        yield if block_given?
    end
```

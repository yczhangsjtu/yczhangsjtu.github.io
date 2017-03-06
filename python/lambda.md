---
title: Lambda Function in Python
---

# Lambda Function in Python

Simply speaking, a lambda function is a function object.
In fact, in python, all functions are objects.
They can even be assigned to a variable.
For example, you can type
```
f = sum
```
to assign the built-in function `sum` to variable `f`.


You can define a function using `def` and then assign this function to another variable.
In this sense, a lambda function is no different from an ordinary function.


However, if you need to use a function only once, you still have to come up with a name for it.
Lambda function is special in that it is created on the fly.
It is kind of expression that returns an object which is of function type.
You do not need to come up with a name for it.


Think about the case when you need a function in a loop, whose behavior depends on the loop iterator.
You would have to define `f1(),f2(),...` for each iteration.
The problem is, this is just impossible. And if you do define the functions like this,
it makes no sense to use the loop at all.


The usage of Lambda function is very simple.

```python
square = lambda x: x*x
print square(5)
add = lambda a,b: a+b
print add(6,8)
print (lambda x: x*x*x)(3)
```

## Interesting Topic about Lambda Function

Tell me what is the output of the following code.

```python
(lambda a:lambda v: a(a,v))(lambda g,n: 1 if n<3 else g(g,n-1)+g(g,n-2))(10)
```

First, you have to make clear the structure of this expression.
It consists of three parts, each in a pair of parenthesis.
The first two parts are lambda functions, and the third part is a number.
The first lambda function takes the second lambda function as argument,
and returns a lambda function, which takes the number as argument.


Let's first make out what does the second lambda function do.
It takes two arguments, the first of which is a function, and the second is a number.
Note that this is a way to use lambda function recursively:
lambda function does not have a name, so how to call them recursively?
Easy. We just add another argument as the function name, and pass it to itself.
For example, for the factorial function *f(n)=nf(n-1)*,
we create the following lambda function

```python
lambda f,n: n*f(f,n-1)
```

So, what if we assign it to a variable, and put the variable itself as its own argument?

```python
g = lambda f,n: n*f(f,n-1)
print g(g,10)
```

What is *g(g,10)*? Well, by the definition, it equals *10g(g,9)*. Clear now?
Of course, the definition here is wrong, since there is no check for exit criteria,
and it will cause trouble by inducing an infinite recursion.
This is just for simplicity.
To make it really works, you have to check the argument *n*.
```python
g = lambda f,n: 1 if n==0 else n*f(f,n-1)
```
Then if you want to calculate factorial, just use `g(g,n)` everywhere.

Wait...this does not seem perfect.
What a weird function it looks like, when calling it requires passing itself!
Well, this is what the first lambda function aims to solve.


Before looking at the first lambda function, think about the following problem.
Can you see how the following two lambda functions can do the same work?
```python
lambda f: lamdba x: f(f,x)
lambda g,x: g(g,x)
```
Well, if you name the first function by `A`,
and the second by `B`, it is easy to see that
`A(f)(x) = B(f,x)` always holds.
In fact, they are all equal to `f(f,x)`.
The logic is, `A` maps `f` to a function,
which then maps `x` to `f(f,x)`,
while `B` does this work more directly.


Now it should be clear what is the use of the first lambda function in the original problem?
It just combines a function `g` and a number `n`,
returning `g(g,n)`.
This is exactly what we want to achieve.
Now we apply `A` to the function `g`,
then we get a function which maps `n` to `g(g,n)`,
which is actually equal to *n!*.


Then it should be easy to figure out that the lambda function in the original problem is actually the definition of Fibonacci series.




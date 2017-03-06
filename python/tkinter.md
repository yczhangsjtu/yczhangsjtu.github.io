---
title: Tkinter - Built-in Graphic Library
---

To use Tkinter, import the Tkinter library as follows.
```python
    from Tkinter import *
```

First we need to initialize it by
```python
    root = Tk()
```

Then ask the Tk object to start a loop, waiting for the user input.
```python
    root.mainloop()
```

The loop must be started after all other preparations are ready.

## Commonly Used Controls

### Entry

A small box to get text input from the user.
```python
    s = StringVar()
    e = Entry(root,textvariable=s) # Build an entry and set s as the string variable
    e.pack() # Put the entry on the board
```

One way to automatically revoke some callback function when the text is
changed is through the vcmd option.
```python
    def onchange(typeofchange,new_value):
        if typeofchange == 1:
            print "Text inserted, result is %s"%new_value
        elif typeofchange == 0:
            print "Text deleted, result is %s"%new_value

    callback = root.register(onchange)
    entry = Entry(root,validate="key",vcmd=(callback,'%d','%P'))
```

Here the validate option can also take value "focus", "focusin",
"focusout", "all", "none".\
\
The arguments passed to the callback function can be any combination of
'%d', '%i', '%P', '%s', '%S', '%v', '%V', '%W', which denotes **type of
events**, **index of inserted text**, **the updated text**, **the
original text**, current value of **validate** option, **reason for this
callback**, **name of the widget**, respectively.
### Button

The button is usually initialized with text shown printed on it, as well
as the command to execute when the button is pressed.
```python
    b = Button(root,text="Start",command=func)
```

where func is a function name defined elsewhere.
## Layout Management

The most basic method to manipulate the layout of the controls is to
pass arguments to the pack method.
```python
    e = Entry(root)
    e.pack(side=LEFT)
    b = Button(root,text="Button")
    b.pack(side=RIGHT)
```

Here the side argument can only take values LEFT,RIGHT,TOP,BOTTOM.\
\
It would be convenient to divide entries into several groups, deploy the
controls in each group, and manipulate the position of the groups as a
whole. For this purpose the Frame control can be used.
```python
    f1 = Frame(root)
    f2 = Frame(root)
    b1 = Button(f1)
    e1 = Entry(f1)
    b2 = Button(f2)
    e2 = Entry(f2)
    b1.pack(side=LEFT)
    e1.pack(side=RIGHT)
    b2.pack(side=LEFT)
    e2.pack(side=RIGHT)
    f1.pack(side=TOP)
    f2.pack(side=BOTTOM)
```

### Grid Layout

You can use grid instead of pack. The meaning of the layout by grid
method is straightforward.
```python
    b1 = Button(root)
    b2 = Button(root)
    b3 = Button(root)
    b4 = Button(root)
    b1.grid(row=0,column=0)
    b2.grid(row=0,column=1)
    b3.grid(row=1,column=0)
    b4.grid(row=1,column=1)
```

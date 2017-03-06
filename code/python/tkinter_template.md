---
title: Tkinter Template
---

```python
from Tkinter import *

root = Tk()

rframe = Frame(root)
tframe = Frame(root)
bframe = Frame(root)
entry = Entry(bframe,text="Entry")
button = Button(bframe,text="Button")
text = Text(rframe)
label = Label(rframe,text="Label")
canvas = Canvas(tframe)
check = Checkbutton(tframe,text="Check Box")

rframe.pack(side=RIGHT)
tframe.pack(side=TOP)
bframe.pack(side=LEFT)

entry.pack(side=TOP)
button.pack(side=BOTTOM)
text.pack(side=TOP)
label.pack(side=BOTTOM)
canvas.pack(side=TOP)
check.pack(side=BOTTOM)

canvas.create_rectangle(20,20,300,300,fill="red")
canvas.create_text(30,30,text="Canvas",anchor=NW,fill="blue")
entry.insert(0,"Entry")

root.mainloop()
```

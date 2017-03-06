---
title: PyOpenGL Template
---

```python
import sys
from OpenGL.GL import *
from OpenGL.GLU import *
from OpenGL.GLUT import *

sclLeft = -2.0
sclRight = 2.0
sclBottom = -2.0
sclTop = 2.0
sclNear = -10.0
sclFar = 10.0

eyex = 3.0
eyey = 3.0
eyez = 3.0
# The display() method does all the work it has to call the appropriate
# OpenGL functions to actually display something.
def display():
    w, h = winw, winh
    # Clear the color and depth buffers
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)
################################################################################

    # Initialize 2d environment
    glMatrixMode(GL_PROJECTION)
    glLoadIdentity()
    if (w <= h):
        gluOrtho2D(sclLeft, sclRight, sclBottom * float(h) / float(w),\
            sclTop * float(h) / float(w))
    else:
        gluOrtho2D(sclLeft * float(w) / float(h),\
            sclRight * float(w) / float(h), sclBottom, sclTop)

    glMatrixMode(GL_MODELVIEW)
    glLoadIdentity()
    glViewport(0, 0, w, h)

    # Draw 2d picture
    glColor3f(1.0,1.0,1.0)
    glBegin(GL_QUADS)
    glVertex2f(sclLeft,sclBottom)
    glVertex2f(sclRight,sclBottom)
    glVertex2f(sclRight,sclTop)
    glVertex2f(sclLeft,sclTop)
    glEnd()
################################################################################

    # Initialize 3d environment
    glMatrixMode(GL_PROJECTION)
    glLoadIdentity()
    if (w <= h):
        glOrtho(sclLeft, sclRight, sclBottom * float(h) / float(w), sclTop *\
        float(h) / float(w), sclNear, sclFar)
    else:
        glOrtho(sclLeft * float(w) / float(h), sclRight * float(w) / float(h),\
        sclBottom, sclTop, sclNear, sclFar)

    glMatrixMode(GL_MODELVIEW)
    glLoadIdentity()
    glViewport(0, 0, w, h)
    gluLookAt(eyex, eyey, eyez, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0)
    
    # Draw 3d picture
    glColor3f(1.0,0.0,0.0)
    glBegin(GL_QUADS)
    glVertex3f(0.0, 0.0, 0.0)
    glVertex3f(0.0, 1.0, 0.0)
    glVertex3f(1.0, 1.0, 0.0)
    glVertex3f(1.0, 0.0, 0.0)
    glEnd()
    glColor3f(0.0,1.0,0.0)
    glBegin(GL_QUADS)
    glVertex3f(0.0, 0.0, 0.0)
    glVertex3f(0.0, 1.0, 0.0)
    glVertex3f(0.0, 1.0, 1.0)
    glVertex3f(0.0, 0.0, 1.0)
    glEnd()
    glColor3f(0.0,0.0,0.0)
    glBegin(GL_QUADS)
    glVertex3f(0.0, 0.0, 0.0)
    glVertex3f(1.0, 0.0, 0.0)
    glVertex3f(1.0, 0.0, 1.0)
    glVertex3f(0.0, 0.0, 1.0)
    glEnd()
    glColor3f(0.0,1.0,1.0)
    glBegin(GL_QUADS)
    glVertex3f(1.0, 1.0, 1.0)
    glVertex3f(1.0, 0.0, 1.0)
    glVertex3f(0.0, 0.0, 1.0)
    glVertex3f(0.0, 1.0, 1.0)
    glEnd()
    glColor3f(1.0,0.0,1.0)
    glBegin(GL_QUADS)
    glVertex3f(1.0, 1.0, 1.0)
    glVertex3f(1.0, 1.0, 0.0)
    glVertex3f(0.0, 1.0, 0.0)
    glVertex3f(0.0, 1.0, 1.0)
    glEnd()
    glColor3f(1.0,1.0,0.0)
    glBegin(GL_QUADS)
    glVertex3f(1.0, 1.0, 1.0)
    glVertex3f(1.0, 0.0, 1.0)
    glVertex3f(1.0, 0.0, 0.0)
    glVertex3f(1.0, 1.0, 0.0)
    glEnd()
################################################################################

    
    # Copy the off-screen buffer to the screen.
    glutSwapBuffers()


def reshape(w,h):
    global winw, winh
    winw, winh = w, h
    pass


glutInit(sys.argv)

# Create a double-buffer RGBA window.
# (Single-buffering is possible.
# So is creating an index-mode window.)
glutInitDisplayMode(GLUT_RGBA | GLUT_DOUBLE | GLUT_DEPTH)

# Create a window, setting its title
glutCreateWindow('interactive')

def keyboard(keyCode,x,y):
    global eyex, eyey
    if keyCode == "a":
        eyex += 0.1
    elif keyCode == "d":
        eyex -= 0.1
    elif keyCode == "w":
        eyey += 0.1
    elif keyCode == "s":
        eyey -= 0.1
    glutPostRedisplay()
# Set the display callback.
# You can set other callbacks for keyboard and mouse events.
glutDisplayFunc(display)
glutReshapeFunc(reshape)
glutKeyboardFunc(keyboard)

# Run the GLUT main loop until the user closes the
# window.
glutMainLoop()
```

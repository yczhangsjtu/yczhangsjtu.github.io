---
title: OpenGL 2D Template (Stretch with Window)
---

```c++
#include <GL/freeglut.h>
#include <cstdlib>

GLfloat sclLeft = -2.0;
GLfloat sclRight = 2.0;
GLfloat sclBottom = -2.0;
GLfloat sclTop = 2.0;


void display()
{
    glClear(GL_COLOR_BUFFER_BIT|GL_DEPTH_BUFFER_BIT);

    glColor3f(1.0,0.0,0.0);
    glBegin(GL_QUADS);
    glVertex2f(0.0,0.0);
    glVertex2f(0.0,1.0);
    glVertex2f(1.0,1.0);
    glVertex2f(1.0,0.0);
    glEnd();

    glutSwapBuffers();
}

void reshape(int w, int h)
{
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(sclLeft, sclRight, sclBottom, sclTop);
    glMatrixMode(GL_MODELVIEW);
    glViewport(0, 0, w, h);
}

void keyboard(unsigned char keyCode, int x, int y)
{
}

void keyboardUp(unsigned char keyCode, int x, int y)
{
}

void special(int keyCode, int x, int y)
{
}

void specialUp(int keyCode, int x, int y)
{
}

void mouse(int btn, int state, int x, int y)
{
}

void menu(int id)
{
    switch(id)
    {
    case 0:
        std::exit(0);
    }
}

void myinit()
{
    glClearColor(1.0, 1.0, 1.0, 1.0);

}

void idle()
{
}

int main(int argc, char *argv[])
{
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB | GLUT_DEPTH);
    glutInitWindowSize(1366, 750);
    glutInitWindowPosition(0, 0);
    glutCreateWindow("Window");
    myinit();
    glutDisplayFunc(display);
    glutReshapeFunc(reshape);
    glutKeyboardFunc(keyboard);
    glutKeyboardUpFunc(keyboardUp);
    glutSpecialFunc(special);
    glutSpecialUpFunc(specialUp);
    glutMouseFunc(mouse);
    glutCreateMenu(menu);
    glutAddMenuEntry("Quit", 0);
    glutAttachMenu(GLUT_RIGHT_BUTTON);
    glutIdleFunc(idle);
    glutMainLoop();
}
```


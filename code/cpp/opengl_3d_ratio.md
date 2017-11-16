---
title: OpenGL 3D Template (Keep Ratio)
---

```C++
#include <GL/freeglut.h>
#include <cstdlib>

GLfloat sclLeft = -2.0;
GLfloat sclRight = 2.0;
GLfloat sclBottom = -2.0;
GLfloat sclTop = 2.0;
GLfloat sclNear = -10.0;
GLfloat sclFar = 10.0;

GLfloat eyex = 3.0;
GLfloat eyey = 3.0;
GLfloat eyez = 3.0;

void display()
{
    glClear(GL_COLOR_BUFFER_BIT|GL_DEPTH_BUFFER_BIT);

    glMatrixMode(GL_MODELVIEW);
    glLoadIdentity();
    gluLookAt(eyex, eyey, eyez, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0);

    glBegin(GL_QUADS);
    glVertex3f(0.0, 0.0, 0.0);
    glVertex3f(0.0, 1.0, 0.0);
    glVertex3f(1.0, 1.0, 0.0);
    glVertex3f(1.0, 0.0, 0.0);
    glEnd();
    glBegin(GL_QUADS);
    glVertex3f(0.0, 0.0, 0.0);
    glVertex3f(0.0, 1.0, 0.0);
    glVertex3f(0.0, 1.0, 1.0);
    glVertex3f(0.0, 0.0, 1.0);
    glEnd();
    glBegin(GL_QUADS);
    glVertex3f(0.0, 0.0, 0.0);
    glVertex3f(1.0, 0.0, 0.0);
    glVertex3f(1.0, 0.0, 1.0);
    glVertex3f(0.0, 0.0, 1.0);
    glEnd();
    glBegin(GL_QUADS);
    glVertex3f(1.0, 1.0, 1.0);
    glVertex3f(1.0, 0.0, 1.0);
    glVertex3f(0.0, 0.0, 1.0);
    glVertex3f(0.0, 1.0, 1.0);
    glEnd();
    glBegin(GL_QUADS);
    glVertex3f(1.0, 1.0, 1.0);
    glVertex3f(1.0, 1.0, 0.0);
    glVertex3f(0.0, 1.0, 0.0);
    glVertex3f(0.0, 1.0, 1.0);
    glEnd();
    glBegin(GL_QUADS);
    glVertex3f(1.0, 1.0, 1.0);
    glVertex3f(1.0, 0.0, 1.0);
    glVertex3f(1.0, 0.0, 0.0);
    glVertex3f(1.0, 1.0, 0.0);
    glEnd();

    glutSwapBuffers();
}

void reshape(int w, int h)
{
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();

    if (w <= h)
        glOrtho(sclLeft, sclRight, sclBottom * (GLfloat) h / (GLfloat) w,
                sclTop * (GLfloat) h / (GLfloat) w, sclNear, sclFar);
    else
        glOrtho(sclLeft * (GLfloat) w / (GLfloat) h,
                sclRight * (GLfloat) w / (GLfloat) h, sclBottom, sclTop, sclNear, sclFar);

    glMatrixMode(GL_MODELVIEW);
    glViewport(0, 0, w, h);
}

void keyboard(unsigned char keyCode, int x, int y)
{
    switch(keyCode)
    {
    case 'a':
        eyex += 0.1;
        break;
    case 'd':
        eyex -= 0.1;
        break;
    case 'w':
        eyez += 0.1;
        break;
    case 's':
        eyez -= 0.1;
        break;
    }
    glutPostRedisplay();
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
    GLfloat mat_specular[]= {1.0, 1.0, 1.0, 1.0};
    GLfloat mat_diffuse[]= {1.0, 1.0, 1.0, 1.0};
    GLfloat mat_ambient[]= {1.0, 1.0, 1.0, 1.0};
    GLfloat mat_shininess= {100.0};
    GLfloat light_ambient[]= {0.0, 0.0, 0.0, 1.0};
    GLfloat light_diffuse[]= {1.0, 0.0, 0.0, 1.0};
    GLfloat light_specular[]= {1.0, 1.0, 1.0, 1.0};
    GLfloat light_position[]= {10.0, 10.0, 10.0, 0.0};

    glLightfv(GL_LIGHT0, GL_POSITION, light_position);
    glLightfv(GL_LIGHT0, GL_AMBIENT, light_ambient);
    glLightfv(GL_LIGHT0, GL_DIFFUSE, light_diffuse);
    glLightfv(GL_LIGHT0, GL_SPECULAR, light_specular);

    glMaterialfv(GL_FRONT, GL_SPECULAR, mat_specular);
    glMaterialfv(GL_FRONT, GL_AMBIENT, mat_ambient);
    glMaterialfv(GL_FRONT, GL_DIFFUSE, mat_diffuse);
    glMaterialf(GL_FRONT, GL_SHININESS, mat_shininess);

    glEnable(GL_LIGHTING);
    glEnable(GL_LIGHT0);

    glClearColor(0.0, 0.0, 0.0, 0.0);
    glEnable(GL_DEPTH_TEST);
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


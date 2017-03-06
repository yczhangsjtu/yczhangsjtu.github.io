---
title: Convex Hull
---

```C++
#include <GL/freeglut.h>
#include <cstdlib>
#include <vector>
#include <cassert>


GLfloat sclLeft = -2.0;
GLfloat sclRight = 2.0;
GLfloat sclBottom = -2.0;
GLfloat sclTop = 2.0;

using namespace std;

typedef struct
{
    double x;
    double y;
} Point;

Point point(double x, double y)
{
    Point p = {x,y};
    return p;
}

vector<Point> points;
vector<Point> ls;
typedef vector<Point>::iterator Iter;

double Random(double a, double b)
{
    return rand()/(float)RAND_MAX * (b-a)+a;
}

void swap(Iter a, Iter b)
{
    Point tmp = *a;
    *a = *b;
    *b = tmp;
}

bool smaller(Point a, Point b)
{
    if(a.x < b.x) return true;
    else if(a.x == b.x) return a.y < b.y;
    return false;
}

Iter choosePiv(Iter left, Iter right)
{
    return left + rand() % (right-left);
}

Iter partition(Iter piv, Iter left, Iter right)
{
    assert(right > left && right > piv && piv >= left);
    swap(piv,left);
    piv = left;
    Iter j = left + 1;
    for(Iter i = left + 1; i < right; i++)
    {
        if( smaller(*i,*piv) )
        {
            swap(i,j);
            j++;
        }
    }
    swap(piv,j-1);
    return j-1;
}

void quicksort(Iter left, Iter right)
{
    if(left >= right-1) return;
    Iter piv = choosePiv(left,right);
    piv = partition(piv,left,right);
    quicksort(left,piv);
    quicksort(piv+1,right);
}

bool rightturn(Point a, Point b, Point c)
{
    double x1 = b.x - a.x;
    double y1 = b.y - a.y;
    double x2 = c.x - b.x;
    double y2 = c.y - b.y;
    if(x1 == 0 && y1 == 0) return false;
    if(x2 == 0 && y2 == 0) return false;
    double d = x1*y2 - x2*y1;
    return d <= 0;
}

void convexHull(vector<Point> ps, vector<Point>& rs)
{
    if(ps.size() < 3) return;
    quicksort(ps.begin(),ps.end());
    vector<Point> up;
    vector<Point> down;
    up.push_back(ps.at(0));
    down.push_back(ps.back());
    for(int i = 1; i < ps.size(); i++)
    {
        up.push_back(ps.at(i));
        while(up.size() > 2)
        {
            if(rightturn(*(up.end()-3),*(up.end()-2),*(up.end()-1)))
                break;
            *(up.end()-2) = up.back();
            up.pop_back();
        }
    }
    for(int i = ps.size()-1; i >= 0; i--)
    {
        down.push_back(ps.at(i));
        while(down.size() > 2)
        {
            if(rightturn(*(down.end()-3),*(down.end()-2),*(down.end()-1)))
                break;
            *(down.end()-2) = down.back();
            down.pop_back();
        }
    }
    rs.clear();
    for(int i = 0; i < up.size(); i++)
        rs.push_back(up.at(i));
    for(int i = 0; i < down.size(); i++)
        rs.push_back(down.at(i));
}

void display()
{
    glClear(GL_COLOR_BUFFER_BIT|GL_DEPTH_BUFFER_BIT);

    glColor3f(0.0,0.0,0.0);
    glPointSize(3.0);
    glBegin(GL_POINTS);
    for(int i = 0; i < points.size(); i++)
    {
        glVertex2f(points.at(i).x,points.at(i).y);
    }
    glEnd();
    glBegin(GL_LINE_LOOP);
    for(int i = 0; i < ls.size(); i++)
    {
        glVertex2f(ls.at(i).x,ls.at(i).y);
    }
    glEnd();

    glutSwapBuffers();
}

void reshape(int w, int h)
{
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    if (w <= h)
        gluOrtho2D(sclLeft, sclRight, sclBottom * (GLfloat) h / (GLfloat) w,
                   sclTop * (GLfloat) h / (GLfloat) w);
    else
        gluOrtho2D(sclLeft * (GLfloat) w / (GLfloat) h,
                   sclRight * (GLfloat) w / (GLfloat) h, sclBottom, sclTop);

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
    for(int i = 0; i < 50; i++)
        points.push_back(point(Random(-2.0,2.0),Random(-2.0,2.0)));
    convexHull(points,ls);
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

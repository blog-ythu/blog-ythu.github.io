---
layout: post
comments: true
title: "OpenGL sumup"
excerpt: ""
date: 2011-10-09 11:00:00
mathjax: true
---

[TOC]

## Setup
Please refer to [here](https://bitbucket.org/herohuyongtao/opengl-setup) on how to setup OpenGL.

## 原则
`RenderScene()` 函数里面只放绘制的东西, 计算的所有代码独立出去, 保证速度!

## 加速显示
用显示列表, 把要绘制的语句写进显示列表, 然后只需要在 `RenderScene()` 中调用显示列表就行了, 如下:

```cpp
// create one display list
GLuint index = glGenLists(1);

// compile the display list, store a triangle in it
glNewList(index, GL_COMPILE);
glBegin(GL_TRIANGLES);
glVertex3fv(v0);
glVertex3fv(v1);
glVertex3fv(v2);
glEnd();
glEndList();

// draw the display list
glCallList(index);

// delete it if it is not used any more
glDeleteLists(index, 1);
```

## `glPushMatrix(); … glPopMatrix();`使用说明
这对语句使用目的是保存当前的View Matrix, 以后中间的代码会出现类似 `glRotatef; glTranslatef; glScalef;` 等改变View Matrix 的语句.

## 3D世界坐标转换成2D屏幕坐标方法
`beginWinCoords()`方法3D->2D, `endWinCoords()`方法2D->3D, 这两个方法直接的所有代码都会按2D屏幕绘制, 不会被3D干扰, 也不会有闪烁.

```cpp
void COpenGLView::beginWinCoords(void)
{
    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glLoadIdentity();
    glTranslatef(0.0, winHeight - 1, 0.0);
    glScalef(1.0, -1.0, 1.0);
    glMatrixMode(GL_PROJECTION);
    glPushMatrix();
    glLoadIdentity();
    glOrtho(0, winWidth, 0, winHeight, -1, 1);
    glMatrixMode(GL_MODELVIEW);
}

void COpenGLView::endWinCoords(void)
{
    glMatrixMode(GL_PROJECTION);
    glPopMatrix();
    glMatrixMode(GL_MODELVIEW);
    glPopMatrix();
}
```

## 往2D屏幕上打印TXT
```cpp
void *m_font;
m_font = (void *) GLUT_BITMAP_9_BY_15;
void COpenGLView::printTo2DWindow(int x, int y, const char * s, float * color)
{
    beginWinCoords();
    glColor3f(1,1,1);
    glPrint(x, y, s, m_font);
    endWinCoords();
}
void COpenGLView::glPrint(int x, int y, const char * s, void * font)
{
    glRasterPos2f(x, y);
    int len = (int) strlen(s);
    for (int i = 0; i < len; i++)
    {
        glutBitmapCharacter(font, s[i]);
    }
}
```

## 绘制带边框Border的图形
例如绘制带边框的四边形, 方法如下:

```cpp
glEnable(GL_POLYGON_OFFSET_FILL);
glPolygonOffset(1.0, 1.0);
... ...

// draw the polygon as normal
/* e.g. :
    glBegin(GL_POLYGON);
    glVertex3f(x+0.5, y+0.5, z-0.5);
    glVertex3f(x+0.5, y+0.5, z+0.5);
    glVertex3f(x+0.5, y-0.5, z+0.5);
    glVertex3f(x+0.5, y-0.5, z-0.5);
    glEnd();
*/
glDisable(GL_POLYGON_OFFSET_FILL);
glColor3v(yourColor); //Color for your polygon border
glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);
... ...

// draw the polygon with same procedure, as in above
glPolygonMode(GL_FRONT_AND_BACK, GL_FILL);
```

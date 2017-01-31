---
title: OpenGL入门学习系列集(三)
tags:
  - OpenGL
  - class
categories:
  - OpenGL
date: 2016-03-24 10:49:50
---



转载/参考自: [OpenGL入门学习](http://www.cnblogs.com/crazyxiaom/articles/2073586.html "OpenGL入门学习")

包括列表显示、数组的介入。

<!--more-->
### OpenGL入门学习[八] 列表显示
我们已经知道，使用OpenGL其实只要调用一系列的OpenGL函数就可以了。然而，这种方式在一些时候可能导致问题。比如某个画面中，使用了数千个多边形来表现一个比较真实的人物，OpenGL为了产生这数千个多边形，就需要不停的调用 glVertex* 函数，每一个多边形将至少调用三次（因为多边形至少有三个顶点），于是绘制一个比较真实的人物就需要调用上万次的glVertex* 函数。更糟糕的是，如果我们需要每秒钟绘制60幅画面，则每秒调用的glVertex* 函数次数就会超过数十万次，乃至接近百万次。这样的情况是我们所不愿意看到的。
同时，考虑这样一段代码：
```cpp
const int segments = 100;
const GLfloat pi = 3.14f;
int i;
glLineWidth(10.0);
glBegin(GL_LINE_LOOP);
for(i=0; i<segments; ++i)
{
     GLfloat tmp = 2 * pi * i / segments;
     glVertex2f(cos(tmp), sin(tmp));
}
glEnd();
```
这段代码将绘制一个圆环。如果我们在每次绘制图象时调用这段代码，则虽然可以达到绘制圆环的目的，但是**cos、sin等开销较大的函数被多次调用，浪费了CPU资源**。如果每一个顶点不是通过cos、sin等函数得到，而是使用更复杂的运算方式来得到，则浪费的现象就更加明显。

经过分析，我们可以发现上述两个问题的共同点：程序多次执行了重复的工作，导致CPU资源浪费和运行速度的下降。使用显示列表可以较好的解决上述两个问题。
在编写程序时，遇到重复的工作，我们往往是将重复的工作编写为函数，在需要的地方调用它。类似的，在编写OpenGL程序时，遇到重复的工作，可以创建一个显示列表，把重复的工作装入其中，并在需要的地方调用这个显示列表。
使用显示列表一般有四个步骤：**分配显示列表编号、创建显示列表、调用显示列表、销毁显示列表**。

#### 分配显示列表编号
OpenGL允许多个显示列表同时存在，就好象C语言允许程序中有多个函数同时存在。C语言中，不同的函数用不同的名字来区分，而在OpenGL中，不同的显示列表用不同的正整数来区分。
你可以自己指定一些各不相同的正整数来表示不同的显示列表。但是如果你不够小心，可能出现一个显示列表将另一个显示列表覆盖的情况。为了避免这一问题，使用 <font color="red">glGenLists函数</font> 来自动分配一个没有使用的显示列表编号。
glGenLists函数有一个参数i，表示要分配i个连续的未使用的显示列表编号。返回的是分配的若干连续编号中最小的一个。例如，glGenLists(3);如果返回20，则表示分配了20、21、22这三个连续的编号。**如果函数返回零，表示分配失败**。
可以使用 <font color="red">glIsList函数</font> 判断一个编号是否已经被用作显示列表。

#### 创建显示列表
创建显示列表实际上就是把各种OpenGL函数的调用装入到显示列表中。**使用glNewList开始装入，使用glEndList结束装入。** 
glNewList有两个参数，第一个参数是一个正整数表示装入到哪个显示列表。第二个参数有两种取值，如果为GL_COMPILE，则表示以下的内容只是装入到显示列表，但现在不执行它们；如果为GL_COMPILE_AND_EXECUTE，表示在装入的同时，把装入的内容执行一遍。
例如，需要把“设置颜色为红色，并且指定一个坐标为(0, 0)的顶点”这两条命令装入到编号为list的显示列表中，并且在装入的时候不执行，则可以用下面的代码：
```cpp
glNewList(list, GL_COMPILE);
glColor3f(1.0f, 0.0f, 0.0f);
glVertex2f(0.0f, 0.0f);
glEnd();
```
注意：显示列表**只能装入OpenGL函数**，而不能装入其它内容。例如：
```cpp
int i = 3;
glNewList(list, GL_COMPILE);
if( i > 20 )
     glColor3f(1.0f, 0.0f, 0.0f);
glVertex2f(0.0f, 0.0f);
glEnd();
```
其中if这个判断就没有被装入到显示列表。以后即使修改i的值，使i>20的条件成立，则glColor3f这个函数也不会被执行。因为它根本就不存在于显示列表中。

另外，**并非所有的OpenGL函数都可以装入到显示列表中**。例如，各种用于查询的函数，它们无法被装入到显示列表，因为它们都具有返回值，而glCallList和glCallLists函数都不知道如何处理这些返回值。在网络方式下，设置客户端状态的函数也无法被装入到显示列表，这是因为显示列表被保存到服务器端，各种设置客户端状态的函数在发送到服务器端以前就被执行了，而服务器端无法执行这些函数。分配、创建、删除显示列表的动作也无法被装入到另一个显示列表，但**调用显示列表的动作则可以被装入到另一个显示列表**。

#### 调用显示列表
使用 <font color="red">glCallList函数</font> 可以调用一个显示列表。该函数有一个参数，表示要调用的显示列表的编号。例如，要调用编号为10的显示列表，直接使用glCallList(10);就可以了。
使用glCallLists函数可以调用一系列的显示列表。该函数有三个参数，第一个参数表示了要调用多少个显示列表。第二个参数表示了这些显示列表的编号的储存格式，可以是GL_BYTE（每个编号用一个GLbyte表示），GL_UNSIGNED_BYTE（每个编号用一个GLubyte表示），GL_SHORT，GL_UNSIGNED_SHORT，GL_INT，GL_UNSIGNED_INT，GL_FLOAT。第三个参数表示了这些显示列表的编号所在的位置。在使用该函数前，需要用 <font color="red"> glListBase函数</font> 来设置一个偏移量。假设偏移量为k，且glCallLists中要求调用的显示列表编号依次为l1, l2, l3, ...，则实际调用的显示列表为l1+k, l2+k, l3+k, ...。
例如：
```cpp
GLuint lists[] = {1, 3, 4, 8};
glListBase(10);
glCallLists(4, GL_UNSIGNED_INT, lists);
```
则实际上调用的是编号为11, 13, 14, 18的四个显示列表。
注：“调用显示列表”这个动作本身也可以被装在另一个显示列表中。

#### 销毁显示列表
销毁显示列表可以回收资源。使用<font color="red">glDeleteLists</font> 来销毁一串编号连续的显示列表。
例如，使用glDeleteLists(20, 4);将销毁20，21，22，23这四个显示列表。

使用显示列表将会带来一些开销，例如，把各种动作保存到显示列表中会占用一定数量的内存资源。但如果使用得当，显示列表可以提升程序的性能。这主要表现在以下方面：
1、明显的减少OpenGL函数的调用次数。如果函数调用是通过网络进行的（Linux等操作系统支持这样的方式，即由应用程序在客户端发出OpenGL请求，由网络上的另一台服务器进行实际的绘图操作），将显示列表保存在服务器端，可以大大减少网络负担。
2、保存中间结果，避免一些不必要的计算。例如前面的样例程序中，cos、sin函数的计算结果被直接保存到显示列表中，以后使用时就不必重复计算。
3、便于优化。我们已经知道，使用glTranslate* 、glRotate* 、glScale* 等函数时，实际上是执行矩阵乘法操作，由于这些函数经常被组合在一起使用，通常会出现矩阵的连乘。这时，如果把这些操作保存到显示列表中，则一些复杂的OpenGL版本会尝试先计算出连乘的一部分结果，从而提高程序的运行速度。在其它方面也可能存在类似的例子。
同时，显示列表也为程序的设计带来方便。我们在设置一些属性时，经常把一些相关的函数放在一起调用，（比如，把设置光源的各种属性的函数放到一起）这时，如果把这些设置属性的操作装入到显示列表中，则可以实现属性的成组的切换。
当然了，即使使用显示列表在某些情况下可以提高性能，但这种提高很可能并不明显。毕竟，在硬件配置和大致的软件算法都不变的前提下，性能可提升的空间并不大。

显示列表的内容就是这么多了，下面我们看一个例子。
假设我们需要绘制一个旋转的彩色正四面体，则可以这样考虑：设置一个全局变量angle，然后让它的值不断的增加（到达360后又恢复为0，周而复始）。每次需要绘制图形时，根据angle的值进行旋转，然后绘制正四面体。这里正四面体采用显示列表来实现，即把绘制正四面体的若干OpenGL函数装到一个显示列表中，然后每次需要绘制时，调用这个显示列表即可。
将正四面体的四个顶点颜色分别设置为红、黄、绿、蓝，通过数学计算，将坐标设置为：
(-0.5, -5 * sqrt(5) / 48,   sqrt(3) / 6),
( 0.5, -5 * sqrt(5) / 48,   sqrt(3) / 6),
(   0, -5 * sqrt(5) / 48,  -sqrt(3) / 3),
(   0, 11 * sqrt(6) / 48,             0)
2007年4月24日修正：以上结果有误，通过计算AB, AC, AD, BC, BD, CD的长度，发现AD, BD, CD的长度与1.0有较大偏差。正确的坐标应该是：
* A点：(   0.5,    -sqrt(6)/12,  -sqrt(3)/6)
* B点：(  -0.5,    -sqrt(6)/12,  -sqrt(3)/6)
* C点：(     0,    -sqrt(6)/12,   sqrt(3)/3)
* D点：(     0,     sqrt(6)/4,            0)
程序代码中也做了相应的修改。

下面给出程序代码，大家可以从中体会一下显示列表的用法。
```cpp
#include <gl/glut.h>
#include <math.h>
// 定义初始窗口大小
#define WIDTH 400 
#define HEIGHT 400
#define ColoredVertex(c, v) do{ glColor3fv(c); glVertex3fv(v); }while(0)
GLfloat angle = 0.0f;
void myDisplay(void)
{
     static int list = 0;
     if( list == 0 )
     {
         // 如果显示列表不存在，则创建
        /* GLfloat
             PointA[] = {-0.5, -5*sqrt(5)/48,   sqrt(3)/6},
             PointB[] = { 0.5, -5*sqrt(5)/48,   sqrt(3)/6},
             PointC[] = {   0, -5*sqrt(5)/48,  -sqrt(3)/3},
             PointD[] = {   0, 11*sqrt(6)/48,           0}; */
         GLfloat
             PointA[] = { 0.5f, -sqrt(6.0f)/12, -sqrt(3.0f)/6},
             PointB[] = {-0.5f, -sqrt(6.0f)/12, -sqrt(3.0f)/6},
             PointC[] = { 0.0f, -sqrt(6.0f)/12,  sqrt(3.0f)/3},
             PointD[] = { 0.0f,  sqrt(6.0f)/ 4,             0};
         GLfloat
             ColorR[] = {1, 0, 0},
             ColorG[] = {0, 1, 0},
             ColorB[] = {0, 0, 1},
             ColorY[] = {1, 1, 0};
         list = glGenLists(1);
         glNewList(list, GL_COMPILE);
         glBegin(GL_TRIANGLES);
         // 平面ABC
         ColoredVertex(ColorR, PointA);
         ColoredVertex(ColorG, PointB);
         ColoredVertex(ColorB, PointC);
         // 平面ACD
         ColoredVertex(ColorR, PointA);
         ColoredVertex(ColorB, PointC);
         ColoredVertex(ColorY, PointD);
         // 平面CBD
         ColoredVertex(ColorB, PointC);
         ColoredVertex(ColorG, PointB);
         ColoredVertex(ColorY, PointD);
         // 平面BAD
         ColoredVertex(ColorG, PointB);
         ColoredVertex(ColorR, PointA);
         ColoredVertex(ColorY, PointD);
         glEnd();
         glEndList();
         glEnable(GL_DEPTH_TEST);
     }
     // 已经创建了显示列表，在每次绘制正四面体时将调用它
     glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
     glPushMatrix(); // 将当前矩阵保存入堆栈顶(保存当前矩阵)
     glRotatef(angle, 1, 0.5, 0);
     glCallList(list);
     glPopMatrix();
     glutSwapBuffers(); // 要在绘制完成时调用实现双缓冲
}
void myIdle(void)
{
     ++angle;
     if( angle >= 360.0f )
         angle = 0.0f;
     myDisplay();
}
int main(int argc, char* argv[])
{
     glutInit(&argc, argv);
     glutInitDisplayMode(GLUT_RGBA | GLUT_DOUBLE); // GLUT_DOUBLE: 双缓冲功能，做动画用
     glutInitWindowPosition(200, 200);
     glutInitWindowSize(WIDTH, HEIGHT);
     glutCreateWindow("OpenGL 窗口");
     glutDisplayFunc(&myDisplay);
     glutIdleFunc(&myIdle); // glutIdleFunc: 在CPU空闲的时间调用某一函数
     glutMainLoop();
     return 0;
}
```
在程序中，我们将绘制正四面体的OpenGL函数装到了一个显示列表中，但是，关于旋转的操作却在显示列表之外进行。这是因为如果把旋转的操作也装入到显示列表，则每次旋转的角度都是一样的，不会随着angle的值的变化而变化，于是就不能表现出动态的旋转效果了。
程序运行时，可能感觉到画面的立体感不足，这主要是因为没有使用光照的缘故。如果将glColor3fv函数去掉，改为设置各种材质，然后开启光照效果，则可以产生更好的立体感。大家可以自己试着使用光照效果，唯一需要注意的地方就是法线向量的计算。由于这里的正四面体四个顶点坐标选取得比较特殊，使得正四面体的中心坐标正好是(0, 0, 0)，因此，每三个顶点坐标的平均值正好就是这三个顶点所组成的平面的法线向量的值。
```cpp
void setNormal(GLfloat* Point1, GLfloat* Point2, GLfloat* Point3)
{
     GLfloat normal[3];
     int i;
     for(i=0; i<3; ++i)
         normal[i] = (Point1[i]+Point2[i]+Point3[i]) / 3;
     glNormal3fv(normal);
}
```
限于篇幅，这里就不给出完整的程序了。不过，大家可以自行尝试，看看使用光照后效果有何种改观。尤其是注意四面体各个表面交界的位置，在未使用光照前，几乎看不清轮廓，在使用光照后，可比较容易的区分各个平面，因此立体感得到加强。当然了，这样的效果还不够。如果在各表面的交界处设置很多细小的平面，进行平滑处理，则光照后的效果将更真实。但这已经远离本课的内容了。

### OpenGL入门学习[十五] 数组的介入

绘制一个立方体，简单呀，我们学了第一课第二课，早就会了。
先别着急，立方体是很简单，但是这里只是拿立方体做一个例子，来说明OpenGL在绘制方法上的改进。
从原始一点的办法开始:
一个立方体有六个面，每个面是一个正方形，好，绘制六个正方形就可以了。
```c
glBegin(GL_QUADS);
     glVertex3f(...);
     glVertex3f(...);
     glVertex3f(...);
     glVertex3f(...);
     // ...
glEnd();
```
为了绘制六个正方形，我们为每个正方形指定四个顶点，最终我们需要指定6*4=24个顶点。但是我们知道，一个立方体其实总共只有八个顶点，要指定24次，就意味着每个顶点其实**重复使用**了三次，这样可不是好的现象。最起码，像上面这样重复烦琐的代码，是很容易出错的。稍有不慎，即使相同的顶点也可能被指定成不同的顶点了。
如果我们定义一个数组，把八个顶点都放到数组里，然后每次指定顶点都使用指针，而不是使用直接的数据，这样就避免了在指定顶点时考虑大量的数据，于是减少了代码出错的可能性。
```c
// 将立方体的八个顶点保存到一个数组里面
static const GLfloat vertex_list[][3] = {
     -0.5f, -0.5f, -0.5f,
      0.5f, -0.5f, -0.5f,
     // ...
};
// 指定顶点时，用指针，而不用直接用具体的数据
glBegin(GL_QUADS);
     glVertex3fv(vertex_list[0]);
     glVertex3fv(vertex_list[2]);
     glVertex3fv(vertex_list[3]);
     glVertex3fv(vertex_list[1]);
     // ...
glEnd();
```
修改之后，虽然代码变长了，但是确实易读得多。很容易就看出第0, 2, 3, 1这四个顶点构成一个正方形。
稍稍观察就可以发现，我们使用了大量的glVertex3fv函数，其实每一句都只有其中的顶点序号不一样，因此我们可以再定义一个序号数组，把所有的序号也放进去。这样一来代码就更加简单了。
```
// 将立方体的八个顶点保存到一个数组里面
static const GLfloat vertex_list[][3] = {
     -0.5f, -0.5f, -0.5f,
      0.5f, -0.5f, -0.5f,
     -0.5f,  0.5f, -0.5f,
      0.5f,  0.5f, -0.5f,
     -0.5f, -0.5f,  0.5f,
      0.5f, -0.5f,  0.5f,
     -0.5f,  0.5f,  0.5f,
      0.5f,  0.5f,  0.5f,
};
// 将要使用的顶点的序号保存到一个数组里面
static const GLint index_list[][4] = {
     0, 2, 3, 1,
     0, 4, 6, 2,
     0, 1, 5, 4,
     4, 5, 7, 6,
     1, 3, 7, 5,
     2, 6, 7, 3,
};
int i, j;
// 绘制的时候代码很简单
glBegin(GL_QUADS);
for(i=0; i<6; ++i)          // 有六个面，循环六次
    for(j=0; j<4; ++j)      // 每个面有四个顶点，循环四次
         glVertex3fv(vertex_list[index_list[i][j]]);
glEnd();
```

这样，我们就得到一个比较成熟的绘制立方体的版本了。它的数据和程序代码基本上是分开的，**所有的顶点放到一个数组中，使用顶点的序号放到另一个数组中**，而利用这两个数组来绘制立方体的代码则很简单。

**正对我们的面，按逆时针顺序，背对我们的面，则按顺时针顺序**，这样就得到了上面那个index_list数组。
为什么要按照顺时针逆时针的规则呢？因为这样做可以保证无论从哪个角度观察，看到的都是“正面”，而不是背面。在计算光照时，正面和背面的处理可能是不同的，另外，**剔除背面只绘制正面，可以提高程序的运行效率。**（关于正面、背面，以及剔除，参见第三课，绘制几何图形的一些细节问题）
例如在绘制之前调用如下的代码：
```c
glFrontFace(GL_CCW);
glCullFace(GL_BACK);
glEnable(GL_CULL_FACE);
glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);
```
则绘制出来的图形就只有正面，并且只显示边线，不进行填充。

#### 顶点数组
（提示：顶点数组是OpenGL 1.1所提供的功能）
前面的方法中，我们将数据和代码分离开，看起来只要八个顶点就可以绘制一个立方体了。但是实际上，循环还是执行了6*4=24次，也就是说虽然代码的结构清晰了不少，但是程序运行的效率，还是和最原始的那个方法一样。
减少函数的调用次数，是提高运行效率的方法之一。于是我们想到了显示列表。把绘制立方体的代码装到一个显示列表中，以后只要调用这个显示列表即可。
这样看起来很不错，但是**显示列表有一个缺点，那就是一旦建立后不可再改**。如果我们要绘制的不是立方体，而是一个能够走动的人物，因为人物走动时，四肢的位置不断变化，几乎没有办法把所有的内容装到一个显示列表中。必须每种动作都使用单独的显示列表，这样会导致大量的显示列表管理困难。
顶点数组是解决这个问题的一个方法。使用顶点数组的时候，也是像前面的方法一样，用一个数组保存所有的顶点，用一个数组保存顶点的序号。但最后绘制的时候，不是编写循环语句逐个的指定顶点了，而是通知OpenGL，“保存顶点的数组”和“保存顶点序号的数组”所在的位置，由OpenGL自动的找到顶点，并进行绘制。
下面的代码说明了顶点数组是如何使用的：

```c
glEnableClientState(GL_VERTEX_ARRAY);
glVertexPointer(3, GL_FLOAT, 0, vertex_list);
glDrawElements(GL_QUADS, 24, GL_UNSIGNED_INT, index_list);
```
其中：
>glEnableClientState(GL_VERTEX_ARRAY); 表示启用顶点数组。
glVertexPointer(3, GL_FLOAT, 0, vertex_list); 指定顶点数组的位置，3表示每个顶点由三个量构成（x, y, z），GL_FLOAT表示每个量都是一个GLfloat类型的值。第三个参数0，参见后面介绍“stride参数”。最后的vertex_list指明了数组实际的位置。
glDrawElements(GL_QUADS, 24, GL_UNSIGNED_INT, index_list); 根据序号数组中的序号，查找到相应的顶点，并完成绘制。GL_QUADS表示绘制的是四边形，24表示总共有24个顶点，GL_UNSIGNED_INT表示序号数组内每个序号都是一个GLuint类型的值，index_list指明了序号数组实际的位置。

上面三行代码代替了原来的循环。可以看到，**原来的glBegin/glEnd不再需要了**，**也不需要调用glVertex* 系列函数来指定顶点**，因此可以明显的减少函数调用次数。另外，数组中的内容可以随时修改，比显示列表更加灵活。 

详细一点的说明。
**顶点数组实际上是多个数组，顶点坐标、纹理坐标、法线向量、顶点颜色等等，顶点的每一个属性都可以指定一个数组，然后用统一的序号来进行访问。**比如序号3，就表示取得颜色数组的第3个元素作为颜色、取得纹理坐标数组的第3个元素作为纹理坐标、取得法线向量数组的第3个元素作为法线向量、取得顶点坐标数组的第3个元素作为顶点坐标。把所有的数据综合起来，最终得到一个顶点。
可以用glEnableClientState/glDisableClientState单独的开启和关闭每一种数组。
```c
glEnableClientState(GL_VERTEX_ARRAY);
glEnableClientState(GL_COLOR_ARRAY);
glEnableClientState(GL_NORMAL_ARRAY);
glEnableClientState(GL_TEXTURE_COORD_ARRAY);
```
用以下的函数来指定数组的位置：
```c
glVertexPointer
glColorPointer
glNormalPointer
glTexCoordPointer 
```
>为什么不使用原来的glEnable/glDisable函数，而要专门的规定一个glEnableClientState/glDisableClientState函数呢？
这跟OpenGL的工作机制有关。OpenGL在设计时，认为可以将整个OpenGL系统分为两部分，一部分是客户端，它负责发送OpenGL命令。一部分是服务端，它负责接收OpenGL命令并执行相应的操作。对于个人计算机来说，可以将CPU、内存等硬件，以及用户编写的OpenGL程序看做客户端，而将OpenGL驱动程序、显示设备等看做服务端。
通常，所有的状态都是保存在服务端的，便于OpenGL使用。例如，是否启用了纹理，服务端在绘制时经常需要知道这个状态，而我们编写的客户端OpenGL程序只在很少的时候需要知道这个状态。所以将这个状态放在服务端是比较有利的。
但顶点数组的状态则不同。我们指定顶点，实际上就是把顶点数据从客户端发送到服务端。是否启用顶点数组，只是控制发送顶点数据的方式而已。服务端只管接收顶点数据，而不必管顶点数据到底是用哪种方式指定的（可以直接使用glBegin/glEnd/glVertex*，也可以使用顶点数组）。所以，服务端不需要知道顶点数组是否开启。因此，顶点数组的状态放在客户端是比较合理的。
为了表示服务端状态和客户端状态的区别，服务端的状态用glEnable/glDisable，客户端的状态则用glEnableClientState/glDisableClientState。

##### stride参数：
顶点数组并不要求所有的数据都连续存放。如果数据没有连续存放，则指定数据之间的间隔即可。
例如：我们使用一个struct来存放顶点中的数据。注意每个顶点除了坐标外，还有额外的数据（这里是一个int类型的值）。
```c
typedef struct __point__ {
     GLfloat position[3];
    int      id;
} Point;
Point vertex_list[] = {
     -0.5f, -0.5f, -0.5f, 1,
      0.5f, -0.5f, -0.5f, 2,
     -0.5f,  0.5f, -0.5f, 3,
      0.5f,  0.5f, -0.5f, 4,
     -0.5f, -0.5f,  0.5f, 5,
      0.5f, -0.5f,  0.5f, 6,
     -0.5f,  0.5f,  0.5f, 7,
      0.5f,  0.5f,  0.5f, 8,
};
static GLint index_list[][4] = {
     0, 2, 3, 1,
     0, 4, 6, 2,
     0, 1, 5, 4,
     4, 5, 7, 6,
     1, 3, 7, 5,
     2, 6, 7, 3,
};
glEnableClientState(GL_VERTEX_ARRAY);
glVertexPointer(3, GL_FLOAT, sizeof(Point), vertex_list);
glDrawElements(GL_QUADS, 24, GL_UNSIGNED_INT, index_list);
```

注意最后三行代码，可以看到，几乎所有的地方都和原来一样，只在glVertexPointer函数的第三个参数有所不同。这个参数就是stride，它表示**“从一个数据的开始到下一个数据的开始，所相隔的字节数”**。这里设置为sizeof(Point)就刚刚好。**如果设置为0，则表示数据是紧密排列的。**对于3个GLfloat的情况，数据紧密排列时stride实际上为3*4=12。
混合数组。如果需要同时使用颜色数组、顶点坐标数组、纹理坐标数组等等，有一种方式是把所有的数据都混合起来，指定到同一个数组中。这就是混合数组。
```c
GLfloat arr_c3f_v3f[] = {
     1, 0, 0, 0, 1, 0,
     0, 1, 0, 1, 0, 0,
     0, 0, 1, -1, 0, 0,
};
GLuint index_list[] = {0, 1, 2};
glInterleavedArrays(GL_C3F_V3F, 0, arr_c3f_v3f);
glDrawElements(GL_TRIANGLES, 3, GL_UNSIGNED_INT, index_list);
```
glInterleavedArrays，可以设置混合数组。这个函数会**自动调用glVertexPointer, glColorPointer等函数，并且自动的开启或禁用相关的数组**。
函数的第一个参数表示了混合数组的类型。例如GL_C3F_V3F表示：三个浮点数作为颜色、三个浮点数作为顶点坐标。也可以有其它的格式，比如GL_V2F, GL_V3F, GL_C4UB_V2F, GL_C4UB_V3F, GL_C3F_V3F, GL_N3F_V3F, GL_C4F_N3F_V3F, GL_T2F_V3F, GL_T4F_V4F, GL_T2F_C4UB_V3F, GL_T2F_C3F_V3F, GL_T2F_N3F_V3F, GL_T2F_C4F_N3F_V3F, GL_T4F_C4F_N3F_V4F等等。其中T表示纹理坐标，C表示颜色，N表示法线向量，V表示顶点坐标。
>顶点数组与显示列表的区别。两者都可以明显的减少函数的调用次数，但是还是各有优点的。
对于顶点数组，顶点数据是存放在内存中的，也就是存放在客户端。每次绘制的时候，需要把所有的顶点数据从客户端（内存）发送到服务端（显示设备），然后进行处理。对于显示列表，顶点数据是放在显示列表中的，显示列表本身又是存放在服务器端的，所以不会重复的发送数据。
对于顶点数组，因为顶点数据放在内存中，所以可以随时修改，每次绘制的时候都会把当前数组中的内容作为顶点数据发送并进行绘制。对于显示列表，数据已经存放到服务器段，并且无法取出，所以无法修改。
也就是说，显示列表可以避免数据的重复发送，效率会较高；顶点数组虽然会重复的发送数据，但由于数据可以随时修改，灵活性较好。

#### 顶点缓冲区对象
（提示：顶点缓冲区对象是OpenGL1.5所提供的功能，但它在成为标准前是一个ARB扩展，可以通过GL_ARB_vertex_buffer_object扩展来使用这项功能。前面已经讲过，ARB扩展的函数名称以字母ARB结尾，常量名称以字母_ARB结尾，而标准函数、常量则去掉了ARB字样。很多的OpenGL实现同时支持vertex buffer object的标准版本和ARB扩展版本。我们这里以ARB扩展来讲述，因为目前绝大多数个人计算机都支持ARB扩展版本，但少数显卡仅支持OpenGL1.4，无法使用标准版本。）
前面说到顶点数组和显示列表在绘制立方体时各有优劣，那么有没有办法将它们的优点集中到一起，并且尽可能的减少缺点呢？顶点缓冲区对象就是为了解决这个问题而诞生的。**它数据存放在服务端，同时也允许客户端灵活的修改，兼顾了运行效率和灵活性。**
顶点缓冲区对象跟纹理对象有很多相似之处。首先，分配一个缓冲区对象编号，然后，为对应编号的缓冲区对象指定数据，以后可以随时修改其中的数据。下面的表格可以帮助类比理解。

|                          |       纹理对象      | 顶点缓冲区对象       |
|:-----------------------  | :----------------  | :---------------- |
|分配编号                    |   glGenTextures   |  glGenBuffersARB   |
|绑定(指定为当前所使用的对象)   |   glBindTexture   |  glBindBufferARB   |
|指定数据                    |   glTexImage*     |  glBufferDataARB   |
|修改数据                    |   glTexSubImage*  |  glBufferSubDataARB|

顶点数据和序号各自使用不同的缓冲区。具体的说，就是**顶点数据放在GL_ARRAY_BUFFER_ARB类型的缓冲区中，序号数据放在GL_ELEMENT_ARRAY_BUFFER_ARB类型的缓冲区中**。
具体的情况可以用下面的代码来说明：
```c
static GLuint vertex_buffer;
static GLuint index_buffer;
// 分配一个缓冲区，并将顶点数据指定到其中
glGenBuffersARB(1, &vertex_buffer);
glBindBufferARB(GL_ARRAY_BUFFER_ARB, vertex_buffer);
glBufferDataARB(GL_ARRAY_BUFFER_ARB,
    sizeof(vertex_list), vertex_list, GL_STATIC_DRAW_ARB);
// 分配一个缓冲区，并将序号数据指定到其中
glGenBuffersARB(1, &index_buffer);
glBindBufferARB(GL_ELEMENT_ARRAY_BUFFER_ARB, index_buffer);
glBufferDataARB(GL_ELEMENT_ARRAY_BUFFER_ARB,
    sizeof(index_list), index_list, GL_STATIC_DRAW_ARB);
```

在指定缓冲区数据时，最后一个参数是关于性能的提示。一共有STREAM_DRAW, STREAM_READ, STREAM_COPY, STATIC_DRAW, STATIC_READ, STATIC_COPY, DYNAMIC_DRAW, DYNAMIC_READ, DYNAMIC_COPY这九种。每一种都表示了使用频率和用途，OpenGL会根据这些提示进行一定程度的性能优化。
（提示仅仅是提示，不是硬性规定。也就是说，即使使用了STREAM_DRAW，告诉OpenGL这段缓冲区数据一旦指定，以后不会修改，但实际上以后仍可修改，不过修改时可能有较大的性能代价） 

当使用glBindBufferARB后，**各种使用指针为参数的OpenGL函数，行为会发生变化。**
以glColor3fv为例，通常，这个函数接受一个指针作为参数，从指针所指的位置取出连续的三个浮点数，作为当前的颜色。但使用glBindBufferARB后，这个函数不再从指针所指的位置取数据。函数会先把指针转化为整数，假设转化后结果为k，则会从当前缓冲区的第k个字节开始取数据。特别一点，如果我们写glColor3fv(NULL);因为NULL转化为整数后通常是零，所以从缓冲区的第0个字节开始取数据，也就是从缓冲区最开始的位置取数据。
这样一来，原来写的
```c
glVertexPointer(3, GL_FLOAT, 0, vertex_list);
glDrawElements(GL_QUADS, 24, GL_UNSIGNED_INT, index_list);
```
在使用缓冲区对象后，就变成了
```c
glVertexPointer(3, GL_FLOAT, 0, NULL);
glDrawElements(GL_QUADS, 24, GL_UNSIGNED_INT, NULL);
```
以下是完整的使用了顶点缓冲区对象的代码：
```c
static GLfloat vertex_list[][3] = {
     -0.5f, -0.5f, -0.5f,
      0.5f, -0.5f, -0.5f,
     -0.5f,  0.5f, -0.5f,
      0.5f,  0.5f, -0.5f,
     -0.5f, -0.5f,  0.5f,
      0.5f, -0.5f,  0.5f,
     -0.5f,  0.5f,  0.5f,
      0.5f,  0.5f,  0.5f,
};
static GLint index_list[][4] = {
     0, 2, 3, 1,
     0, 4, 6, 2,
     0, 1, 5, 4,
     4, 5, 7, 6,
     1, 3, 7, 5,
     2, 6, 7, 3,
};
if( GLEE_ARB_vertex_buffer_object ) {
     // 如果支持顶点缓冲区对象
    static int isFirstCall = 1;
    static GLuint vertex_buffer;
    static GLuint index_buffer;
    if( isFirstCall ) {
         // 第一次调用时，初始化缓冲区
         isFirstCall = 0;
         // 分配一个缓冲区，并将顶点数据指定到其中
         glGenBuffersARB(1, &vertex_buffer);
         glBindBufferARB(GL_ARRAY_BUFFER_ARB, vertex_buffer);
         glBufferDataARB(GL_ARRAY_BUFFER_ARB,
            sizeof(vertex_list), vertex_list, GL_STATIC_DRAW_ARB);
         // 分配一个缓冲区，并将序号数据指定到其中
         glGenBuffersARB(1, &index_buffer);
         glBindBufferARB(GL_ELEMENT_ARRAY_BUFFER_ARB, index_buffer);
         glBufferDataARB(GL_ELEMENT_ARRAY_BUFFER_ARB,
            sizeof(index_list), index_list, GL_STATIC_DRAW_ARB);
     }
     glBindBufferARB(GL_ARRAY_BUFFER_ARB, vertex_buffer);
     glBindBufferARB(GL_ELEMENT_ARRAY_BUFFER_ARB, index_buffer);
     // 实际使用时与顶点数组非常相似，只是在指定数组时不再指定实际的数组，改为指定NULL即可
     glEnableClientState(GL_VERTEX_ARRAY);
     glVertexPointer(3, GL_FLOAT, 0, NULL);
     glDrawElements(GL_QUADS, 24, GL_UNSIGNED_INT, NULL);
} else {
     // 不支持顶点缓冲区对象
     // 使用顶点数组
     glEnableClientState(GL_VERTEX_ARRAY);
     glVertexPointer(3, GL_FLOAT, 0, vertex_list);
     glDrawElements(GL_QUADS, 24, GL_UNSIGNED_INT, index_list);
}
```
可以分配多个缓冲区对象，顶点坐标、颜色、纹理坐标等数据，可以各自单独使用一个缓冲区。
每个缓冲区可以有不同的性能提示，比如在绘制一个运动的人物时，顶点坐标数据经常变化，但法线向量、纹理坐标等则不会变化，可以给予不同的性能提示，以提高性能。
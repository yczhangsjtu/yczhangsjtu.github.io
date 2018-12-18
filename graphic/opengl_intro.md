# OpenGL绘图和渲染

OpenGL是一个3D引擎。它按照一定的流程，将程序员定义好的场景绘制到屏幕上。

当OpenGL开始绘图的时候，它看到的有以下这么些信息：

1. 模型信息：有哪些物体。这些物体用顶点的三维或四维坐标表示，每三个顶点为一组，表示一个三角形。此外还有每个顶点处的法向量，以及每个顶点的颜色等信息，都是用浮点数组表示的。
2. 摄像头信息：摄像头在哪儿，从哪个角度拍摄，视野有多大。这一切信息由两个变换矩阵概括了：`modelviewMatrix`和`projectionMatrix`。
3. 光照信息

OpenGL根据这些信息，一步步计算，最终得到屏幕上哪个点应该是什么颜色。这个过程被称为**OpenGL流水线**。

## OpenGL流水线

OpenGL流水线是指3D对象从内存里构建好之后，到绘制在屏幕上的全过程。

![OpenGL Pipeline](/assets/images/opengl-pipeline.png)

流水线大概包括以下几个步骤：

1. 顶点渲染：对于每个顶点，输入顶点在世界坐标系中的位置，输出顶点在摄像头坐标系中的位置
2. 栅格化：对于每个三角形，输入三个顶点信息，判断这个三角形覆盖了屏幕上的哪些像素
3. 像素渲染：对于每个像素，输入这个像素在屏幕上的位置，以及模型的其他信息和光照信息等，计算这个像素是什么颜色

在启用流水线进行计算之前，还需要做些准备工作。因为OpenGL是要用GPU进行计算的，而GPU只能访问显存。所以，需要把内存中的数据拷贝到显存里。为了做这件事，OpenGL定义了一个叫**缓存（Buffer）**的概念，并提供了一些操作缓存的接口。程序员不需要了解操作显存的具体细节，对缓存操作就可以管理显存，并告诉OpenGL怎样使用显存里的数据。

**顶点渲染**和**像素渲染**这两步都是可编程的。程序员通过写一种叫做GLSL（OpenGL Shader Language）的语言的程序，来实现这两个渲染器。

对于顶点渲染来说，一般只做一件事，就是将摄像头的变换矩阵`modelviewMatrix`和`projectionMatrix`乘在顶点坐标上，将结果输出。如果输入是同态向量，还要做一次去同态化，得到三维坐标。OpenGL会并行调用顶点渲染器，每个顶点调用一个。

对于像素渲染来说，做的事情就有点复杂了。简单的像素渲染器可以不管输入，只输出一个颜色，这样就会在屏幕上画出一片颜色均匀的轮廓。也可以根据输入的屏幕坐标来指定不同的颜色。一般来说，如果要画出3D效果的话，需要把顶点颜色、**光照**、顶点的法向量同时考虑进来。和顶点渲染器一样，OpenGL会并行调用像素渲染器，每个像素调用一个。

像素渲染器输出的结果，会写到**帧缓存（Frame Buffer）**中。虽然都叫缓存，但这和用作GPU输入的缓存完全不是一个概念。帧缓存也在GPU中，它直接决定了屏幕上显示什么。

## 帧缓存（FrameBuffer）

**帧缓存**就是放最终画出来的2D图象的地方。这其实是流水线的最后一步，所以这里只简单提及一下。这样的缓存在OpenGL中总共有五个：

- **前端缓存（Front buffer）**: 这里存放的图象会直接画到显示器上
- **后端缓存（Back buffer）**: 一般用来在这里画图，画好后和Front buffer交换，这样在动画过程中不会有闪动
- **左缓存（Left buffer）和右缓存（right buffer）**: 和VR相关，基本用不到
- **深度缓存（Depth buffer）**: 用来处理对象的前后关系

最简单的模式是直接画在前端缓存和深度缓存上。不过，因为绘制的第一步几乎必然是清空画布，所以每次更新图象时，有可能看到闪动。

比较常用的模式是使用后端缓存来绘制，完成后交换前端缓存和后端缓存。

## 缓存（Buffer）

缓存是OpenGL暴露给程序员的用来操作显存的接口。为了高效绘制3D场景，OpenGL需要用到GPU，而程序员建立的3D模型，其数据一开始放在内存里。

假设我们有一个三角形，用三个顶点的同态坐标表示。

```c++
const float vertexPositions[] = {
    0.75f, 0.75f, 0.0f, 1.0f,
    0.75f, -0.75f, 0.0f, 1.0f,
    -0.75f, -0.75f, 0.0f, 1.0f,
};
```

我们想把这个三角形的顶点坐标数据放到显存里。

首先，创造一个缓存。创造缓存的同时，OpenGL在显存里分配一块内存出来。OpenGL用整数来作为缓存的描述符。`glGenBuffers`可以用来构造`n`个缓存，然后把这`n`个缓存的描述符存在数组里。这个例子中，只需要一个缓存，所以直接取要存这个描述符的整数地址。

```c++
GLuint positionBufferObject;
glGenBuffers(1, &positionBufferObject);
```

OpenGL采用的是状态机模型。它要操作一个OpenGL内置数据结构的时候，不是每次都把这个数据结构的描述符传给对应的函数，而是首先指定一个描述符，声明以下所有的操作都是针对这个数据结构做的。这相当于用全局变量为函数省掉参数。这个过程称为绑定（Bind）。为了保证代码的独立性，一般操作结束之后就立刻解绑（指定一个空描述符）。

`glBindBuffer`函数用来绑定缓存。`GL_ARRAY_BUFFER`表示这个缓存的类型就是普通的数组。每个类型可以绑定一个缓存。可以想象为每个类型有一个全局变量，存着这个类型目前绑定着哪个缓存，而`glBindBuffer`的第一个参数则告诉OpenGL这次是要修改哪个全局变量。

```c++
glBindBuffer(GL_ARRAY_BUFFER, positionBufferObject);
```

绑定好缓存后，接下来就是拷贝数据了。下面这个函数，把内存中的数据拷贝到当前指定的缓存里。这个函数的实际效果和`memset`类似，只不过我们不知道目标的内存地址（只有OpenGL自己知道），所以不能直接调用`memset`来做这件事。

```c++
glBufferData(GL_ARRAY_BUFFER, sizeof(vertexPositions), vertexPositions, GL_STATIC_DRAW);
```

数据拷贝到缓存之后，OpenGL的流水线就可以处理这些数据了。流水线中第一个步骤就是顶点渲染。顶点渲染器会从缓存对象中读取数据。首先，它需要知道这一坨数据到底是什么。这个例子中的`glVertexAttribPointer`函数告诉它，这些数据是`GL_FLOAT`格式的，每四个代表一个点，且两个点的数据之间间隔为0，数据的起始位置就是Buffer的起始位置（偏移为0）。

绘制时，OpenGL程序会根据这些信息，将缓存中的数据拆开成点，对每个点都执行一个**顶点渲染器（Vertex Shader）**。这个过程是对每个点并行执行的，因而速度非常快。

`glEnableVertexAttribArray`函数的作用是指定这个缓存的数据作为渲染器的第几个输入。

```c++
glEnableVertexAttribArray(0);
glVertexAttribPointer(0, 4, GL_FLOAT, GL_FALSE, 0, 0);
```

最后，解绑当前缓存对象。

```c++
glBindBuffer(GL_ARRAY_BUFFER, 0);
```

对另外一部分数据（颜色）的处理大体相同，只不过`glEnableVertexAttribArray`改为指定其为渲染器的第1个输入。索引则不经过顶点渲染器处理（顶点渲染器是处理单个顶点的，索引在这里没什么用）。

## 顶点渲染（Vertex Shader）

现在，数据已经被存入了缓存，并传递给了顶点渲染器。实际上，顶点渲染器拿到了两个输入，分别是两个`vec4`类型的变量。

一个渲染器，说白了就是一个程序，只不过在OpenGL中，它并不是用C语言写的，而是用一个领域特定语言，即GLSL (OpenGL Shading Language)。一个Shader程序需要在程序执行时动态编译，而不是和OpenGL程序一起编译好，原因是不同的机器的GPU指令集不尽相同，这样做可以尽量确保程序的可移植性。

OpenGL的流水线中，只有顶点渲染器和**段渲染器（Fragment Shader）**这两个部分是可编程的，也就是由用户指定的。

渲染器作为一个程序，很像是一个无副作用的函数。它接受输入，产生输出，期间不能和其他渲染器有任何数据交互。下面是一个简单的顶点渲染器。

```c++
#version 330

layout(location = 0) in vec4 position;
void main()
{
    gl_Position = position;
}
```

第一行，每个渲染器都必须指定版本。

`layout(location = 0) in vec4 position`行，指定第0个输入是一个`vec4`类型的变量，名字为`position`。

和C语言类似，GLSL用`main`函数指定所有需要执行的程序。这个顶点渲染器很简单，就是把输入的点的坐标原样输出。这里`gl_Position`是顶点渲染器特有的内置变量，类型是`out vec4`。每个顶点渲染器在`main`函数中必须给它赋一个值，不然程序就会出错，类似于写了一个指定了返回值类型，却没有真正返回值的函数。

除了`gl_Position`外，顶点渲染器还可以在`main`函数外自己指定输出数据，比如`out vec4 Color`。自己指定的变量名不能以`gl_`开头。此外，和`gl_Position`不同，自己的输出数据对OpenGL来说没有什么意义。它唯一的作用就是传给下一个渲染器（也就是段渲染器），由后者来使用。但`gl_Position`不会传给下一个渲染器，它由OpenGL直接使用，确切地说，是在栅格化（Rasterization）过程中就被用掉了。

相比之下，自己定义的输出变量，必须在下一个渲染器中指定为输入，名字必须相同，修饰符也要相同，换句话说，除了把`out`换成`in`之外，没有任何改变。OpenGL并不理解这个变量是做什么的，只有程序员自己明白。

通常，需要由顶点渲染器传给段渲染器的变量，有Color和Normal，也就是图形在这一点的颜色和法向量。

但是，这里存在一个数量不匹配的问题。顶点渲染器只对每一个点运行一次。在我们这里的例子中，就只运行三次。如果有额外的输出变量如Color，那么也只有三个。但段渲染器的目的是确定每个像素的颜色（一个段看做一个像素），是对每个像素运行一次。所以顶点渲染器会运行成千上万次。如何从顶点渲染器的三个输出里，推导出段渲染器需要的成千上万个输入呢？

这个推导过程是OpenGL来自动完成的，叫做**段插值（Fragment Interpolation）**。默认的插值模式为`smooth`模式，你也可以自己指定`smooth out vec4 Color`。除此之外，还有两个模式为`noperspective`和`flat`。其中，`flat`模式简单地把第一个顶点渲染器的输出作为所有的段渲染器的输入。

## 栅格化

栅格化的过程，就是确定屏幕上哪个像素属于哪个图形，从而知道在计算这个像素颜色时，该调用哪个段渲染器。栅格化过程是不可编程的。

## Fragment Shader

段渲染器由OpenGL对每个像素进行调用，用来确定每个像素的颜色。它的输出结果作为最终结果，直接就写到图像上去了。

如下是一个简单的段渲染器。它不做任何特殊处理（像光照啊，纹理啊），直接把传递给它的颜色输出。

和顶点渲染器不同，段渲染器没有内置的输出变量。另外，它只需要一个`vec4`类型的输出变量，别的输出变量也没什么用，因为OpenGL只需要一个输出，那就是颜色。这个输出变量的名字，用户可以自己指定。

```c++
#version 330

smooth in vec4 theColor;

out vec4 outputColor;

void main()
{
    outputColor = theColor;
}
```

除了从顶点渲染器接受到的输入外，段渲染器还有一些内置输入，这些输入是栅格化模块传给它的。其中一个就是该像素点在屏幕上的位置`vec3 gl_FragCoord`。其`x`和`y`属性表示在屏幕上的位置，`z`表示深度。

除了颜色外，每个点还可以有一个法向量。这个法向量和颜色一样，由顶点渲染器输出，经过插值后传给段渲染器。法向量的作用是辅助段渲染器计算光照效果。不过，如果要计算光照的话，要传的数据就更多了：光源的数据（点光源的位置、强度，平行光源的方向、强度等），以及材料（Material）的属性。材料可以看做是一种高级的颜色，和不同的光照信息结合可以输出不同的颜色。建3D模型的时候给模型上色，涂的不是颜色，是材料。

## Uniform变量

目前为止，我们画的这个三角形没有经过任何变换。它的`x`和`y`坐标本来就是在`[-1,1]x[-1,1]`这个方形之内的。我们看到的是默认的摄像头（中心在原点，头顶`y`方向，往负`z`方向俯视）看到的场景。如果我们想要给摄像头换个角度，我们需要两个矩阵：`modelview`矩阵和投影`projection`矩阵。前者由`glm::lookAt()`计算出来，后者可以用`glm::perspective()`或者`glm::ortho()`来计算。

然后，将这两个矩阵依次乘在每个点上，就得到了从指定的摄像头看到的场景。问题是，这个计算该在哪里进行？如果在OpenGL程序里进行，显然要用CPU对每个点做循环。当点数非常大的时候，这会成为性能瓶颈。如果要利用GPU并行计算，就要把这个乘法放到顶点渲染器里。

和点的坐标不同，当OpenGL并行地对每个点执行一个顶点渲染器时，这两个矩阵的值是不变的。因此，这两个矩阵不能和点数据一样，走缓存路线传入渲染器中。实际上，渲染器的输入中有一类uniform变量，用来接收从OpenGL输入的变量，这些变量对每个点都是相同的。

在Shader中定义这两个矩阵的方式如下：

```c++
uniform in mat4 modelview;
uniform in mat4 projection;
```

随后就可以直接使用了

```c++
int main() {
    gl_Position = projection * modelview * position;
}
```

从OpenGL程序传uniform变量给渲染器的方式，是使用`glUniform*`系列函数。和其他`gl`函数类似，后面的`*`部分由“数字+类型”组成，如`glUniform3fv`传入的是`GLfloat`类型的3维数组，`glUniform1i`传入的是`GLuint`类型的单个变量的值。

传入四阶矩阵则使用`glUniformMatrix4fv`函数。

`glUniform*`系列函数的参数为`(GLint location, GLsizei count, const GLtype *value)`（以`v`结尾）或`(GLint location, GLtype value0, GLtype value1 ...)`（不以`v`结尾）。其中，`location`和通过缓存传入的输入参数类似，表示这个参数在渲染器的输入参数中的位置。和通过Buffer传入的输入参数不同，这个位置不能自己指定，需要在OpenGL程序初始化时，通过`glGetUniformLocation`函数获取。这个函数是在渲染器编译链接好之后执行的。

`glUniformMatrix*`系列函数没有不带`v`的版本，参数为`(GLint location, GLsizei count, GLboolean transpose, const GLtype *value)`。`transpose`表示是否对矩阵进行转置。`value`参数通常通过取矩阵的第0行第0列的元素的地址获取，即`&m[0][0]`。

注意，上述这些函数没有指定到底是传给顶点渲染器还是段渲染器，因为这两个渲染器是共享所有的uniform变量的。所以，这两个渲染器中不能有名字相同但类型不同的uniform变量，不然链接时会报错。

并不是声明了uniform变量，就一定会用到。只有真正在`main`函数中用到的变量，才能让`glGetUniformLocation`函数获取到值，否则这个函数会返回-1。此外，一个渲染器要用的uniform变量，另一个渲染器不一定会用到，那么另一个渲染器可以不声明。但是一定要注意，两个渲染器各自要用到的不同的uniform变量不能取相同的名字，不然它们会被当做同一个变量，它们的值会互相覆盖！不注意这一点，如果幸运的话，两个变量的类型不相同，链接时就会报错；不然，如果两个变量的类型还相同，程序不会报错，但运行结果和预期不同，就成了一个让人半天也调试不出来的BUG。

## 光照

光照的效果在段渲染器中实现。不过，光照的计算需要众多的参数。一个参数是当前点的法向量，这个值由顶点渲染器传过来。

## 渲染器的编译和链接

渲染器是运行在GPU中的程序。因为不同的电脑GPU指令可能不同，为了保证可移植性，渲染器并不是和OpenGL程序一起编译的。实际上，渲染器由OpenGL提供的函数，在OpenGL运行时编译。

以下程序编译并链接顶点渲染器和段渲染器，得到一个**程序（program）**。

```c++
char *vertexShaderSource = "...";
char *fragmentShaderSource = "...";
GLuint vertexShader, fragmentShader, program;
GLint compiled, linked;

svertexShader = glCreateShader(GL_VERTEX_SHADER);
glShaderSource(vertexShader, 1, vertexShaderSource, NULL);
glCompileShader(vertexShader);
glGetShaderiv(GL_COMPILE_STATUS, &compiled)
if(!compiled) {
    cerr << "Failed to compile vertex shader!";
    exit(-1);
}

fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
glShaderSource(fragmentShader, 1, fragmentShaderSource, NULL);
glCompileShader(fragmentShader);
glGetShaderiv(GL_COMPILE_STATUS, &compiled)
if(!compiled) {
    cerr << "Failed to compile fragment shader!" << endl;
    exit(-1);
}

program = glCreateProgram();
glAttachShader(program, vertexShader);
glAttachShader(program, fragmentShader);
glLinkProgram(program);
glGetProgramiv(program, GL_LINK_STATUS, &linked);
if(linked) {
    glUseProgram(program);
} else {
    cerr << "Failed to link shaders!" << endl;
    exit(-1);
}

```

这段程序的最后，如果链接成功的话，调用了`glUseProgram`函数。这个函数调用之后，就可以用`glGetUniformLocation(program, "variableName")`函数获取渲染器中的uniform变量了。

## 开始写OpenGL程序

注意，OpenGL本身是没有和窗口、鼠标、键盘等用户界面交互的接口的。这是为了保证OpenGL的可移植性。提供这些接口的是GLUT。

下面是一个典型的使用了glut的OpenGL程序的`main`函数。这个`main`函数中只调用了`glut`开头的函数，`gl`开头的函数都包装在了`init()`里。

```c++
int main(int argc, char *argv[]) {
    glutInit(&argc, argv); // 永远是第一个调用的OpenGL函数，未初始化的情况下，调用任何其他OpenGL函数的后果都是不可预料的
    glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB); // 绘制模式：用什么FrameBuffer，用什么颜色空间。这里选了最简单的：直接画在Front Buffer上，用RGB三色模式
    glutInitWindowSize(500, 500);
    glutInitWindowPosition(100, 100);
    glutCreateWindow("Demo");
    
    // 初始化函数，这个函数是自己定义的，但做的事情大致是相同的，具体内容下文介绍
    init();
    
    // 指定回调函数，这些回调函数是用来和用户交互的
    glutDisplayFunc(display);
    glutReshapeFunc(reshape);
    glutKeyboardFunc(keyboard);
    glutMouseFunc(mouse);
    glutMotionFunc(mousedrag);
    
    // 开始主循环
    glutMainLoop();
    
    // 程序退出，清理资源
    deleteBuffers(); // 清理init()函数中分配的Buffer，具体内容下文介绍
    
    return 0;
}
```

注意：对MacOS来说: `glutInitDisplayMode`函数需要额外OR一个`GLUT_3_2_CORE_PROFILE`标志位。

### 全局变量

在这个简单的例子中，不考虑对模型进行变换，直接将准备好的数据写到全局变量里。

```c++
const float vertexPositions[] = {
    0.75f, 0.75f, 0.0f, 1.0f,
    0.75f, -0.75f, 0.0f, 1.0f,
    -0.75f, -0.75f, 0.0f, 1.0f,
};

const float vertexColors[] = {
    1.0f, 0.0f, 0.0f, 1.0f,
    0.0f, 1.0f, 0.0f, 1.0f,
    0.0f, 0.0f, 1.0f, 1.0f
};

vec3 eye, center, up;

mat4 modelview, projection;

GLuint positionBufferObject， colorBufferObject, vao;
```

### 初始化函数init()

一个3D对象是由点列组成的对象，在OpenGL中被称为`VertexArrayObject`，简写为VAO。之前介绍的`VertexAttribute`相关的操作，都是在指定了一个VAO的前提下完成的。这些操作会修改一个VAO的内部状态。注意，绑定缓存、往缓存里写数据这些操作和VAO无关，只有试图把当前缓存和渲染器相关联的函数会，也就是`glEnableVertexAttribArray`，`glDisableVertexAttribArray`和`glVertexAttribPointer`。这三个函数在没有指定VAO时执行会报错。

所有这些对VAO的操作在初始化中完成。在绘制时，只需要Bind一下想要画的VAO，就可以用一个函数来完成绘制。

这个例子中只用到了一个VAO。

```c++
void init() {
    // 编译和链接Shader，见上文，这里略
    program = ...;
    
    // 准备modelview和projection矩阵
    modelviewLocation = glGetUniformLocation(program, "modelview");
    projectionLocation = glGetUniformLocation(program, "projection");
    
    eye = vec3(0.0f, 0.0f, 3.0f);
    center = vec3(0.0f, 0.0f, 0.0f);
    up = vec3(0.0f, 1.0f, 0.0f);
    modelview = lookAt(eye, center, up);
    projection = perspective(radians(45.0f), 1.33f, 0.1f, 10.0f);
    
    glUniformMatrix4fv(modelviewLocation, 1, GL_FALSE, &modelview[0][0]);
    glUniformMatrix4fv(projectionLocation, 1, GL_FALSE, &projection[0][0]);
    
    // 准备Buffer和VAO
    glGenVertexArrays(1, &vao);
    glBindVertexArray(vao);
    
	glGenBuffers(1, &positionBufferObject);
    glBindBuffer(GL_ARRAY_BUFFER, positionBufferObject);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertexPositions), vertexPositions, GL_STATIC_DRAW);
    glEnableVertexAttribArray(0);
	glVertexAttribPointer(0, 4, GL_FLOAT, GL_FALSE, 0, 0);
    glBindBuffer(GL_ARRAY_BUFFER, 0);
    
	glGenBuffers(1, &colorBufferObject);
    glBindBuffer(GL_ARRAY_BUFFER, colorBufferObject);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertexColors), vertexColors, GL_STATIC_DRAW);
    glEnableVertexAttribArray(1);
	glVertexAttribPointer(1, 4, GL_FLOAT, GL_FALSE, 0, 0);
    glBindBuffer(GL_ARRAY_BUFFER, 0);
    
    glBindVertexArray(0);
}
```

### 绘制

绘制是在回调函数`display`中完成的。这个函数在每次需要重绘时由系统自动调用。

每次需要绘制一个对象时，需要指定对应的VAO，然后调用`glDrawArrays`函数。

绘制开始时，要记得先清空画布。

```c++
void display() {
    glClearColor(0.0f, 0.0f, 0.0f, 1.0f); // 清空为黑色
    glClear(GL_COLOR_BUFFER_BIT);
    
    glBindVertexArray(vao);
    glDrawArrays(GL_TRIANGLES, 0, 3);
    glBindVertexArray(0);
}
```

`GL_TRIANGLES`模式下，每三个点为一组，画一个三角形。两个三角形之间不共用点。这个例子中只有三个点，所以用什么模式都无所谓。

不过，如果点比较多的话，不共用点就会造成存储空间和性能的浪费。比如，如果要画一个正方体，需要画12个三角形，不共用点的话，需要指定36个点的坐标，虽然实际上只有八个点。这时，索引数组就可以发挥作用了。画正方体时，可以只指定8个点的坐标，而在索引数组中指定36个点各是这8个点中的哪个。

索引数组需要传递给另外一类缓存，叫做`GL_ELEMENT_ARRAY_BUFFER`。为了演示索引数组的用法，我们在全局变量中增加一个数组

```c++
const GLuint vertexIndices = {
    0, 1, 2
};

GLuint indexBufferObject;
```

在`init()`函数中，增加一段代码来把这个数组拷贝到缓存中。这段代码需要在`glBindVertexArray(0)`之前执行。

```c++
glGenBuffers(1, &indexBufferObject);
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, indexBufferObject);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(vertexIndices), vertexIndices, GL_STATIC_DRAW);
```

注意到，这里最后没有调用`glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0)`来解绑。因为`GL_ELEMENT_ARRAY_BUFFER`的绑定与解绑也是VAO的状态之一（相比之下，`GL_ARRAY_BUFFER`则不是）。

最后，在`display`函数中，用`glDrawElements`代替`glDrawArrays`。

```c++
glDrawElements(GL_TRIANGLES, 3, GL_UNSINGED_INT, 0);
```

### 清理

程序运行完后，需要把缓存和VAO对象清理掉。

```c++
void deleteBuffers() {
    glDeleteBufferArrays(1, &vao);
    glDeleteBuffers(1, &positionBufferObject);
    glDeleteBuffers(1, &colorBufferObject);
}
```

### 和用户交互

如果需要和用户进行交互，可以在以下函数中进行实现。很多情况下，用户可能会希望改变摄像头的位置，或者改变窗口大小，这时候可能需要更新**模型（modelview）**和**投影（projection）**矩阵，此时需要重新调用`glUniformMatrix4fv`函数更新渲染器中的这两个矩阵。其实，`reshape`函数是程序开始时必然会调用一次的，因此投影矩阵的更新可以只在这里做，而不在`init`函数中做。

用户做了一些操作后，可能会改变世界状态，使当前的画面过时，这时要调用`glutPostRedisplay`函数来重新绘制。

```c++
void keyboard(unsigned char key, int x, int y) {
    // Do something, then redraw the scene
    // Might update the modelview matrix
    glUniformMatrix4fv(modelviewLocation, 1, GL_FALSE, &modelview[0][0]);
    glutPostRedisplay();
}

void mouse(int button, int state, int x, int y) {
    // Do something, then redraw the scene
    // Might update the modelview matrix
    glUniformMatrix4fv(modelviewLocation, 1, GL_FALSE, &modelview[0][0]);
    glutPostRedisplay();
}

void mousedrag(int x, int y) {
    // Do something, then redraw the scene
    // Might update the modelview matrix
    glUniformMatrix4fv(modelviewLocation, 1, GL_FALSE, &modelview[0][0]);
    glutPostRedisplay();
}

void reshape(int w, int h) {
    glViewport(0, 0, (GLsizei) w, (GLsizei) h);
    // Perspective Projection (45 degree in radian)
    projection = glm::perspective(radian(45.0f), (GLfloat) w / (GLfloat) h, 0.1f, 10.0f);
    // Send the projection matrix to the shader
    glUniformMatrix4fv(projectionLocation, 1, GL_FALSE, &projection[0][0]);
}
```
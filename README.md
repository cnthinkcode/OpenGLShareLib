# OpenGLShareLib
MacOS OpenGL环境搭建

需要使用的三方库
- GLEW
- CLTools
- libGLTools.a

这些库已经打包放在github，可以到[OpenGLShare.zip](https://github.com/cnthinkcode/OpenGLShareLib/blob/main/OpenGLShare.zip)下载

1、打开Xcode，新建masOS App项目。

![新建项目](https://www.thinkcode.cn/usr/uploads/2023/02/2402135366.png)

2、下面随便写个名字就可以。

![填个名字](https://www.thinkcode.cn/usr/uploads/2023/02/2917077851.png)


3、删除下面Xcode的生成的文件：
- AppDelegate.h
- AppDelegate.m
- ViewController.h
- ViewController.m
- Main.storyboard
- main.m

到项目配置的General->Main Interface里面删除”Main“

![去掉Main.storyboard入口](https://www.thinkcode.cn/usr/uploads/2023/02/3702120478.png)


4、新建一个main.cpp，勾选不创建main.hpp即可。

![C++ File](https://www.thinkcode.cn/usr/uploads/2023/02/709548923.png)

![不创建头文件](https://www.thinkcode.cn/usr/uploads/2023/02/2720262424.png)


5、将解压后的OpenGLShare拖入到项目中；在Build Setting->Header Search Paths中拖入OpenGLShare/include/和OpenGLShare/include/GLEW文件夹

![Header Search Paths](https://www.thinkcode.cn/usr/uploads/2023/02/2052908385.png)


6、在Build Phases里面添加系统自带openGL库
- GLUT.framework
- OpenGL.framework
 
![openGL库](https://www.thinkcode.cn/usr/uploads/2023/02/1579518342.png)


7、将下面的代码，贴到main.cpp里面：

```cpp
#include <stdio.h>
#include "GLTools.h"
#include <glut/glut.h>

#pragma clang diagnostic push
#pragma clang diagnostic ignored"-Wdeprecated-declarations"

GLBatch triangleBatch;
GLShaderManager shaderManager;

void onChangeSize(int w,int h){
    glViewport(0,0, w, h);
}

void setupRC(){
    glClearColor(1.0f, 1.0f, 1.0f, 1.0f);
    shaderManager.InitializeStockShaders();
    
    GLfloat vVerts[] = {
        -0.5f, 0.5f, 0.0f,
        -0.5f, -0.5f, 0.0f,
        0.5f, -0.5f, 0.0f,
        0.5f, 0.5f, 0.0f,
    };
        
    triangleBatch.Begin(GL_TRIANGLE_FAN,4);
    triangleBatch.CopyVertexData3f(vVerts);

    triangleBatch.End();
}

void onRenderScene(void) {
    glClear(GL_COLOR_BUFFER_BIT|GL_DEPTH_BUFFER_BIT|GL_STENCIL_BUFFER_BIT);
    
    GLfloat vColor[] = {0.0f, 0.0f, 0.0f, 1.0f};
    shaderManager.UseStockShader(GLT_SHADER_IDENTITY,vColor);
    
    triangleBatch.Draw();
    
    glutSwapBuffers();
}


int main(int argc, char *argv[]){
    
    gltSetWorkingDirectory(argv[0]);
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_DOUBLE|GLUT_RGBA|GLUT_DEPTH|GLUT_STENCIL);
    glutInitWindowSize(800, 600);
    glutCreateWindow("窗口");
    
    glutReshapeFunc(onChangeSize);
    glutDisplayFunc(onRenderScene);
    
    GLenum status = glewInit();
    
    if(GLEW_OK != status) {
        fprintf(stderr,"glew error:%s\n",glewGetErrorString(status));
        return 1;
    }
    
    setupRC();
    
    glutMainLoop();

    return 0;
}


#pragma clang diagnostic pop
```

因为Apple已经将openGL标记为废弃了，在现有Xcode使用openGL API会出现废弃警告，使用如下代码将使用openGL API的代码包住，就不会出现警告了。

```cpp
#pragma clang diagnostic push
#pragma clang diagnostic ignored"-Wdeprecated-declarations"

// 代码

#pragma clang diagnostic pop
```

8、贴完代码后能够正常运行，说明已经配置完成。

![运行后窗口](https://www.thinkcode.cn/usr/uploads/2023/02/1668116457.png)


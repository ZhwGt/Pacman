# 吃豆人游戏

### 运行截图
![image](https://user-images.githubusercontent.com/51261084/135572326-d64d3331-fca8-4802-bcdd-c4c636e81a97.png)
![image](https://user-images.githubusercontent.com/51261084/135572448-dbe69a4d-45e0-4e63-9b1c-b5148162cf4d.png)
![image](https://user-images.githubusercontent.com/51261084/135572502-06942826-0882-4b23-8b35-5b155931e53f.png)

 ### 模块拆分

* 角色设计
* 地图设计
* 怪物设计
* 食物设计
* 操作设计
* 界面设计

### 项目目录结构

#### include 目录下
* control.h
* createpacman.h
* food.h
* gameover.h
* gameresult.h
* gamestart.h
* init.h
* laberynth.h
* main.h
* monster.h

#### src 目录下（相对应）

* control.cpp
* createpacman.cpp
* food.cpp
* gameover.cpp
* gameresult.cpp
* gamestart.cpp
* init.cpp
* labernth.cpp
* Pacman.cpp
* monster.cpp

#### 全局变量
```cpp
extern bool replay;         //检查是否启动游戏
extern bool over;             //检查游戏是否结束
extern float squareSize;     //一个单元大小
extern float xIncrement;     // x坐标
extern float yIncrement;     // y坐标
extern int rotation;         // 方向
extern float* monster1;     //第一个怪物的坐标和方向
extern float* monster2;     //第二个怪物的坐标和方向
extern float* monster3;     //第三个怪物的坐标和方向
extern float* monster4;     //第四个怪物的坐标和方向
extern vector<int> border;     //墙坐标

//障碍物坐标 (为了清晰分为三部分)
extern vector<int> obstaclesTop;
extern vector<int> obstaclesMiddle;
extern vector<int> obstaclesBottom;
extern deque<float> food;
extern vector<vector<bool>> bitmap;     // 2d图像，可移动区域
extern bool* keyStates;                 // 按键状态
extern int points; 
```

#### 重要函数
```cpp
void glutInit(int*argc,char**argv)；初始化
void glutInitDisplayMode(unsighed int mode)；定义显示方式 mode：是一个 GLUT 库里预定义的可能的布尔组合，使用 mode 去指定颜色模式，数量和缓冲区类型。
void glutInitWindowSize(int width,int height);设置窗口大小 width：窗口宽度 height：窗口高度
void glutInitWindowPositon(int x,int y);确定窗口位置（默认左上角） x:横坐标 y:纵坐标
int  glutCreateWindow(char* title);设置窗口的标题 title:标题内容
void glutDisplayFunc(void(*func)(void);注册当前窗口的显示回调函数 void (*func)(void)：回调函数名称，在这里我们用的是 display
void glutReshapeFunc(void(*func)(int width,int height));重新设置窗口 void(*func)(int width,int height):回调函数名称,在这里我们用的是 reshape
void glutIdleFunc(void(*func)(void));调用渲染函数 void(*func)(void):回调函数名称,系统空闲时调用，在这里我们用的是 display
void glutKeyboardFunc(void(*func)(unsigned char key,int x,int y));处理按键事件
void glutKeyboardUpFunc(void (*func)(unsigned char key,int x,int y));处理松开按键事件
void glutMainLoop(void);循环执行
```

### openGL库安装
```bash
sudo apt update
sudo apt install build-essential
sudo apt install libgl1-mesa-dev
sudo apt install libglu1-mesa-dev
sudo apt install freeglut3-dev
```

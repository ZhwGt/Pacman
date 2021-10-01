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
### 初始化地图
```cpp
//init.cpp
#include "init.h"
#include "main.h"
//初始化游戏
void init(void){
    //清除屏幕
    glClearColor(0.0, 0.0, 0.0, 0.0);
    glShadeModel(GL_FLAT);//着色
    //重置按键
    for (int i = 0; i < 256; i++){
        keyStates[i] = false;
    }
    //用障碍物填充地图
    bitmap.push_back({ true, true, true, true, true, true, true, true, true, true, true, true, true, true, true });
    bitmap.push_back({ true, false, false, false, false, false, false, false, false, false, false, false, false, false, true });
    bitmap.push_back({ true, false, true, true, true, true, false, true, true, false, true, true, true, false, true });
    bitmap.push_back({ true, false, false, false, false, true, false, true, false, false, false, false, true, false, true});
    bitmap.push_back({ true, false, true, true, false, false, false, false, false, true, true, false, false, false, true});
    bitmap.push_back({ true, false, false, true, true, false, true, true, true, true, false, false, true, false, true});
    bitmap.push_back({ true, true, false, false, false, false, true, false, true, true, false, true, true, false, true});
    bitmap.push_back({ true, true, true, true, true, false, false, false, true, false, false, false, false, false, true});
    bitmap.push_back({ true, true, false, false, false, false, true, false, true, true, false, true, true, false, true });
    bitmap.push_back({ true, false, false, true, true, false, true, true, true, true, false, false, true, false, true });
    bitmap.push_back({ true, false, true, true, false, false, false, false, false, true, true, false, false, false, true });
    bitmap.push_back({ true, false, false, false, false, true, false, true, false, false, false, false, true, false, true });
    bitmap.push_back({ true, false, true, true, true, true, false, true, true, false, true, true, true, false, true });
    bitmap.push_back({ true, false, false, false, false, false, false, false, false, false, false, false, false, false, true });
    bitmap.push_back({ true, true, true, true, true, true, true, true, true, true, true, true, true, true, true });
}
```
### 障碍物初始化
```cpp
//laberynth.cpp
#include "laberynth.h"
#include "main.h"
//障碍物与墙体的绘制方法
void drawLaberynth(){
    glColor3f(1.0, 1.0, 1.0);
    //边界
    for (vector<int>::size_type i = 0; i < border.size(); i = i + 4){
        glRectf(border.at(i) * squareSize, border.at(i + 1)*squareSize, border.at(i + 2)*squareSize, border.at(i + 3)*squareSize);
    }

    //障碍物，分为上中下三部分绘图
    for (vector<int>::size_type j = 0; j < obstaclesBottom.size(); j = j + 4){
        glRectf(obstaclesBottom.at(j) * squareSize, obstaclesBottom.at(j + 1)*squareSize, obstaclesBottom.at(j + 2)*squareSize, obstaclesBottom.at(j + 3)*squareSize);
    }
    for (vector<int>::size_type k = 0; k < obstaclesMiddle.size(); k = k + 4){
        glRectf(obstaclesMiddle.at(k) * squareSize, obstaclesMiddle.at(k + 1)*squareSize, obstaclesMiddle.at(k + 2)*squareSize, obstaclesMiddle.at(k + 3)*squareSize);
    }
    for (vector<int>::size_type p = 0; p < obstaclesTop.size(); p = p + 4){
        glRectf(obstaclesTop.at(p) * squareSize, obstaclesTop.at(p + 1)*squareSize, obstaclesTop.at(p + 2)*squareSize, obstaclesTop.at(p + 3)*squareSize);
    }
}
```

### 游戏角色制作
```cpp
//createpacman.cpp
#include "createpacman.h"
#include "GL/gl.h"
#include "main.h"

void drawPacman(float positionX, float positionY, float rotation){
    int x, y;
    glBegin(GL_LINES);    //创建一条线
    glColor3f(1.0, 1.0, 0.0);
    for (int k = 0; k < 32; k++){
        x = (float)k / 2.0 * cos((30 + 90*rotation) * M_PI / 180.0) + (positionX*squareSize);
        y = (float)k / 2.0* sin((30 + 90 * rotation) * M_PI / 180.0) + (positionY*squareSize);
        for (int i = 30; i < 330; i++){
            glVertex2f(x, y);
            x = (float)k / 2.0 * cos((i + 90 * rotation) * M_PI / 180.0) + (positionX*squareSize);
            y = (float)k / 2.0* sin((i + 90 * rotation) * M_PI / 180.0) + (positionY*squareSize);
            glVertex2f(x, y);
        }
    }
    glEnd();//绘图结束
}
```

### 怪物设计
```cpp
//monster.cpp
#include "monster.h"
#include "main.h"
//绘画怪物
void drawMonster(float positionX, float positionY, float r, float g, float b){
    int x, y;
    glBegin(GL_LINES);
    glColor3f(r, g, b);
    //头
    for (int k = 0; k < 32; k++){
        x = (float)k / 2.0 * cos(360 * M_PI / 180.0) + (positionX*squareSize);
        y = (float)k / 2.0* sin(360 * M_PI / 180.0) + (positionY*squareSize);
        for (int i = 180; i <= 360; i++){
            glVertex2f(x, y);
            x = (float)k / 2.0 * cos(i * M_PI / 180.0) + (positionX*squareSize);
            y = (float)k / 2.0* sin(i * M_PI / 180.0) + (positionY*squareSize);
            glVertex2f(x, y);
        }
    }
    glEnd();
    //身体
    glRectf((positionX*squareSize) - 17, positionY*squareSize, (positionX*squareSize) + 15, (positionY*squareSize) + 15);
    glBegin(GL_POINTS);
    glColor3f(0, 0.2, 0.4);
    //眼睛和腿
    glVertex2f((positionX*squareSize) - 11, (positionY*squareSize) + 14); //legs
    glVertex2f((positionX*squareSize) - 1, (positionY*squareSize) + 14); //legs
    glVertex2f((positionX*squareSize) + 8, (positionY*squareSize) + 14); //legs
    glVertex2f((positionX*squareSize) + 4, (positionY*squareSize) - 3); //eyes
    glVertex2f((positionX*squareSize) - 7, (positionY*squareSize) - 3); //eyes
    glEnd();
}

//怪物移动
void updateMonster(float* monster, int id){
        //找到当前位置
        int x1Quadrant = (int)((monster[0] - (2/squareSize)) - (16.0 *cos(360 * M_PI / 180.0)) / squareSize);
        int x2Quadrant = (int)((monster[0] + (2/squareSize)) + (16.0 *cos(360 * M_PI / 180.0)) / squareSize);
        int y1Quadrant = (int)((monster[1] - (2/squareSize)) - (16.0 *cos(360 * M_PI / 180.0)) / squareSize);
        int y2Quadrant = (int)((monster[1] + (2/squareSize)) + (16.0 *cos(360 * M_PI / 180.0)) / squareSize);
        //怪物移动和撞墙检测
        switch ((int)monster[2]){
        case 1:
            if (!bitmap.at(x1Quadrant).at((int)monster[1])){
                monster[0] -= 2 / squareSize;
            }else {
                int current = monster[2];
                do{
                    monster[2] =  (rand() % 4) + 1;
                } while (current == (int) monster[2]);
            }
            break;
        case 2:
            if (!bitmap.at(x2Quadrant).at((int)monster[1])){
                monster[0] += 2 / squareSize;
            }
            else {
                int current = monster[2];
                do{
                    monster[2] = (rand() % 4) + 1;
                } while (current == (int)monster[2]);
            }
            break;
        case 3:
            if (!bitmap.at((int)monster[0]).at(y1Quadrant)){
                monster[1] -= 2 / squareSize;
            }
            else {
                int current = monster[2];
                do{
                    monster[2] = (rand() % 4) + 1;
                } while (current == (int)monster[2]);
            }
            break;
        case 4:
            if (!bitmap.at((int)monster[0]).at(y2Quadrant)){
                monster[1] += 2 / squareSize;
            }
            else {
                int current = monster[2];
                do{
                    monster[2] = (rand() % 4) + 1;
                } while (current == (int)monster[2]);
            }
            break;
        default:
            break;
        }
}
```

### 食物设计
```cpp
//food.cpp
#include "food.h"
#include "main.h"

//检查食物是否被吃
bool foodEaten(int x, int y, float pacmanX, float pacmanY){
    if (x >= pacmanX - 16.0 *cos(359 * M_PI / 180.0) && x <= pacmanX + 16.0*cos(359 * M_PI / 180.0)){
        if (y >= pacmanY - 16.0*cos(359 * M_PI / 180.0) && y <= pacmanY + 16.0*cos(359 * M_PI / 180.0)){
            return true;
        }
    }
    return false;
}

//画上食物
void drawFood(float pacmanX, float pacmanY){
    deque<float> temp;
    //检查食物是否没有被吃掉
    for (deque<float>::size_type i = 0; i < food.size(); i = i + 2){
        if (!foodEaten(food.at(i)*squareSize, food.at(i + 1)*squareSize, pacmanX, pacmanY)){
            temp.push_back(food.at(i));
            temp.push_back(food.at(i + 1));
        }
        else {
            points++;
        }
    }
    food.swap(temp);
    glPointSize(5.0);
    glBegin(GL_POINTS);
    glColor3f(1.0, 1.0, 1.0);
    //画上食物
    for (deque<float>::size_type j = 0; j < food.size(); j = j + 2){
        glVertex2f(food.at(j)*squareSize, food.at(j + 1)*squareSize);//画点
    }
    glEnd();
}
```

### 角色移动设计
```cpp
//control.cpp
#include "control.h"
#include "main.h"
//设置按键
void keyPressed(unsigned char key, int x, int y){
    keyStates[key] = true;
}

//释放按键
void keyUp(unsigned char key, int x, int y){
    keyStates[key] = false;
}

//重置所有元素并开始游戏
void resetGame(){
    over = false;
    xIncrement = 0;
    yIncrement = 0;
    rotation = 0;
    monster1 = new float[3] {10.5, 8.5, 1.0};
    monster2 = new float[3] {13.5, 1.5, 2.0};
    monster3 = new float[3] {4.5, 6.5, 3.0};
    monster4 = new float[3] {2.5, 13.5, 4.0};
    points = 0;
    for (int i = 0; i < 256; i++){
        keyStates[i] = false;
    }
    food = { 1.5, 1.5, 1.5, 2.5, 1.5, 3.5, 1.5, 4.5, 1.5, 5.5, 1.5, 6.5, 1.5, 7.5, 1.5, 8.5, 1.5, 9.5, 1.5, 10.5, 1.5, 11.5, 1.5, 12.5, 1.5, 13.5, 2.5, 1.5, 2.5, 6.5, 2.5, 9.5, 2.5, 13.5, 3.5, 1.5, 3.5, 2.5, 3.5, 3.5, 3.5, 4.5, 3.5, 6.5, 3.5, 8.5, 3.5, 9.5, 3.5, 10.5, 3.5, 11.5, 3.5, 13.5, 4.5, 1.5, 4.5, 4.5, 4.5, 5.5, 4.5, 6.5, 4.5, 7.5, 4.5, 8.5, 4.5, 11.5, 4.5, 12.5, 4.5, 13.5, 5.5, 1.5, 5.5, 2.5, 5.5, 5.5, 5.5, 10.5, 5.5, 11.5, 5.5, 13.5, 6.5, 2.5, 6.5, 3.5, 6.5, 4.5, 6.5, 5.5, 6.5, 7.5, 6.5, 10.5, 6.5, 13.5, 7.5, 5.5, 7.5, 6.5, 7.5, 7.5, 7.5, 9.5, 7.5, 10.5, 7.5, 11.5, 7.5, 12.5, 7.5, 13.5, 8.5, 2.5, 8.5, 3.5, 8.5, 4.5, 8.5, 5.5, 8.5, 7.5, 8.5, 10.5, 8.5, 13.5, 9.5, 1.5, 9.5, 2.5, 9.5, 5.5, 9.5, 10.5, 9.5, 11.5, 9.5, 13.5, 10.5, 1.5, 10.5, 4.5, 10.5, 5.5, 10.5, 6.5, 10.5, 7.5, 10.5, 8.5, 10.5, 11.5, 10.5, 12.5, 10.5, 13.5, 11.5, 1.5, 11.5, 2.5, 11.5, 3.5, 11.5, 4.5, 11.5, 5.5, 11.5, 6.5, 11.5, 8.5, 11.5, 9.5, 11.5, 10.5, 11.5, 11.5, 11.5, 13.5, 12.5, 1.5, 12.5, 6.5, 12.5, 9.5, 12.5, 13.5, 13.5, 1.5, 13.5, 2.5, 13.5, 3.5, 13.5, 4.5, 13.5, 5.5, 13.5, 6.5, 13.5, 7.5, 13.5, 8.5, 13.5, 9.5, 13.5, 10.5, 13.5, 11.5, 13.5, 12.5, 13.5, 13.5 };
}

//控制吃豆人移动
void keyOperations(){
    //获得当前位置
    float  x = (1.5 + xIncrement) * squareSize;
    float y = (1.5 + yIncrement) * squareSize;
    //更新按键
    if (keyStates['a']){
        x -= 2;
        int x1Quadrant = (int)((x - 16.0 *cos(360 * M_PI / 180.0)) / squareSize);
        if (!bitmap.at(x1Quadrant).at((int)y/squareSize)){
            xIncrement -= 2 / squareSize;
            rotation = 2;
        }
    }
    if (keyStates['d']){
        x += 2;
        int x2Quadrant = (int)((x + 16.0 *cos(360 * M_PI / 180.0)) / squareSize);
        if (!bitmap.at(x2Quadrant).at((int)y / squareSize)){
            xIncrement += 2 / squareSize;
            rotation = 0;
        }
    }
    if (keyStates['w']){
        y -= 2;
        int y1Quadrant = (int)((y - 16.0 *cos(360 * M_PI / 180.0)) / squareSize);
        if (!bitmap.at((int)x/squareSize).at(y1Quadrant)){
            yIncrement -= 2 / squareSize;
            rotation = 3;
        }
    }
    if (keyStates['s']){
        y += 2;
        int y2Quadrant = (int)((y + 16.0 *cos(360 * M_PI / 180.0)) / squareSize);
        if (!bitmap.at((int)x / squareSize).at(y2Quadrant)){
            yIncrement += 2 / squareSize;
            rotation = 1;
        }
    }
    if (keyStates[' ']){
        if (!replay && over){
            resetGame();
            replay = true;
        }
        else if (replay && over){
            replay = false;
        }
    }
}
```

### 进入界面

```cpp
//gamestart.cpp
#include "gamestart.h"
#include "monster.h"
#include "createpacman.h"
#include "laberynth.h"
#include "main.h"
#include "gameover.h"
#include "food.h"
#include "gameresult.h"
#include "control.h"

//欢迎界面
void welcomeScreen(){
    glClearColor(0, 0.2, 0.4, 1.0);
    string message = "*************************************";
    string::iterator it = message.begin();
    glRasterPos2f(150, 200);
    while (it!=message.end())
        glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, *it++);
    message = "Pacman - by HD";
    glColor3f(1, 1, 1);
    glRasterPos2f(225, 250);
    it = message.begin();
    while (it!=message.end())
        glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, *it++);
    message = "*************************************";
    glRasterPos2f(150, 300);
    it = message.begin();
    while (it!=message.end())
        glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, *it++);
    message = "To control Pacman use A to go right, D to go left, W to go up and S to go down.";
    glRasterPos2f(50, 400);
    it = message.begin();
    while (it!=message.end())
        glutBitmapCharacter(GLUT_BITMAP_HELVETICA_18, *it++);
    message = "To start or restart the game, press the space key.";
    glRasterPos2f(170, 450);
    it = message.begin();
    while (it!=message.end())
        glutBitmapCharacter(GLUT_BITMAP_HELVETICA_18, *it++);
}

//显示屏幕和元素
void display(){
    if (points == 1){
        over = false;
    }
    keyOperations();
    glClear(GL_COLOR_BUFFER_BIT);    //清除颜色缓冲区(当前被激活为写操作的颜色缓存)
    gameOver();
    if (replay){
        if (!over){
            drawLaberynth();
            drawFood((1.5 + xIncrement) * squareSize, (1.5 + yIncrement) * squareSize);
            drawPacman(1.5 + xIncrement, 1.5 + yIncrement, rotation);
            updateMonster(monster1, 1);
            updateMonster(monster2, 2);
            updateMonster(monster3, 3);
            updateMonster(monster4, 4);
            drawMonster(monster1[0], monster1[1], 0.0, 1.0, 1.0); //cyan
            drawMonster(monster2[0], monster2[1], 1.0, 0.0, 0.0); //red
            drawMonster(monster3[0], monster3[1], 1.0, 0.0, 0.6); //magenta
            drawMonster(monster4[0], monster4[1], 1.0, 0.3, 0.0); //orange
        }
        else {
            resultsDisplay();
        }
    }
    else {
        welcomeScreen();
    }
    glutSwapBuffers();
}

//重置窗口
void reshape(int w, int h){
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    glViewport(0, 0, (GLsizei)w, (GLsizei)h);
    glOrtho(0, 750, 750, 0, -1.0, 1.0);
    glMatrixMode(GL_MODELVIEW);
    glLoadIdentity();
}
```

### 游戏结果判断
```cpp
//gameover.cpp
#include "gameover.h"
#include "main.h"
//游戏结束
void gameOver(){
    int pacmanX = (int)(1.5 + xIncrement);
    int pacmanY = (int)(1.5 + yIncrement);
    int monster1X = (int)(monster1[0]);
    int monster1Y = (int)(monster1[1]);
    int monster2X = (int)(monster2[0]);
    int monster2Y = (int)(monster2[1]);
    int monster3X = (int)(monster3[0]);
    int monster3Y = (int)(monster3[1]);
    int monster4X = (int)(monster4[0]);
    int monster4Y = (int)(monster4[1]);
    if (pacmanX == monster1X && pacmanY == monster1Y){
        over = true;
    }
    if (pacmanX == monster2X && pacmanY == monster2Y){
        over = true;
    }
    if (pacmanX == monster3X && pacmanY == monster3Y){
        over = true;
    }
    if (pacmanX == monster4X && pacmanY == monster4Y){
        over = true;
    }
    if (points == 106){
        over = true;
    }
}
```

### 游戏结果界面
```cpp
//gameresult.cpp
#include "gameresult.h"
#include "main.h"
//游戏结果
void resultsDisplay(){
    if (points == 106){
        //胜利
        string message = "*************************************";
        string::iterator it = message.begin();
        glRasterPos2f(170, 250);
        while (it!=message.end())
            glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, *it++);
        message = "CONGRATULATIONS, YOU WON! ";
        glColor3f(1, 1, 1);
        glRasterPos2f(200, 300);
        it = message.begin();
        while (it!=message.end())
            glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, *it++);
        message = "*************************************";
        glRasterPos2f(170, 350);
        it = message.begin();
        while (it!=message.end())
            glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, *it++);
        message = "To start or restart the game, press the space key.";
        glRasterPos2f(170, 550);
        it = message.begin();
        while (it!=message.end())
            glutBitmapCharacter(GLUT_BITMAP_HELVETICA_18, *it++);
    }else {
        //Lost
        string message = "*************************************";
        string::iterator it = message.begin();
        glRasterPos2f(210, 250);
        while (it!=message.end())
            glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, *it++);
        message = "SORRY, YOU LOST ... ";
        glColor3f(1, 1, 1);
        glRasterPos2f(250, 300);
        it = message.begin();
        while (it!=message.end())
            glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, *it++);
        message = "*************************";
        glRasterPos2f(210, 350);
        it = message.begin();
        while (it!=message.end())
            glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, *it++);
        message = "You got: ";
        glRasterPos2f(260, 400);
        it = message.begin();
        while (it!=message.end())
            glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, *it++);
        string result = to_string(points);
        message = (char*)result.c_str();
        glRasterPos2f(350, 400);
        it = message.begin();
        while (it!=message.end())
            glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, *it++);
        message = " points!";
        glRasterPos2f(385, 400);
        it = message.begin();
        while (it!=message.end())
            glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, *it++);
        message = "To start or restart the game, press the space key.";
        glRasterPos2f(170, 550);
        it = message.begin();
        while (it!=message.end())
            glutBitmapCharacter(GLUT_BITMAP_HELVETICA_18, *it++);
    }
}
```

### Makefile
```cpp
#编译方式
CC = g++
#C++版本，显示所有警告
VERSION = -g -std=c++11 -Wall
#头文件和库文件
INCLUDE = -I../include -lglut -lGL
#目标文件，最后生成文件
TARGET = ../bin/Pacman
#源代码路径
SRCS = $(wildcard *.cpp)
#编译为.o文件
OBJS = $(patsubst %cpp,%o,$(SRCS))

all:$(TARGET)
#执行编译
$(TARGET):$(OBJS)
    $(CC) $(OBJS) $(VERSION) $(INCLUDE) -o $(TARGET)

%.o:%.cpp
    $(CC) $(VERSION) $(INCLUDE) -c $<
#清除
.PHONY:clean
clean:
    rm -rf $(OBJS) $(TARGET)
```

### 

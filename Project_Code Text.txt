#include <windows.h>
#include<stdio.h>
#include<iostream>
#include<GL/glut.h>
#include<string>
#include<sstream>

#define Sleep
using namespace std;
float barX = 200, barY = 465, barWidth = 80, barheight = 5;
float ballVelX = 2.0, ballVelY = 2.0;
const int brickAmount = 100;
int score = 0, chances = 3, previousScore = 0, highestScore = 0;
bool flag = true, flag2 = true;


class Cuboid
{
    static const float cubeVertices[][3];
    static const int cubeIndicies[];
    static const int faceColours[][3];
    
    float mX,mY,mWidth,mHeight,mDepth; 
    
public:
    Cuboid(float x, float y, float width, float height, float depth = 30) 
    {
        mX = x;
        mY = y;
        mWidth = width;
        mHeight = height;
        mDepth = depth;
    }
    
    Cuboid() {}
    
    void draw()
    {
        glPushMatrix();
        glTranslatef(mX, mY, 0);
    
        glBegin(GL_TRIANGLES);
        int cubeFace = 0;
        for(int i = 0; i < 36; i++)
        {
            if(i % 6 == 0)
                glColor3ub(faceColours[cubeFace][0], faceColours[cubeFace][1], faceColours[cubeFace++][2]);
            
            glVertex3f((cubeVertices[cubeIndicies[i]][0] * mWidth), (cubeVertices[cubeIndicies[i]][1] * mHeight), cubeVertices[cubeIndicies[i]][2] * mDepth);
        }
        glEnd();
        glPopMatrix();
    }
    
    float getPosX()   { return mX; }
    float getPosY()   { return mY; }
    float getWidth()  { return mWidth; }
    float getHeight() { return mHeight;}
    void move(float x, float y) { mX += x; mY += y; }
    void setPos(float x, float y) { mX = x; mY = y; }
};


const float Cuboid::cubeVertices[][3] = {
    {0, 0, 0}, 
    {1.0f, 0, 0},
    {1.0f, 1.0f, 0},
    {0, 1.0f, 0},
    {0, 0, 1.0f}, 
    {1.0f, 0, 1.0f},
    {1.0f, 1.0f, 1.0f},
    {0, 1.0f, 1.0f} };

const int Cuboid::cubeIndicies[] = {
    5, 4, 0, 1, 5, 0, 6, 5, 1, 2, 6, 1,
    7, 6, 2, 3, 7, 2, 4, 7, 3, 0, 4, 3,
    6, 7, 4, 5, 6, 4, 1, 0, 3, 2, 1, 3 };
                                    
    
const int Cuboid::faceColours[][3] = {
    {255, 0, 0},
    {255, 255, 0},
    {0, 255, 255},
    {0, 255, 0},
    {0, 0, 255},
    {255, 0, 255} };

Cuboid ball(235, 430, 10, 10);
Cuboid bar(234, 465, 80, 5);


struct bricks{
    float x;
    float y;
    float width;
    float height;
    bool isAlive ;
	
};

bricks bricksArray[brickAmount];




void createBricks(){ 
    float brickX = 41, brickY = 50;
    for(int i=0;i<brickAmount;i++){
        if(brickX > 400){
            brickX = 41;
            brickY += 11;
        }
        bricksArray[i].x = brickX;
        bricksArray[i].y = brickY;
        bricksArray[i].width = 38.66;
        bricksArray[i].height = 10;
        brickX += 39.66;
    }
    glColor3ub(0,0,255);
    for(int i=0;i<brickAmount;i++){
        if(bricksArray[i].isAlive == true){
            Cuboid brick(bricksArray[i].x, bricksArray[i].y, bricksArray[i].width, bricksArray[i].height);
            brick.draw();

        }
    }
}

void print(int a){
    glRasterPos2f(490, 40);
    stringstream ss;
    ss << a;
    string s = "Score: "+ss.str();
    int len = s.length();
    for(int i = 0; i < len; i++){
        glutBitmapCharacter(GLUT_BITMAP_HELVETICA_18, s[i]);
    }
    glRasterPos2f(490, 70);
    stringstream ss2;
    ss2 << chances;
    string chance = "Chances left: "+ss2.str();
    for(int i = 0; i < chance.length(); i++){
        glutBitmapCharacter(GLUT_BITMAP_HELVETICA_12, chance[i]);
    }
    glRasterPos2f(490, 100);
    stringstream ss3;
    ss3 << previousScore;
    string prevScore = "Previous score: "+ss3.str();
    for(int i = 0; i < prevScore.length(); i++){
        glutBitmapCharacter(GLUT_BITMAP_HELVETICA_12, prevScore[i]);
    }
    glRasterPos2f(490, 130);
    stringstream ss4;
    ss4 << highestScore;
    string highScore = "Highest score: "+ss4.str();
    for(int i = 0; i < highScore.length(); i++){
        glutBitmapCharacter(GLUT_BITMAP_HELVETICA_12, highScore[i]);
    }

}

void message(bool a){
    if(a == false){
        glRasterPos2f(20, 400);
        stringstream ss;
        ss << previousScore;
        string s = "Game over. Your score: "+ss.str()+". Click to start new game.";
        int len = s.length();
        for(int i = 0; i < len; i++){
            glutBitmapCharacter(GLUT_BITMAP_HELVETICA_18, s[i]);
        }
    }
}

void completeMessage(bool a){
    if(a == false){
        glRasterPos2f(20, 400);
        stringstream ss;
        ss << score;
        string s = "STAGE COMPLETE. Your score: "+ss.str()+". Click to start new game.";
        int len = s.length();
        for(int i = 0; i < len; i++){
            glutBitmapCharacter(GLUT_BITMAP_HELVETICA_12, s[i]);
        }
    }
}

void myDisplay(void){
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    glColor3f (0.0, 0.0, 0.0);
    glPushMatrix();
    glRotatef(10, 240, 240, 0);
    ball.draw();
    bar.draw();
    createBricks();
    glPopMatrix();
    glBegin(GL_QUADS);
    glColor3ub(0, 0, 0);
    glVertex2f(480, 0);
    glVertex2f(480, 480);
    glVertex2f(485, 480);
    glVertex2f(485, 0);
    glEnd();
    print(score);
    message(flag);
    completeMessage(flag2);
    glFlush();
    glutSwapBuffers();
}

void myInit (void){
    glClearColor(1.0, 1.0, 1.0, 0.0);
    glColor3f(0.0f, 0.0f, 0.0f);
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    glOrtho(0.0, 760.0, 480.0, 0.0, -300, 300);
    
    glEnable(GL_DEPTH_TEST);  
}

bool checkCollision(float aX, float aY, float aW, float aH, float bX, float bY, float bW, float bH){
    if ( aY+aH < bY )
        return false;
    else if ( aY > bY+bH )
        return false;
    else if ( aX+aW < bX )
        return false;
    else if ( aX > bX+bW )
        return false;
    else
        return true;
}

void moveBall(){
    if(score >= 300){
        ballVelX = 0.5;
        ballVelY = 0.5;
    }
    if(score >= 1000){
        bar.setPos(200, 465);
        
        ball.setPos(235, 430);
        ballVelX = 0;
        ballVelY = 0;
        float brickX = 2, brickY = 2;
        for(int i=0;i<brickAmount;i++){
            if(brickX > 450){
                brickX = 2;
                brickY += 11;
            }
            bricksArray[i].x = brickX;
            bricksArray[i].y = brickY;
            bricksArray[i].width = 38.66;
            bricksArray[i].height = 10;
            bricksArray[i].isAlive = true;
            brickX += 39.66;
        }
        previousScore = score;
        if(highestScore < score){
            highestScore = score;
        }
        chances = 3;
        score = 0;
        flag2 = false;
        Sleep(3000);
        completeMessage(flag2);
    }
    else{
        ball.move(ballVelX, 0);
        for (int i=0; i<brickAmount; i++){
            if(bricksArray[i].isAlive == true){
                if(checkCollision(ball.getPosX(), ball.getPosY(), ball.getWidth(), ball.getHeight(), bricksArray[i].x, bricksArray[i].y, bricksArray[i].width, bricksArray[i].height) == true){
                    ballVelX = -ballVelX;
                    bricksArray[i].isAlive = false;
                    score += 10;
                    break;
                }
            }
        }
        ball.move(0, ballVelY);
        for (int i=0; i<brickAmount; i++){
            if(bricksArray[i].isAlive == true){
                if(checkCollision(ball.getPosX(), ball.getPosY(), ball.getWidth(), ball.getHeight(), bricksArray[i].x, bricksArray[i].y, bricksArray[i].width, bricksArray[i].height) == true){
                    ballVelY = -ballVelY;
                    bricksArray[i].isAlive = false;
                    score += 10;
                    break;
                }
            }
        }
        if(ball.getPosX() < 0){
            ballVelX = -ballVelX;
        }
        else if(ball.getPosX()+ball.getWidth() > 480){
            ballVelX = -ballVelX;
        }
        if(ball.getPosY() < 0){
            ballVelY = -ballVelY;
        }
        else if(ball.getPosY()+ball.getHeight() > 480){
            if(chances <= 1){
                bar.setPos(200, 465);
                ball.setPos(235, 430);
                ballVelX = 0;
                ballVelY = 0;
                float brickX = 2, brickY = 2;
                for(int i=0;i<brickAmount;i++){
                    if(brickX > 450){
                        brickX = 2;
                        brickY += 11;
                    }
                    bricksArray[i].x = brickX;
                    bricksArray[i].y = brickY;
                    bricksArray[i].width = 38.66;
                    bricksArray[i].height = 10;
                    bricksArray[i].isAlive = true;
                    brickX += 39.66;
                }
                previousScore = score;
                if(highestScore < score){
                    highestScore = score;
                }
                chances = 3;
                score = 0;
                flag = false;
                Sleep(3000);
                message(flag);
            }
            else{
                chances--;
                ball.setPos(235, 430);
                if(ballVelY < 0){
                    ballVelY = -ballVelY;
                }
                Sleep(3000);
            }
        }
        if(checkCollision(ball.getPosX(), ball.getPosY(), ball.getWidth(), ball.getHeight(), bar.getPosX(), bar.getPosY(), bar.getWidth(), bar.getHeight()) == true){
            ballVelY = -ballVelY;
        }
    }
    glutPostRedisplay();
}

void keyboard(int key, int x, int y){
    switch(key){
        case GLUT_KEY_LEFT:
            bar.move(-10, 0);
            if(bar.getPosX() < 0){
                bar.setPos(0, bar.getPosY());
            }
            glutPostRedisplay();
            break;
        case GLUT_KEY_RIGHT:
            bar.move(10, 0);
            if(bar.getPosX()+bar.getWidth() > 480){
                bar.setPos(480-bar.getWidth(), bar.getPosY());
            }
            glutPostRedisplay();
            break;
        default:
            break;
    }
}

void mouse(int button,int state,int x,int y){
    switch(button){
        case GLUT_LEFT_BUTTON:
            if(state==GLUT_DOWN){
                flag = true;
                if(ballVelX <= 0 && ballVelY <= 0){
                    ballVelX = 0.3;
                    ballVelY = 0.3;
                }
                glutIdleFunc(moveBall);
            }
            break;
        default:
            break;
    }
}



int main(int argc, char** argv){
    for(int i=0;i<brickAmount;i++)
    {
        bricksArray[i].isAlive = true;
    }
        
    glutInit(&argc, argv);
glutInitDisplayMode(GLUT_DOUBLE|GLUT_RGB);
glutInitWindowSize(760,480);
glutInitWindowPosition(100,150);
glutCreateWindow("Brick Breaker By hamood ,kimo,obbai and mahran");
glutDisplayFunc(myDisplay);
glutSpecialFunc(keyboard);
glutMouseFunc(mouse);
myInit();
glutMainLoop();
}



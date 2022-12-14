---
layout:post
title:my first c++ project
date:2022-12-1
author:xyw
catalog:true
tags:
    -Blog
---
# 第一个C++项目（一个无聊的游戏）



## 原先的期末作业，后来期末提前取消了

未完全完工，可能有亿点bug

## 基本玩法

按鼠标中键，翻开国旗

若你看到了两个相同的国旗，先后用鼠标右键点击即可消除

****

## 代码

'#include<iostream>
#include<Windows.h>
#include<graphics.h>
#include<vector>
#include<time.h>
using namespace std;
#define ROWCOUNT 10 //行的图数
#define COLCOUNT 16 //列的图数
#define FRONTWIDTH 41//一个格子的宽
#define FRONTHEIGHT 51//一个格子的高
#define BKWIDTH 46//一个格子的背景宽
#define BKHEIGHT 56//一个格子的背景高
#define KEY_DOWN(VK_NONAME) ((GetAsyncKeyState(VK_NONAME) & 0x8000) ? 1:0)
int gamemap[ROWCOUNT][COLCOUNT]={0};//存储图案
int a[ROWCOUNT][COLCOUNT]={0};//储存点击状态
int b[1000]={-1,-2,-3,-4,0};//第n次点击的图片的编号
int x[1000]={0};//第n次点击的图片的x坐标
int y[1000]={0};//第n次点击的图片的y坐标
int flag3=0;
double it=0;
void setmap();
void printmap();//命令行
void drawmap();//图形界面显示
void print();//翻转卡牌
void TIME();//单次计时函数
void init();
IMAGE backcard,card,qw,back;
DWORD WINAPI Threadl(LPVOID IpParameter);//计时线程
void main()//主函数已用过的变量名num,n,a[][],b[],i,x[],y[]
{
    init();
    //initgraph(COLCOUNT*BKWIDTH,ROWCOUNT*BKHEIGHT,SHOWCONSOLE);//窗口初始化,窗口大小由格子的大小与格子的数量决定
    srand(time(NULL));
    loadimage(&backcard,"backcard.jpg");
    loadimage(&card,"card.jpg");
    loadimage(&qw,"qw.jpg");
    loadimage(&back,"back.bmp");
    setmap();
    //printmap();
    drawmap();
    MOUSEMSG msg;//获取鼠标位置
    int n=0,i=4;
    int flag=1,flag2=1;
    while(1)
    {  
        msg = GetMouseMsg();

        if((msg.mkMButton||msg.mkLButton)&&flag==1)
        {
            HANDLE hThreadl=CreateThread(NULL,0,Threadl,NULL,0,NULL);
            flag=0;
            CloseHandle(hThreadl);
    
    
        }
    
        if(msg.mkMButton&&(a[msg.y/BKHEIGHT][msg.x/BKWIDTH]==1||a[msg.y/BKHEIGHT][msg.x/BKWIDTH]==0))
        {
            //printf("%d:(%d,%d)\n",n%2,msg.x/BKWIDTH,msg.y/BKHEIGHT);
            n++;
            putimage((msg.x/BKWIDTH)*BKWIDTH,(msg.y/BKHEIGHT)*BKHEIGHT,BKWIDTH,BKHEIGHT,&qw,0,gamemap[msg.y/BKHEIGHT][msg.x/BKWIDTH]*(BKHEIGHT));
            a[msg.y/BKHEIGHT][msg.x/BKWIDTH]=1;
            TIME();
            putimage((msg.x/BKWIDTH)*BKWIDTH,(msg.y/BKHEIGHT)*BKHEIGHT,BKWIDTH,BKHEIGHT,&backcard,0,0);
            a[msg.y/BKHEIGHT][msg.x/BKWIDTH]=0;
        }
            if((msg.mkLButton&&(a[msg.y/BKHEIGHT][msg.x/BKWIDTH]==1||a[msg.y/BKHEIGHT][msg.x/BKWIDTH]==0))&&(x[i]!=msg.x/BKWIDTH||y[i]!=msg.y/BKHEIGHT))
            {
                i++;
                b[i]=gamemap[msg.y/BKHEIGHT][msg.x/BKWIDTH]*(BKHEIGHT);//改成点击的图片对应的编号
                x[i]=msg.x/BKWIDTH;
                y[i]=msg.y/BKHEIGHT;
    
            }
            if(msg.mkLButton&&(a[msg.y/BKHEIGHT][msg.x/BKWIDTH]==1||a[msg.y/BKHEIGHT][msg.x/BKWIDTH]==0)&&(b[i]==b[i-1]&&b[i]!=b[i-2]||(b[i]==b[i-1]&&b[i]==b[i-2]&&b[i]==b[i-3])))//加上两个图片相同的条件//&&b[i]==b[i-1]&&b[i]!=b[i-2])//加上两个图片相同的条件,消除判定
            {
                putimage(x[i-1]*BKWIDTH,y[i-1]*BKHEIGHT,BKWIDTH,BKHEIGHT,&qw,0,b[i-1]);
                putimage(x[i]*BKWIDTH,y[i]*BKHEIGHT,BKWIDTH,BKHEIGHT,&qw,0,b[i]);
                TIME();
                a[msg.y/BKHEIGHT][msg.x/BKWIDTH]=2;
                a[y[i-1]][x[i-1]]=2;
                putimage((msg.x/BKWIDTH)*BKWIDTH,(msg.y/BKHEIGHT)*BKHEIGHT,BKWIDTH,BKHEIGHT,&back,0,0);
                putimage((x[i-1])*BKWIDTH,(y[i-1])*BKHEIGHT,BKWIDTH,BKHEIGHT,&back,0,0);
            }
            flag2=1;
            if(it>5)
            {
            for(int i=0;i<10;i++)
            {
               for(int j=0;j<16;j++)
                {
                  if(a[i][j]!=3)
                  {
                      flag2=0;
                   }
                 }
             }
            if(flag2==1)
            {
                flag3=1;
                //ExitHandle;
                //
            }
            if(flag3==1)
            {
                cout<<"游戏结束"<<endl;
                cout<<"你的得分是"<<endl;
                cout<<"你的用时是"<<it<<endl;
                break;
    
            }
            }
        /*if(msg.mkLButton)
        {
            printf("%d:(%d,%d)\n",n%2,msg.x/BKWIDTH,msg.y/BKHEIGHT);
            n++;
            putimage((msg.x/BKWIDTH)*BKWIDTH,(msg.y/BKHEIGHT)*BKHEIGHT,BKWIDTH,BKHEIGHT,&qw,0,gamemap[msg.y/BKHEIGHT][msg.x/BKWIDTH]*(BKHEIGHT));
            time();
        putimage((msg.x/BKWIDTH)*BKWIDTH,(msg.y/BKHEIGHT)*BKHEIGHT,BKWIDTH,BKHEIGHT,&backcard,0,0);
        a[msg.y/BKHEIGHT][msg.x/BKWIDTH]=0;
        }*/
    
    }
    
    
    
    
    while(1);

}//停顿

void setmap()               //随机生成模块
{  
    vector<int> v;                                          ;//在map数组中删去拿出的元素
for(int i=0;i<ROWCOUNT*COLCOUNT/16;i++)          //总共有40种图案,将图案有序排列至map数组中
{
    for(int j=0;j<16;j++)                             //每种4个
    {
        v.push_back(i);                                    //map数组中将充满4个0,4个1……
    }
}
//将map数组中的图案放到gamemap数组中
     int index;
    for(int i=0;i<ROWCOUNT*COLCOUNT;i++)
{
index=rand()%v.size();
gamemap[i/COLCOUNT][i%COLCOUNT]=v[index];
v.erase(v.begin()+index);
}
}

void printmap()                             //显示模块1
{
    cout<<endl;
for (int i=0;i<ROWCOUNT;i++)
  {  for(int j=0;j<COLCOUNT;j++)
    {
    printf("%02d ",gamemap[i][j]);
    }
printf("\n");
  }
}

void drawmap()                        //显示模块2
{
for (int i=0;i<ROWCOUNT;i++)
  {  for(int j=0;j<COLCOUNT;j++)
    {

        putimage(j*BKWIDTH,i*BKHEIGHT,BKWIDTH,BKHEIGHT,&qw,0,gamemap[i][j]*(BKHEIGHT));
        putimage(j*BKWIDTH,i*BKHEIGHT,BKWIDTH,BKHEIGHT,&backcard,0,0);//贴卡牌背面
    
    
    
     }

  }
}

void TIME()
{
    //printf("开始计时\n");
   time_t start_t, end_t;
   double diff_t;
              time(&start_t);
            Sleep(1000);
             time(&end_t);
            diff_t = difftime(end_t, start_t);
           // printf("执行时间 = %f\n", diff_t);
}
DWORD WINAPI Threadl(LPVOID IpParameter)
{
    HANDLE hOutput= GetStdHandle(STD_OUTPUT_HANDLE);
    CONSOLE_CURSOR_INFO cci;
    cci.dwSize=1;
    cci.bVisible=0;
    SetConsoleCursorInfo(hOutput,&cci);
    COORD coord={0,0};

        printf("游戏开始\n");
      time_t start_t, end_t;
       double diff_t;
       double t=0;
       while(1)
       {    time(&start_t);
            Sleep(1000);
             time(&end_t);
           t+= diff_t = difftime(end_t, start_t);
           SetConsoleCursorPosition(hOutput,coord);
            printf("游戏时间 = %f\n", t);
            //system("cls");
           if(flag3==1)
           {
               break;
           }
       }
    it=t;
    return t;
    }

void init()
{
    system("cls");
    //初始化界面
    cout<<endl<<"##欢迎来到连连看游戏##"<<endl;
    //插入选择界面
    cout<<"请输入1开始游戏"<<endl;//<<"初级（输入1）"<<endl<<"中级（输入2）"<<endl<<"高级（输入3）"<<endl;;
    int option;
    cin>>option;
    switch(option)
    {
        case(1):
            initgraph(COLCOUNT*BKWIDTH,ROWCOUNT*BKHEIGHT,SHOWCONSOLE);
            //l=9,w=9,b=10;system("cls");goto CHUSHI;
            break;
        case(2):
            initgraph(COLCOUNT*BKWIDTH,ROWCOUNT*BKHEIGHT,SHOWCONSOLE);
            //Sleep(5000);
            for (int i=0;i<ROWCOUNT;i++)
           {  
               for(int j=0;j<COLCOUNT;j++)
               {
                   a[i][j]=3;
               }
            }
           }                   
}'

****

## 图片素材

必须以原名字发在cpp同级的文件夹中

![back.bmp](![](https://s3.uuu.ovh/imgs/2022/12/01/36e7294d9056d8e4.bmp))

![qw.jpg](![](https://s3.uuu.ovh/imgs/2022/12/01/7ed544c93edd7cea.jpg))

![backcard.jpg](![](https://s3.uuu.ovh/imgs/2022/12/01/c3091ccf514ed684.jpg))



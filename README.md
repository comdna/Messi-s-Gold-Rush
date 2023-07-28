# Messi-s-Gold-Rush
让梅老板和黄金矿工联动一次,本次项目是第一次的开发,无迭代,目前的设置很简单.不喜勿喷,嫌low勿喷!

这个新的类似黄金矿工的游戏,仅仅简单设置了一下第一关,并且还是自己暴力敲上去的;
所以比较low,勿喷,本人知道自己水平很一般,和各位观众大佬没法比


下一步的迭代开发计划:
1.使用随机数来来生成游戏关卡.
2.添加上 分数 时间的计入;
3.游戏开始和结束界面制作 需要将进一步提升,交互设计要加强.
当然问题和改进思路还有很多很多,随时提就行,希望别喷的太猛,孩子内心 不是那么的强大!


现在的代码如下:
/*
使用说明:
1.需要自己配制 easyx图形库,网上下载,按照它给的步骤来就ok
2.我开发时用的是 VS2022版本,C++17标准; 试玩的小伙伴们,你们的IDE可不可以运行我还真不确定
*/

//Coder: BUPT(柏油) SCS 软件工程专业 Liu Y.F
#include<easyx.h>
#include<graphics.h>
#include<Windows.h>
#include <iostream>
#include <cstdlib>
#include <ctime>
#include<cmath>
#include<conio.h>
#include<vector>
using namespace std;
constexpr auto WIDTH = 1000;
constexpr auto HEIGHT = 600;
constexpr auto PAI = 3.1415926535;
//游戏开始的封面
void gamestart();
//游戏准备
void gamepre();
//游戏循环界面
void game();
//游戏结束界面
void finishgame();

enum situation { up,down,quiet };
enum TYPE{gold,cup};
//物品类
class Thing {
public:
	int x, y;
	int size_width, size_height;
	TYPE type;
	bool islive = true;
};

std::vector<Thing> thing;
Thing temp_thing;
//钩子类
class G {
public:
	int x0 = 500, y0 = 120;
	int x,y;
	double o=0.0;
	double vo=0.0005;
	bool l_or_r= false;
	situation s;
	int length=40;
	int speed;
	int vx, vy;
	int index=-1;

	//静止的摆动
	void swing() {
		if (!(s == quiet)) return;
		//钩子的角度
		if (s == quiet) {
			if (l_or_r == false) {
				o += vo;
				if (o > 3.14) {
					o = 3.14;
					l_or_r = !l_or_r;
				}
			}
			else {
				o -= vo;
				if (o <= 0) {
					o = 0;
					l_or_r = !l_or_r;
				};
			}
		}
		
		x = x0 + length * cos(o);
		y = y0 + length * sin(o);
	}

	//下移
	void keyevent() 
	{
		//if (!(s == quiet)) return;
		//空格键实现 放钩子
		if (GetAsyncKeyState(VK_SPACE)) 
		{
			s = down;
			vx = speed * cos(o);
			vy = speed * sin(o);
		}
		if (s == down) {
			x += vx;
			y += vy;
			Sleep(20);
		}
		else if (s == up) {
			x -= vx;
			y -= vy;
			Sleep(20);
		}
		//判断是否抓到物品 or 出界
		if (x<0 || x>WIDTH || y > HEIGHT) {
			s = up;
		}
		//判断是否上升结束
		if ( s==up&&sqrt(pow(x-x0,2)+pow(y-y0,2)) < length) {
			s = quiet;
			//所抓取的物体的 消失
			if (this->index != -1) {
				thing[this->index].islive = false;
				this->index = -1;
			}
		}
	}

	//抓取
	void judgegrap() {
		//判断是否被抓
		for (int i = 0; i < thing.size(); i++) {
			if (this->x >= thing[i].x && this->x <= thing[i].size_width + thing[i].x &&
				this->y >= thing[i].y && this->y <= thing[i].size_height + thing[i].y
				&&thing[i].islive==true)
			{
				//s = up;
				//thing[i].x = this->x;
				//thing[i].y = this->y;
				this->index = i;
				s = up;
				//thing[i].islive == false;
				break;
			}
		};
		//如果被抓 跟着一起动弹
		if (this->index != -1) {
			thing[this->index].x = this->x- thing[this->index].size_width/2;
			thing[this->index].y = this->y- thing[this->index].size_height/2;
		}
	}

};


G g;

IMAGE image[10];
int main() {
	gamestart();
	//设置钩子的初始状态 和加载图片
	gamepre();
	g.x0 = 500;
	g.y0 = 110;
	g.o = 3.14 / 2;
	g.length = 70;
	g.x = 500;
	g.y = 150 + g.length;
	g.l_or_r = false;
	g.s = quiet;
	g.speed = 5;
	g.vx = 0;
	g.vy = 0;
	g.index = -1;
	BeginBatchDraw();
	//物品的初始化
	/*1*/temp_thing.size_height = temp_thing.size_width = 80; temp_thing.type = gold; temp_thing.x = 300;  temp_thing.y = 300;//
	temp_thing.islive = true; thing.push_back(temp_thing);
	/*2*/temp_thing.size_height = temp_thing.size_width = 80; temp_thing.type = gold; temp_thing.x = 700;  temp_thing.y = 350;
	temp_thing.islive = true; thing.push_back(temp_thing);
	/*3*/temp_thing.size_height = temp_thing.size_width = 80; temp_thing.type = gold; temp_thing.x = 100;  temp_thing.y = 200;//
	temp_thing.islive = true; thing.push_back(temp_thing);
	/*4*/temp_thing.size_height = temp_thing.size_width = 80; temp_thing.type = gold; temp_thing.x = 600;  temp_thing.y = 400;
	temp_thing.islive = true; thing.push_back(temp_thing);
	/*5*/temp_thing.size_height = temp_thing.size_width = 80; temp_thing.type = gold; temp_thing.x = 500;  temp_thing.y = 450;
	temp_thing.islive = true; thing.push_back(temp_thing);
	/*6*/temp_thing.size_height = temp_thing.size_width = 80; temp_thing.type = gold; temp_thing.x = 900;  temp_thing.y = 250;
	temp_thing.islive = true; thing.push_back(temp_thing);
	/*7*/temp_thing.size_height = temp_thing.size_width = 80; temp_thing.type = gold; temp_thing.x = 200;  temp_thing.y = 500;//
	temp_thing.islive = true; thing.push_back(temp_thing);

	//测试方便
	thing.clear();

	/*1*/temp_thing.size_height = 90; temp_thing.size_width = 60; temp_thing.type = cup; temp_thing.x = 900;  temp_thing.y = 470;
	temp_thing.islive = true; thing.push_back(temp_thing);

	bool gameover = false;
	while (!gameover)
	{
		game();
		FlushBatchDraw();
		g.swing();
		g.keyevent();
		g.judgegrap();
		int i = 0;
		for (i = 0; i <= thing.size(); i++) {
			if (i == thing.size()) {
				gameover = true;
				break;
			}
			else if (thing[i].islive == true) break;
		};
	}
	EndBatchDraw();
	finishgame();
	return 0;
}
void gamepre() {
	//initgraph(WIDTH, HEIGHT);
	//背景
	loadimage(&image[0], L"./image0.jpg", WIDTH, HEIGHT);
	//老板
	loadimage(&image[3], L"./image2.bmp", 100, 100);
	loadimage(&image[4], L"./image2.jpg", 100, 100);
	//金球
	loadimage(&image[1], L"./image1.bmp", 80, 80);
	loadimage(&image[2], L"./image1.jpg", 80, 80);
	//大力神杯
	loadimage(&image[5], L"./image3.bmp", 60, 100);
	loadimage(&image[6], L"./image3.jpg", 60, 100);
}

void game() {
	//background
	putimage(0, 0, &image[0]);
	//贴图老板
	setlinecolor(WHITE);
	setfillcolor(RGB(255, 192, 203));
	fillrectangle(0,0,1000,100);
	putimage(450, 0, &image[3], SRCAND);
	putimage(450, 0, &image[4], SRCPAINT);
	//绘制钩子
	setlinecolor(BLACK);
	setlinestyle(PS_SOLID,5);
	line(g.x, g.y, g.x0, g.y0);
	
	//物品的绘制
	//1
	//物品存在 则呈现
	for (int i = 0; i < thing.size(); i++) {
		//金球奖
		if (thing[i].islive == true&&thing[i].type==gold) {
			putimage(thing[i].x, thing[i].y, &image[1], SRCAND);
			putimage(thing[i].x, thing[i].y, &image[2], SRCPAINT);
		}
		//大力神杯
		else if (thing[i].islive == true && thing[i].type == cup) {
			putimage(thing[i].x, thing[i].y, &image[5], SRCAND);
			putimage(thing[i].x, thing[i].y, &image[6], SRCPAINT);
		}
	}
}

void gamestart() {
	initgraph(WIDTH, HEIGHT);
	// 背景color
	setbkcolor(RGB(255, 182, 193)); 
	cleardevice();
	IMAGE i1;
	loadimage(&i1, L"./beg1.jpg",600,600);
	putimage(400, 0, &i1);

	// 绘制标题
	setlinecolor(BLACK);
	rectangle(50,180,330,380);
	settextstyle(50, 0, _T("Consolas"));
	settextcolor(WHITE); // 标题文字
	outtextxy(100, 200, _T("Messi's") );
	outtextxy(130, 260, _T("Gold") );
	outtextxy(130, 320, _T("Rush") );
	// 左下角注明 
	settextstyle(18, 0, _T("Consolas"));
	TCHAR madeByText[] = _T("Made by BUPT Liu YF");
	int madeByTextWidth = textwidth(madeByText);
	outtextxy(10, HEIGHT - 30, madeByText);
	//按空格开始游戏
	Sleep(2000);
	//底部添加文字
	settextcolor(WHITE);
	settextstyle(38, 0, _T("Consolas"));
	TCHAR startText[] = _T("Press Enter to start game");
	int textWidth = textwidth(startText);
	outtextxy(WIDTH / 2 - textWidth / 2,HEIGHT - 50, startText);
	bool tag = false;
	while (!tag) {
		if (GetAsyncKeyState(VK_RETURN)) tag = true;
	}
}

void finishgame() {
	//测试之后忘了删除
	//initgraph(WIDTH,HEIGHT);
	//贴图
	//setbkcolor(RGB(253, 255, 25)); 黄色
	//setbkcolor(WHITE); //白色
	setbkcolor(RGB(221, 160, 221)); //淡紫色
	cleardevice();
	IMAGE i1,i2,i3;
	loadimage(&i1, L"./fin2.jpg", 200, 200);
	loadimage(&i2, L"./fin1.jpg", 350, 200);
	putimage(340, 0, &i2);
	putimage(0, 0, &i1);
	putimage(800, 0, &i1);
	//庆祝字体
	settextcolor(WHITE);
	settextstyle(60, 0, _T("Consolas"));
	outtextxy(160, HEIGHT / 2 - 15, _T("Congratulations! You Win!"));
	Sleep(1000);
	//来自coder的肯定
	loadimage(&i3, L"./fin1.png", 150, 200);
	putimage(0,400, &i3);
	settextcolor(BLACK);
	settextstyle(60, 0, _T("仿宋"));
	outtextxy(230,500, _T("—— 来自Coder的肯定!"));

	//结束
	Sleep(1000);
	settextcolor(RED);
	settextstyle(38, 0, _T("Consolas"));
	TCHAR startText[] = _T("Press Enter to leave");
	int textWidth = textwidth(startText);
	outtextxy(230,400, startText);
	bool tag = false;
	while (!tag) {
		if (GetAsyncKeyState(VK_RETURN)) tag = true;
	}
	closegraph();
}


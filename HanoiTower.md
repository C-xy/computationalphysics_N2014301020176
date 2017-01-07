我的某同学在学c语言，他说书上有个简单的c语言算法，写的是关于还原[汉诺塔](http://baike.baidu.com/subview/191666/20428903.htm)。程序会自动计算将一个完整的汉诺塔从第一根柱子移动到第三根柱子上的最短步骤，然后打印出来。于是我想，能不能写一个还原“任意摆放”的汉诺塔的算法？于是就有了如下的代码，平台vs2015

 1. 

```cpp
//HanoiTower.h

#pragma once
#include <vector>
#include <string>
#include <array>
using std::array;
using std::vector;
using std::string;
typedef int Disk;
typedef array<vector<Disk>,3> HanoiTower;//tower[3]
typedef int Column;//column=0,1,2 (A,B,C)
typedef string error;//多余了..
void move_sequential(HanoiTower& tower,Disk n,Column to);
```
 2. 
```cpp
//HanoiTower.cpp

#include <vector>
#include <string>
#include <assert.h>
#include <iostream>
#include "HanoiTower.h"
using std::cout;
using std::endl;
Column fide_disk(HanoiTower& tower,Disk disk)
{
	for(Column i=0;i<3;++i)
	{
		for(auto each:tower[i])
		{
			if(each==disk) return i;
		}
	}
	throw error("can't find disk in tower");
}
void move_top(HanoiTower& tower,Disk disk,Column from,Column to)
{
	auto temp=tower[from][tower[from].size()-1];
	assert(temp==disk);
	tower[to].push_back(temp);
	tower[from].pop_back();
	//print
	static unsigned int step=1;
	//char fromc='A'+from,toc='A'+to;
	cout<<"step "<<step<<": \tmove disk"<<disk<<" from column"<<from+1<<" to column"<<to+1<<endl;
	++step;
}
void move_sequential(HanoiTower& tower,Disk n,Column to)
{
	Column now=fide_disk(tower,n);
	if(n==1)
	{
		if(now==to) return;
		move_top(tower,n,now,to);
		return;
	}
	if(now==to)
	{
		move_sequential(tower,n-1,to);
		return;
	}
	Column unused=0+1+2-now-to;//calculate which column is unused
	move_sequential(tower,n-1,unused);
	move_top(tower,n,now,to);
	move_sequential(tower,n-1,to);
}
```
 3. 
```cpp
//main.cpp
#include "HanoiTower.h"
#include <iostream>
#include <conio.h>
using std::cin;
using std::cout;
using std::endl;
int main()
{
	HanoiTower tower;
	cout<<"输入汉诺塔总层数：";
	int n;
	n=_getch();
	n-='0';
	int depth=n;
	cout<<n<<"\n1：默认初始排序  2：自定义初始排序";
	int temp;
	temp=_getch();
	temp-='0';
	cout<<temp<<endl;
	for(auto each:tower)
	{
		each.clear();
	}
	if(temp==1)
	{
		for(;n!=0;--n)
		{
			tower[0].push_back(n);
		}
	}
	else if(temp==2)
	{
		char index;
		cout<<"请将圆盘从大到小依次放置在柱子1,2或者3上（输入1,2或3）"<<endl;
		for(;n!=0;--n)
		{
			cout<<"将大小为"<<n<<"的圆盘放置在：";
			index=_getch();
			cout<<index<<endl;
			tower[index-'1'].push_back(n);
		}
	}
	cout<<"最终将全部移动到 column3 上"<<endl;
	system("pause");
	move_sequential(tower,depth,2);
	cout<<"finish!"<<endl;
	_getch();
}
```
[**为了兼容，我用的静态库，1M多，点此下载**](https://github.com/C-xy/computationalphysics_N2014301020176/blob/master/HanoiTower.exe?raw=true)

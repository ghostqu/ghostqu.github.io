---
layout: post
title: "我的C++学习笔记2"
date: 2016-02-04 23:10:09 +0800
comments: true
categories: C++
---

昨天讲了程序的格式化输出，今天继续上文，即程序的输入部分，首先我们还是继续来讲解一些定义。

<!--more-->
---
##1.定义和概念

**数组(array)**是一种数据格式，作用是储存多个相同类型的值。当我们要声明一个数组时要指出以下三点，数组类型，数组名和数组中的元素数。声明数组的通用格式如下`typeName arrayName[arraySize];`其中数组长度arraySize必须为整型常数或const值，也可以是常量表达式[^footnote1]，换句话说，arraySize的数值在编译之前必须是确定已知的。

**注意:**数组内元素是可以单独调用的，但是使用时需要注意一点，数组声明是arraySize是数组长度，但是数组中的元素序号是从0开始的。例如，我们声明一个长度为10的数组`int i[10];`，数组的元素是0~9而不是1～10;，切记切记。

另一个需要注意的地方是数组的初始化，当我们**定义数组**[^footnote2]时我们可以给数组以以下方式赋值`int num[4]={1,2,3,4};`；当然我们也可以只给一部分元素赋值，例如`int num[4]={1,2};`和`int num[4]={1,,3};`前一语句给第1、2号元素赋值，后一句给第1、3号元素赋值，其他没有赋值的元素默认为0；另外我们也可以把数组内所有元素初始化为0，如`int num[4]={0};`。

这里有一个有趣的情况，当我们用以上方式定义数组时，可以不输入数组长度，即方括号中为空，这时编译器会自动计算数组长度，例如`int num[]={1,2,3,4};`数组长度为4;。

另外数组之间不能互相赋值，例如：
```
int num[4] = {1,2,3,4};
int num1[4] = {0}
num = num1;  //This is an ERROR
```
这个是错误的，编译器会报错。

**字符串**本质上是一个char型数组，用于存储连续的本本文字。我们有一个非常方便的方式在**声明数组**时将字符串写入，声明方式如下`char myName[] = "Mr.Qu";`。

**注意:**在C++中，所有字符串都是以空字符结尾(即\0)，这个是用来标记字符串结尾的，当我们输入`char myName[] = "Mr.Qu";`时，编译器隐性的在结尾加入了空字符，因此这个数组的长度为6而不是5。

另有一点有趣的应用大家可以做实验尝试一下，按照以下程序会输出什么？
```
#include <iostream>

int main()
{
 using namespace std;
 char myName[6] = {'M','r','.','Q','\0','u'};
 char myName1[5] = {'M','r','.','Q','u'};
 cout << myName << endl;
 cout << myName1 << endl;
 return 0;
}
```

---

##2.程序及作业

鉴于从今天开始我只做C++部分的笔记，所以作业我用C++来做。程序要求为正确输入并显示一段有空格的字符串。
```
// 02.cpp --read one string
#include <iostream>

int main()
{
	using namespace std;
	
	const int strLength=100;
	char strRead[strLength];

	cout << "Please input the string." << endl;
    //cin >> strRead; //this is wrong
    cin.getline(strRead,strLength);
	
	cout << "The string you just entered is " << endl
			<< strRead << endl;
	
	return 0;
	}

```

程序运行结果如下：

```
Please input the string.
I Love You!
The string you just entered is 
I Love You!


------------------
(program exited with code: 0)
Press return to continue

```

---
##3.程序分析

首先，各位看官肯定发现了一个从未见过的关键字`const`，`const`叫做限定符，作用是把**一个对象转化为常量**，在本例中作用类似于`#define strLength 100`，但是`const`比`define`好用，因为它**可以指定类型，可以限定范围，可以用于指针和数组**，当然我们会在后面介绍它的其他功能。

大家可能发现了我程序中注释了一行错误代码`cin >> strRead; `，那么为什么这个代码是错误的呢？由于我们不能从键盘上输入空字符（\0），cin需要通过其他方式来判断我们是否输入完毕字符串，**C++中cin通过空格、制表符和换行符来确定字符串结尾**，那么当我们输入一串带空格的字符串时，实际上cin值读取到你第一个空格之前的内容，并将其作为字符串存入数组中[^footnote3]。

好在C++提供了很强大的类成员函数，其中包括`cin.getline()`和`cin.get()`。本次中我们用了`cin.getline()`，这个函数的作用是**读取整个行，它通过识别换行符来判断字符串是否结束**，并且这个函数是有参数的，第一个参数是用来存储输入行的数组名，第二个是要读取的字符数（n）。`cin.getline()`在读取到换行符或者是n-1个字符数时停止读取，至于为什么是n-1？是因为最后一个位置要留给\0。

**注意:**`cin.getline()`利用换行符来判断字符串是否结束，在读取到换行符时，`cin.getline()`将换行符替换为空字符并储存在数组末尾。

在介绍下一个函数`cin.get()`之前，我要提出一个问题，当我们用`cin`来读取字符串，当输入了空格时`cin`就结束存储了，那么剩下的字符到哪里了？我们用程序来说话。

```
// 02(1).cpp --read one string
#include <iostream>
#define strLength 100
int main()
{
	using namespace std;
	
	//const int strLength=100;
	char strRead[strLength];
	char strRead1[strLength];

	cout << "Please input the string." << endl;
    cin >> strRead;
    cin.getline(strRead1,strLength);
	
	cout << "The string you just entered is " << endl
			<< strRead << endl
			<< strRead1 << endl;
	
	return 0;
	}

```
程序运行结果为：

```
Please input the string.
I Love You!
The string you just entered is 
I
 Love You!


------------------
(program exited with code: 0)
Press return to continue
```

各位童鞋可以明显看出，在空格之后输入的部分并没有消失，它们可以被第二个函数识别并存储。这是因为我们**输入的字符先被存储在系统的缓冲区**，缓冲区可以理解成一个队列，程序实际读取的是这个队列中的内容。

好了，在理解上面的知识之后，我们继续介绍`cin.get()`，实际上`cin.get()`跟`cin.getline()`非常像，只不过`cin.get()`在读取整行字符之后，不丢弃换行符，而是把它留在缓冲区之中，这意味着如果你要输入两次，第二次调用时`cin.get()`将无法识别第二次的输入，因为上一次调用后未被丢弃的换行符依旧留在缓冲区中。好在我们有办法解决这个问题，首先，当`cin.get()`不输入任何参数时[^footnote4]可读取下一个字符（不管是换行符、空格还是制表符），其次，还有一个类函数叫做`cin.getchar()`作用与前面相同，我们可以通过`cin.get(strRead1,strLength).get;`或者`cin.get(strRead1,strLength); cin.get();`来调用它们。

---

以上纯属个人观点，欢迎大家指正，如果有什么地方令您不满，请尽快告知，鄙人会酌情判断进行修改。若修改后鄙人观点还是令您不悦——你咬我啊。(╯°Д°)╯︵ ┻━┻

---
**作业:**
1.讲述c语言中处理空格的方法。
2.输入以下字符串“Even now, you don't konw HOW MUCH I LOVE YOU”，按照原样显示，并计算其中单词数，字母数和大写字母数。



[^footnote1]:例如`sizeof(int)`或`12*8`。

[^footnote2]:注意是定义不是声明，不是声明，不是声明。忘记什么是定义什么是声明的，忘记的同学给你们一个[传送门](http://ghostqu.github.io/blog/2016/02/02/c-plus-01/)。

[^footnote3]:作为字符串的意思是在最后一位加上\0。

[^footnote4]:这里用了函数的重定义。




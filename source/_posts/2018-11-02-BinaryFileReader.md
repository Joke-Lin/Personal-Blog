---
title: 简单的二进制阅读器
date: 2018-11-02
tags: 
- C/C++
categories: 
- Tool
---

> 在学习BMP位图的构成时，对网上的收费16进制查看器很是烦躁，notepad查看时卡到放弃人生
>
> 因为只是为了初步学习图片知识，以及查看2进制文件内部构成的话，可以自己实现一个

## 思路

- 使用C++的文件操作进行二进制的读操作，这里默认以1个字节为单位（2位16进制）

- 每次读一个字节，就将其转化位16进制，读取的时候需要注意有符号数和无符号数的区别，我这里用**unsigned Char** 来存取每一个字节的内容

- 对于C++的文件读写网上很多介绍

- 对于每一个读取的数写入文本文件内（.txt文件）便于查看

- 计算文件大小 通过C++的文件指针移动即首尾文件指针差除以1024即文件大小（KB）

<!-- more -->

## 代码如下

```c++
#include<iostream>
#include<fstream>
#include<string>
#include<cstdlib>

using namespace std;

char HEX[16] = { '0','1','2','3','4','5','6','7','8','9','A','B','C','D','E','F' };

void transfrom(int num, char* hexNumber)
{
	for (int i = 0; i < 8; i++)
	{
		hexNumber[i] = '0';
	}
	int index = 7;
	while (num != 0 && index >= 0)
	{
		hexNumber[index--] = HEX[num % 16];
		num = num / 16;
	}
}

string getFileName(string& filename)
{
	int index = -1;
	for (int i = filename.length() - 1; i >= 0; i--)
	{
		if (filename[i] == '\\')
		{
			index = i;
			break;
		}
	}
	return filename.substr(index + 1, filename.length());
}

int main()
{
	int num = 0;    //The Byte have been read
	string path_r;
	string path_w;
	cout << "Please input the File for read and write's name: " << endl;

	ifstream in;
	ofstream out;

	while (true)
	{
		cout << "The file path to read: ";
		getline(cin, path_r);
		in = ifstream(path_r, ios::binary);
		if (!in.is_open())
		{
			cout << "Error: File Path is Wrong" << endl;
		}
		else break;
	}
	// Get the file path to Write
	cout << "The File Path to save(.txt): ";
	getline(cin, path_w);
	out = ofstream(path_w);

	//Get the File size
	long long Beg = in.tellg();
	in.seekg(0, ios::end);
	long long End = in.tellg();
	long long fileSize = End - Beg;
	in.seekg(0, ios::beg);
	out << "\t\t" << getFileName(path_r) << "\tFile Size: " << fileSize / 1024.0 << 	"KB" << endl << endl;

	//The index of every row
	char hexNumber[9] = "00000000";

	//Print the first row's index
	unsigned char temp;
	out << "\t\t"; //Format index
	for (int i = 0; i < 16; i++) out << HEX[i] << "  ";
	out << endl;



	//Read and Write File
	while (in.read((char*)&temp, 1))
	{
		if (num % 16 == 0)
		{
			out << endl;
			transfrom(num, hexNumber);
			out << hexNumber << ":\t";
		}
		num++;
		int hex = (unsigned)temp;
		char a = HEX[hex / 16];
		char b = HEX[hex % 16];
		out << a << b << " ";
	}

	out.seekp(0,ios::beg);
	
	// Close file
	in.close();
	out.close();

	cout << "Read Successfully" << endl;
	system("pause");
	return 0;
}
```

## 运行说明

- 不能输入输出文件名一致（可以自己试试ㄟ( ▔, ▔ )ㄏ）
- 首先输入需要读的文件（可以直接将文件拖入黑框 系统会自动输入绝对路径）
- 然后输入用于保存结果的 TXT 文件（如果不存在则在当前目录新建）

## 运行截图

> 以读取Bmp图像为例

![](/assets/ArticleImg/2018/BinaryInput.jpg)

![](/assets/ArticleImg/2018/BinaryOutput.jpg)
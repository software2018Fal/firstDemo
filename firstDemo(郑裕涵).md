# 实验一
**实验步骤**


 1. 安装VC2010 express，新建工程firstDemo，添加main.cpp.
 2. 拷贝gdal和lib文件夹至项目目录。根据教程添加头部引用和代码。项目代码如下：

<pre>#include <iostream>
<pre>using namespace std;
<pre>#include "gdal/gdal_priv.h"
<pre>#pragma comment(lib, "gdal_i.lib")

int main()
{
	//输入图像
	GDALDataset* poSrcDS;
	//输出图像
	GDALDataset* poDstDS;
	//图像的宽度和高度
	int imgXlen, imgYlen;
	//输入图像路径
	char* srcPath = "tree.jpg";
	//输出图像路径
	char* dstPath = "res.tif";
	//图像内存存储
	GByte* buffTmp;
	//图像波段数
	int i, bandNum;
	
	//注册驱动
	GDALAllRegister();

	//打开图像
	poSrcDS = (GDALDataset*)GDALOpenShared(srcPath, GA_ReadOnly);

	//获取图像宽度高度和波段数量
	imgXlen = poSrcDS->GetRasterXSize();
	imgYlen = poSrcDS->GetRasterYSize();
	bandNum = poSrcDS->GetRasterCount();
	//输出获取的结果
	cout << " Image X Length: " << imgXlen << endl;
	cout << " Image Y Length: " << imgYlen << endl;
	cout << " Band number:    " << bandNum << endl;
	//根据图像的宽度和高度分配内存
	buffTmp = (GByte*)CPLMalloc(imgXlen*imgYlen*sizeof(GByte));
	//创建输出图像
	poDstDS = GetGDALDriverManager()->GetDriverByName("GTiff")->Create(dstPath, imgXlen, imgYlen, bandNum, GDT_Byte, NULL);
	//一个个波段的输入，然后一个个波段的输出
	for(i = 0; i < bandNum; i++)
	{
		poSrcDS->GetRasterBand(i+1)->RasterIO(GF_Read, 0, 0, imgXlen, imgYlen, buffTmp, imgXlen, imgYlen, GDT_Byte, 0, 0);
		poSrcDS->GetRasterBand(i+1)->RasterIO(GF_Write, 0, 0, imgXlen, imgYlen, buffTmp, imgXlen, imgYlen, GDT_Byte, 0, 0);
		printf("... ... band %d processing ... ...\n",i);
	}

	//清除内存
	CPLFree(buffTmp);
	//关闭dataset
	GDALClose(poDstDS);
	GDALClose(poSrcDS);

	system("PAUSE");
	return 0;

}

 3. 将gdal18.dll拷贝到项目的release目录下，并添加图片trees.jpg.
 4. 运行程序.

---

**问题**
编译后出现
1>d:\work\firstdemo\main.cpp(6): fatal error C1010: 在查找预编译头时遇到意外的文件结尾。是否忘记了向源中添加“#include "StdAfx.h"”?

查询之后，在项目属性页中将预编译头选项修改为不使用预编译头就解决了。





# Windows 10, Visual Studio 2015环境下OpenCV 3.1配置和入门


OpenCV是计算机视觉的事实标准库，学习OpenCV非常重要，在目前的国情下，在Windows上完成OpenCV的开发和教学工作是非常重要的，还不能指望所有人都有Mac计算机，即使Linux也是不现实的。

我们来详细探讨如何在Windows 10，Visual Studio 2015的环境下来配置OpenCV的学习环境。

### 下载OpenCV

没啥说的，[OpenCV官网](http://opencv.org/)上下载，我们要下载最新的3.1版本，所以要点击`DOWNLOADS`链接，不要下载主页上面的2.4.13版本。在下载页面中下载`OpenCV for Windows 3.1`即可。

![](/2016/10/opencv-windows-md/01.PNG)
下载下来的是一个`opencv-3.1.0.exe`文件，其实就是一个自解压的压缩包，可以直接执行也可以`7-zip`解压，随你便。

最重要的两个目录是`build\include`和`build\x64\vc14\lib`目录，这是Visual Studio 2015 C++中要配置的两个重要目录。

### 如何配置

在`Visual Studio 2015`中新建一个`Visual C++`的`Empty Project`，项目名字和位置自己定义好。

![](/2016/10/opencv-windows-md/02.PNG)
在`project`上右击选择`Properties`，在`VC++ Directories`中配置`Include Directories`和`Library Directories`。

![](/2016/10/opencv-windows-md/03.PNG)
要注意属性的配置文件，非常重要。我们配置`Debug`和`x64`。是的，OpenCV 3.1已经不支持x86的开发了。

配置完路径后，要在`Linker`段中的`Input`中的`Additional Dependencies`中将OpenCV的`opencv_world310d.lib`引入。

关闭配置框后一个重要的事情是在顶部工具条中要修改当前的配置。

![](/2016/10/opencv-windows-md/04.PNG)
新建一个C++文件。我们来完成第一个tutorial，[Load and Dispaly an Image](http://docs.opencv.org/2.4/doc/tutorials/introduction/display_image/display_image.html#display-image)。

为了学习方便，我们在一个项目中通过菜单和函数来完成不同教程。

```c++
#include <iostream>
#include <string>

//引入OpenCV头文件
#include <opencv2\core.hpp>
#include <opencv2\imgcodecs.hpp>
#include <opencv2\highgui.hpp>

using namespace std;
using namespace cv;

const string imageName("D:/zjl.jpg");

void displayMenu(); //选择不同的Tutorial

void loadAndDisplayAnImage(string windowTitle); //第一个教程

int main()
{
	displayMenu();

	return 0;
}

void displayMenu()
{
	char key = ' ';//读取菜单选项

	while (key != 'x')
	{
		cout << "Please select what tutorial do you want to run:" << endl;
		cout << "[1]Load and Dispaly an Image" << endl;
		cout << "[x]Quit" << endl;

    cin >> key;

		switch (key) {
		case '1':
			loadAndDisplayAnImage("Load and Display an Image");
			break;
		case 'x':
			break;
		}
	}
}

void loadAndDisplayAnImage(string windowTitle)
{
	Mat inputImage;
	inputImage = imread(imageName, CV_LOAD_IMAGE_COLOR);
	if (inputImage.empty())
	{
		cout << "The image has problem or file not fount" << endl;
		return;
	}

	namedWindow(windowTitle, CV_WINDOW_AUTOSIZE);
	imshow(windowTitle, inputImage);

	waitKey();

	destroyWindow(windowTitle);
}
```



编译运行，会收到错误。

![](/2016/10/opencv-windows-md/05.PNG)
这是因为找不到动态链接库。你有两种方法，第一种是将`opencv\build\x64\bin\opencv_world310d.dll`复制到`c:\windows\system`目录下，第二种方法是将`opencv\build\x64\bin\`目录添加到`path`环境变量中。

或者我们可以把`opencv_world310d.dll`直接复制到我们生成的`.exe`目录中。

OK，大功告成！


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2016/10/opencv-windows-md/  


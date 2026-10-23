# macOS Sierra, Xcode 8 环境下OpenCV 3.1配置


我们来详细探讨如何在macOS Sierra, Xcode 8 环境下来配置OpenCV的学习环境。

### 安装OpenCV

在macOS下，最方便的安装是使用`brew`来进行安装。

```plain
brew update
brew install homebrew/science/opencv3 --HEAD
```




> 上面非常重要的是一定要加上`--HEAD`选项，确保在macOS Sierra下能够正确编译，否则，你会收到`QT`的错误。OpenCV的下个发行版会改正这个Bug，目前3.1版本我们要加上参数。

OpenCV3 会安装在 `/usr/local/opt/opencv3`的目录下。

### 配置Xcode进行开发

在`Xcode 8`中新建一个`macOS`的`Command Line Tool`项目。

![](/2016/10/opencv-macos/01.png)
项目名字和位置自己定义好，确保语言选择的是`C++`。

![](/2016/10/opencv-macos/02.png)
在项目的`Build Settings`中，找到`Search Paths`段，然后设置`Header Search Paths`和`Library Search Paths`，填入OpenCV3的`include`和`lib`目录，使用brew安装的话，分别是`/usr/local/opt/opencv3/include`和`/usr/local/opt/opencv3/lib`。

![](/2016/10/opencv-macos/03.png)
在`Linking`段中设置`Other Linker Flags`

> 对于链接的设定，可以依据你要使用的OpenCV功能来进行链接设定。比如，我只是用`core`的功能，那么就设定为`-lopencv_core`即可，如果还要使用`imgcodecs`的功能，那就设定`-lopencv_core -lopencv_imgcodecs`，如果图方便，就把所有的都设定上。

![](/2016/10/opencv-macos/04.png)
修改`mian.cpp`

```c++
//
//  main.cpp
//  OpenCV-Demo
//
//  Created by 张景林 on 28/10/2016.
//  Copyright © 2016 张景林. All rights reserved.
//

#include <iostream>
#include <opencv2/core.hpp>

using namespace std;

int main(int argc, const char * argv[]) {
    // insert code here...
    std::cout << "OpenCV Version " << CV_VERSION << endl;
    return 0;
}
```



OK，大功告成！


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2016/10/opencv-macos/  


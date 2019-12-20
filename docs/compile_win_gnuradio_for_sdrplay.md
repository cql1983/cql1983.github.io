1. 首先下载sdrplay官网的API
> ，https://www.sdrplay.com/software/SDRplay_RSP_API-Windows-2.13.1.exe  
这个版本是注明了 For GNU Radio windows users and for developers)  
2. 安装后拷贝安装目录下inc文件夹的mir_sdr.h到C:\gr-build\src-stage3\staged_install\Release\include目录，拷贝 x64目录下mir_sdr_api.lib 到C:\gr-build\src-stage3\staged_install\lib\目录
3. 修改C:\gr-build\src-stage3\oot_code\gr-osmosdr\cmake\Modules 下FindLibSDRplay.cmake 文件

```
if(NOT LIBSDRPLAY_FOUND)
  pkg_check_modules (LIBSDRPLAY_PKG libsdrplay)
  find_path(LIBSDRPLAY_INCLUDE_DIRS NAMES mir_sdr.h
    PATHS
    ${LIBSDRPLAY_PKG_INCLUDE_DIRS}
    /usr/include
    /usr/local/include
  )
```
第三行的头文件文件名，原来代码的头文件是linux下的，文件名和我们拷贝过来的不一致，需要修改为实际的文件名。

4.同样，需要修改第10行开头的库文件名为拷贝过来的文件名，这里好像不用后缀。

```
find_library(LIBSDRPLAY_LIBRARIES NAMES mir_sdr_api
    PATHS
    ${LIBSDRPLAY_PKG_LIBRARY_DIRS}
    /usr/lib
    /usr/local/lib
  )
```
5. 然后再开始编译，就可以再日志里看到SDRPLAY已经被启用了。

6. 另外，我在Setp8步的编译OOT模块的PS文件中gr-osmosdr的CMAKE参数里添加了 
 ```
 -DENABLE_NONFREE=TRUE 
```
不知道默认是不是开启了，显式加上没错。
7. 还要修改C:\gr-build\src-stage3\oot_code\gr-osmosdr\lib\sdrplay\sdrplay_source_c.cc   /*#include <mirsdrapi-rsp.h> 为#include <mir_sdr.h>  

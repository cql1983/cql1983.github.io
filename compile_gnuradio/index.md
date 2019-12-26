

## 2019.12.26 手工搞定缺失zeromq的问题

默认编译找不到zeromq,直接手工修改源码
C:\gr-build\src-stage3\src\gnuradio\cmake\Modules\FindZeroMQ.cmake 文件
改成如下 ，主要是CMAKE那些变量理不清，这个编译又是一个大工程，直接硬改路径得了。不知道为什么官网的不出这个问题。也没人报bug,我搞的就不行。

```
INCLUDE(FindPkgConfig)
PKG_CHECK_MODULES(PC_ZEROMQ "libzmq")

FIND_PATH(ZEROMQ_INCLUDE_DIRS
    NAMES zmq.hpp
    HINTS ${PC_ZEROMQ_INCLUDE_DIR}
    ${CMAKE_INSTALL_PREFIX}/include
    PATHS
    C:\gr-build\build\Release\include
    /usr/local/include
    /usr/include
)

FIND_LIBRARY(ZEROMQ_LIBRARIES
    NAMES zmq libzmq libzmq-v140-mt-4_3_1
    HINTS ${PC_ZEROMQ_LIBDIR}
    ${CMAKE_INSTALL_PREFIX}/lib
    ${CMAKE_INSTALL_PREFIX}/lib64
    PATHS
    ${ZEROMQ_INCLUDE_DIRS}/../lib
    /usr/local/lib
    /usr/lib
)

INCLUDE(FindPackageHandleStandardArgs)
FIND_PACKAGE_HANDLE_STANDARD_ARGS(ZEROMQ DEFAULT_MSG ZEROMQ_LIBRARIES ZEROMQ_INCLUDE_DIRS)
MARK_AS_ADVANCED(ZEROMQ_LIBRARIES ZEROMQ_INCLUDE_DIRS)

```




## 2019.12.18 windows 下编译gnuradio的坑

1. 首先那个下载依赖的包要提取出来单独下载放进目录去，否则五六百M，几k的速度卡死你。http://www.gcndevelopment.com/gnuradio/downloads/sources/gnuradio_dependency_pack_v1.6.7z
2. UHD下载fpga固件的那个python直接杀了就是，国内就算你千兆宽带也下不动的，再说我也用不起USRP。
3. 没有安装fortran编译器，有一个不会被编译.
4. 没有安装opencle 的那个显卡关系，那个频谱可视化的也不会被编译，脚本里好像默认的是要AMD APP SDK。
5. glog 里面有一个BUILD文件，所以脚本里默认build文件夹不会被创建，导致无法编译这一一个。
6. 还有几个要从sf.net下的软件，网络原因会下载失败，手工下载放进去。
其他详细的空了来更新，我还要添加我的PlutoSDR进去呢。
2019.12.19 接着更新，PlutoSDR的支持参考 https://github.com/tfcollins/GNURadio_Windows_Build_Scripts/tree/gr-iio-support

注意，添加那个gr-iio下载的时候，分支要写 attr-block-update  ，他库里面写的是attr-block。脚本运行不正常,修改了以后要把build目录下的对应文件夹删掉，否则脚本判断已经下载就不会再下载了。



## 2019.12.16 编译gr-fosphor 

 1.  当前clone代码的时候要clone gr3.7这个分支的。默认的已经是3.8的了哦。
 2.  opencl安装intel的那个版本后，需要安装 
 ```sudo apt install ocl-icd-opencl-dev ```
 这个包，否则cmake是找不到opencl的，参考这个地址 https://github.com/fireice-uk/xmr-stak-amd/issues/97
 3. 这个包其实就是一个有Opencl加速的频谱可视化，也没什么特殊作用，视觉效果好看，不过机器配置要好，SDR带宽要大，这样就能好看了。
 4. 没安装AMD APP SDK的机器貌似运行不正常，最好还是安装一下，没做测试，我2台机器都安装过的。下载地址[http://amd-dev.wpengine.netdna-cdn.com/app-sdk/installers/APPSDKInstaller/3.0.130.135-GA/full/AMD-APP-SDKInstaller-v3.0.130.135-GA-windows-F-x64.exe](http://amd-dev.wpengine.netdna-cdn.com/app-sdk/installers/APPSDKInstaller/3.0.130.135-GA/full/AMD-APP-SDKInstaller-v3.0.130.135-GA-windows-F-x64.exe)
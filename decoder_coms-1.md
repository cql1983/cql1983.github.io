# 解码COMS-1气象卫星

大概步骤如下：
1. 安装gnuradio  用于解调RF信号到符号流。
2. 编译安装https://github.com/opensatelliteproject/xritdemod 用它的xritdecoder用于从符号流解码（解卷积，加扰等）。
3. 安装https://github.com/sam210723/COMS-1 库，从解码后的帧里面解出应用层数据，解密，出图。




## 解调
gnuradio解调用sam的软件仓库里的这个框图COMS-1-master\COMS-1-master\demod\LRIT Demod.grc  （自行修改接收设备或者IQ文件源）
![解调](https://raw.githubusercontent.com/cql1983/BI8AKT/master/docs/gnuradio_coms-1.jpg)

## 解码

在ubuntu 16.04上，按照https://github.com/opensatelliteproject/OpenSatelliteProject这个文章的方法编译安装https://github.com/opensatelliteproject/xritdemod库里的程序，主要是要xritdecoder这个软件从来解码。

下面是大概步骤，不一定准确

首先安装编译需要的软件包
```
sudo apt-get -y install libairspy-dev libusb-1.0-0-dev libhackrf-dev libhackrf0 libaec0 libaec-dev mono-complete monodevelop nuget libopenal-dev referenceassemblies-pcl ttf-mscorefonts-installer gtk-sharp3 build-essential git
```
克隆并编译
```
git clone https://github.com/opensatelliteproject/xritdemod.git

cd xritdemod

make libcorrect

sudo make libcorrect-install

make libSatHelper

sudo make libSatHelper-install 

make librtlsdr

sudo make librtlsdr-install 

#前面几部是编译需要的库和包，这一步才是编译xritdemod xritdecoder
make
make test
```
我第二次编译的时候出现xritdemod编译不过，不过没关系，只要有xritdecoder就好。编译完成后，可以直接运行xritdecoder，他会等待第一步解调送过来数据以及等待解多路复用（demux）连接。

## demux 解多路复用

decoder出来的数据需要进一步解多路复用，提取出lrit数据来。


```
actionchen@ubuntu:~/COMS-1$ git clone https://github.com/sam210723/COMS-1.git
```

gnuradio过来的数据需要通过udp->tcp转换，才能连接到decoder上（反正我试过windows直接在gnuradio里用tcp silk会死掉无法解调）
```
actionchen@ubuntu:~/COMS-1$ python udp-bridge/udp-bridge.py 4999 5000
```
如果gnuradio不在本机，需要修改udp-bridge/udp-bridge.py里面的第一个127.0.0.1为本机IP。

然后运行demux来从decoder出来的数据提取lrit数据
先修改xrit-rx.ini，让他从osp里接收数据

```
actionchen@ubuntu:~/COMS-1$ cat demux/xrit-rx.ini 
[rx]
mode = lrit
input = osp
output = ingest

[goesrecv]
ip = 127.0.0.1
vchan = 5004

[osp]
ip = 127.0.0.1
vchan = 5001
```



先要在主目录创建这个lrit文件夹，用来存放提取出来的lrit文件
```
actionchen@ubuntu:~/COMS-1$ mkdir ~/lrit
actionchen@ubuntu:~/COMS-1$ python demux/xrit-rx.py ~/lrit
```

这时候，点击gnuradio的框图运行吧。其余的几个窗口都应该能看到有数据。demux窗口也能看到有文件写入。

## 解密 


```
 python decrypt/xrit-decrypt.py ~/EncryptionKeyMessage_001F2904C905.bin.dec ~/lrit/
```
这个EncryptionKeyMessage_001F2904C905.bin.dec文件是从COMS-1官网的示例文件里EncryptionKeyMessage_001F2904C905.bin通过解密生成的。这个需要的自己下或者看sam的网站学习或者私下交流吧。

## 出图


```
actionchen@ubuntu:~/COMS-1$ python ./lrit-img.py -s ~/lrit
```
这样就会在~/lrit/目录下看到气象云图了。







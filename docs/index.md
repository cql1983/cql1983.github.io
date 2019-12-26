# BI8AKT的个人记录站点

这个页面会记录我在收听无线电信号中一些有趣的信号，请等待更新，我会慢慢记录下来。

## 2019.12.18 windows 下编译gnuradio的坑

1. 首先那个下载依赖的包要提取出来单独下载放进目录去，否则五六百M，几k的速度卡死你。http://www.gcndevelopment.com/gnuradio/downloads/sources/gnuradio_dependency_pack_v1.6.7z
2. UHD下载fpga固件的那个python直接杀了就是，国内就算你千兆宽带也下不动的，再说我也用不起USRP。
3. 没有安装fortran编译器，有一个不会被编译.
4. 没有安装opencle 的那个显卡关系，那个频谱可视化的也不会被编译，脚本里好像默认的是要AMD APP SDK。
5. glog 里面有一个BUILD文件，所以脚本里默认build文件夹不会被创建，导致无法编译这一一个。
6. 还有几个要从sf.net下的软件，网络原因会下载失败，手工下载放进去。
其他详细的空了来更新，我还要添加我的PlutoSDR进去呢。

## 2019.12.19 接着更新，PlutoSDR的支持参考 https://github.com/tfcollins/GNURadio_Windows_Build_Scripts/tree/gr-iio-support

注意，添加那个gr-iio下载的时候，分支要写 attr-block-update  ，他库里面写的是attr-block。脚本运行不正常,修改了以后要把build目录下的对应文件夹删掉，否则脚本判断已经下载就不会再下载了。



## 2019.12.16 编译gr-fosphor 

 1.  当前clone代码的时候要clone gr3.7这个分支的。默认的已经是3.8的了哦。
 2.  opencl安装intel的那个版本后，需要安装 
 ```sudo apt install ocl-icd-opencl-dev ```
 这个包，否则cmake是找不到opencl的，参考这个地址 https://github.com/fireice-uk/xmr-stak-amd/issues/97
 3. 这个包其实就是一个有Opencl加速的频谱可视化，也没什么特殊作用，视觉效果好看，不过机器配置要好，SDR带宽要大，这样就能好看了。


## 2018.01.11  业余无线电视 OSD台标显示

1. 更新字库
FPV使用的mini OSD 出厂已经带了字库，所以需要重新刷回默认字库，好在[这里](http://www.mylifesucks.de/tools/max7456/)有在线可以转换和查看。
参考[这个地址]( https://www.maximintegrated.com/cn/app-notes/index.mvp/id/4117 ) 学习max7456字库的基础知识，使用 MAX7456汉字取模软件 对汉字生成二进制数据，替换默认的DEFAULTCM.MCM的字库文件对应的位置，（可以先去http://www.mylifesucks.de/tools/max7456/在线查看替换的字库是否正确）然后拷贝替换后DEFAULTCM.MCM的内容到scarab-osd （mini OSD 开源项目自带的）Font code generator.xlsm,生成fontD.h，然后scarab-osd 项目里取消注释#define LOADFONT_DEFAULT  ，编译给miniOSD刷机，他会自动更新字库。
2. 刷机使用https://github.com/Lecostarius/overlay 这个项目修改的程序，定义好需要显示的文字位置，在程序里直接显示即可。

```

//#include <EEPROM.h> //Needed to access eeprom read/write functions

#define DATAOUT 11//MOSI
#define DATAIN  12//MISO
#define SPICLOCK  13//sck
#define MAX7456SELECT 10//ss
#define VSYNC 2// INT0



//MAX7456 opcodes
#define DMM_reg   0x04
#define DMAH_reg  0x05l
#define DMAL_reg  0x06
#define DMDI_reg  0x07
#define VM0_reg   0x00
#define VM1_reg   0x01

//MAX7456 commands
#define CLEAR_display 0x04
#define CLEAR_display_vert 0x06
#define END_string 0xff
// with NTSC
//#define ENABLE_display 0x08
//#define ENABLE_display_vert 0x0c
//#define MAX7456_reset 0x02
//#define DISABLE_display 0x00

// with PAL
// all VM0_reg commands need bit 6 set
#define ENABLE_display 0x48
#define ENABLE_display_vert 0x4c
#define MAX7456_reset 0x42
#define DISABLE_display 0x40

#define WHITE_level_80 0x03
#define WHITE_level_90 0x02
#define WHITE_level_100 0x01
#define WHITE_level_120 0x00

// with NTSC
//#define MAX_screen_size 390
//#define MAX_screen_rows 13

// with PAL
#define MAX_screen_size 480
#define MAX_screen_rows 16

#define EEPROM_address_hi 510
#define EEPROM_address_low 511
#define EEPROM_sig_hi 'e'
#define EEPROM_sig_low 's'

volatile byte screen_buffer[MAX_screen_size]={0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0xf8,0xf9,0xfa,0xfb,0xfc,0xfd,0x44,0x0c,0x13,0x08,0x0b,0x15,0x1e,0x00,0xfe,0x44,0x01,0x02,0x08,0x0a,0x17,0x12,0x3e};

volatile byte writeOK;
volatile byte valid_string;
volatile byte save_screen;
volatile int  incomingByte;
volatile int  count= 56 ;

void setup()
{
 byte spi_junk, eeprom_junk;
 int x;
 Serial.begin(9600);
 Serial.flush();

 pinMode(MAX7456SELECT,OUTPUT);
 digitalWrite(MAX7456SELECT,HIGH); //disable device

 pinMode(DATAOUT, OUTPUT);
 pinMode(DATAIN, INPUT);
 pinMode(SPICLOCK,OUTPUT);
 pinMode(VSYNC, INPUT);

 // SPCR = 01010000
 //interrupt disabled,spi enabled,msb 1st,master,clk low when idle,
 //sample on leading edge of clk,system clock/4 rate (4 meg)
 SPCR = (1<<SPE)|(1<<MSTR);
 spi_junk=SPSR;
 spi_junk=SPDR;
 delay(250);

 // force soft reset on Max7456
 digitalWrite(MAX7456SELECT,LOW);
 spi_transfer(VM0_reg);
 spi_transfer(MAX7456_reset);
 digitalWrite(MAX7456SELECT,HIGH);
 delay(500);

 // set all rows to same charactor white level, 90%
 digitalWrite(MAX7456SELECT,LOW);
 for (x = 0; x < MAX_screen_rows; x++)
 {
   spi_transfer(x + 0x10);
   spi_transfer(WHITE_level_90);
 }

 // make sure the Max7456 is enabled
 spi_transfer(VM0_reg);
 spi_transfer(ENABLE_display);
 digitalWrite(MAX7456SELECT,HIGH);


 writeOK = false;
 valid_string = false;
 save_screen = false;
 incomingByte = 0;

write_new_screen();

 
}

//////////////////////////////////////////////////////////////
void loop()
{
;
}

//////////////////////////////////////////////////////////////
byte spi_transfer(volatile byte data)
{
 SPDR = data;                    // Start the transmission
 while (!(SPSR & (1<<SPIF)))     // Wait the end of the transmission
 {
 };
 return SPDR;                    // return the received byte
}



//////////////////////////////////////////////////////////////
void write_new_screen()
{
 int x, local_count;
 byte char_address_hi, char_address_lo;
 byte screen_char;
 
 local_count = count;
 
 char_address_hi = 0;
 char_address_lo = 0;
//Serial.println("write_new_screen");  

 // clear the screen
 digitalWrite(MAX7456SELECT,LOW);
 spi_transfer(DMM_reg);
 spi_transfer(CLEAR_display);
 digitalWrite(MAX7456SELECT,HIGH);

 // disable display
 digitalWrite(MAX7456SELECT,LOW);
 spi_transfer(VM0_reg);
 spi_transfer(DISABLE_display);

 spi_transfer(DMM_reg); //dmm
 //spi_transfer(0x21); //16 bit trans background
 spi_transfer(0x01); //16 bit trans w/o background

 spi_transfer(DMAH_reg); // set start address high
 spi_transfer(char_address_hi);

 spi_transfer(DMAL_reg); // set start address low
 spi_transfer(char_address_lo);

 x = 0;
 local_count =56;
 while(local_count) // write out full screen
 {
   screen_char = screen_buffer[x];
   spi_transfer(DMDI_reg);
   spi_transfer(screen_char);
   x++;
   local_count--;
 }

 spi_transfer(DMDI_reg);
 spi_transfer(END_string);

 spi_transfer(VM0_reg); // turn on screen next vertical
 spi_transfer(ENABLE_display_vert);
 digitalWrite(MAX7456SELECT,HIGH);
}



```







## 2017.12.14  NOAA 气象卫星的接收

玩电视棒的人应该都尝试过接收这个气象云图，目前信号最好，也是最容易收的就是美国NOAA系列的几颗星了。用SDR，带宽开50K，FM模式就可以用软件解调音频信号为图像了。

![image](https://raw.githubusercontent.com/cql1983/BI8AKT/master/docs/12110746_noaa19.jpg)

![image](https://raw.githubusercontent.com/cql1983/BI8AKT/master/docs/12110746_noaa19_1.jpg)

这两张是一次收下来的图，不同通道加处理后的图。效果更好的有苏联的LRPT格式（137.9M）以及HRPT(1.7G),但是靠拉杆天线是不行了，得上四螺旋或者八木天线了。

## 2017.12.07 SSTV接收

SSTV 来啦，而且还是ISS发射的哦，先睹为快，细节空了再写。

这次也是刚好赶巧了。
![image](https://raw.githubusercontent.com/cql1983/BI8AKT/master/docs/ISS_SSTV_2017_12_07_CST.bmp)



## 2017.12.03 最近更新的是关于 短波气象传真 的一些信息。

在接收了NOAA系列气象卫星的信号后好久的时间，偶然在QQ群里得知，还有短波气象传真这个东东，于是网络上搜集，终于找到日本的短波气象传真相关信息。

软件：http://www2.plala.or.jp/hikokibiyori/soft/kgfax/index.html  日文的，不过中国地区目前能收到的信号也只有日本的那几个频率，用他们的软件不错，那个德国的软件hf-fax.de，要注册收费，据说还不好用。另外还有个seatty的软件好像也支持接收。

接收频率\(摘自kg fax 的说明文件)：

目前我有效接收到的就只有这三个频率，其余的频率包括台湾的都没有收到过信号。

```
JMH(日本気象FAX:回転速度120rpm,同期信号長20msec)

- 3.6206MHz USB
- 7.7931MHz USB
- 13.9866MHz USB
```
另外，美国气象部门提供的[**全球海洋气象广播**](http://www.nws.noaa.gov/om/marine/rfax.pdf)频率表下载。

**接收方法**：将短波机调整到列表中的频率，选择USB模式，将喇叭或者耳机口用3.5mm公对公线连接到电脑的麦克风输入口。打开kg-fax 就能收到了。kg fax 软件的使用方法baidu有很多。

今天到塘西河公园接收，使用设备为黑鼠2017版，自己改装的3-4芯 3.5mm音频线，从黑鼠耳机口输出到电脑麦克风输入，接收过程一直有杂音，不知道是设备还是干扰的原因，导致接收图像还没有深圳那个websdr效果好。下面是我今天接收的图像。
![image](https://github.com/cql1983/BI8AKT/blob/master/docs/tangxihe.png?raw=true)


## 2017.11.30 更新了接收机场航空情报通播的信息。

### 中国无线电频率划分表

频    率 | 说明
--- | ---
108—117.975（MHz） |  航空无线电导航  注：5.197A  附加划分：108-117.975 MHz频段亦划分给作为主要业务的航空移动（R）业务，仅限于根据公认的国际航空标准运行的系统。此类使用须遵守第413号决议（WRC-07，修订版）的规定。航空移动（R）业务对108-112 MHz频段的使用须仅限于根据公认的国际航空标准，为支持空中导航功能提供导航信息的由陆基发射机和相关接收机组成的系统。（WRC-07）
117.975—137(MHz) | 航空移动 5.111  5.200 5.200  在117.975-136 MHz频段，121.5MHz频率为航空应急频率，如属需要，123.1 MHz频率亦可作为121.5MHz频率的辅助航空频率。水上移动业务移动电台可按照第31条中规定的条件使用这些频率与航空移动业务电台通信，用于遇险和安全。（WRC-07）

   
   这次出差来到合肥，那么就用手持接收机搜一下机场频率玩玩，市区离机场几十公里，站在楼顶，很快就搜到了合肥新桥机场的机场通播频率128.850MHz,（注意民用航空频率使用的是AM模式调制），顺便我还上传了个[**录音**](http://player.youku.com/embed/XMzE5ODE4MzIzMg==)到优酷上面。
   在线录音如下：

<audio controls="controls" height="100" width="300">
  <source src="https://github.com/cql1983/BI8AKT/raw/master/docs/%E5%90%88%E8%82%A5%E6%9C%BA%E5%9C%BA%E9%80%9A%E6%92%AD.MP3" type="audio/mp3" />
<embed height="100" width="300" src="https://github.com/cql1983/BI8AKT/raw/master/docs/%E5%90%88%E8%82%A5%E6%9C%BA%E5%9C%BA%E9%80%9A%E6%92%AD.MP3" />
</audio>

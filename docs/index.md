# BI8AKT的个人记录站点

这个页面会记录我在收听无线电信号中一些有趣的信号，请等待更新，我会慢慢记录下来。


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
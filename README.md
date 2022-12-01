@[TOC](文章目录)

---
# 智能车入门到进门

 ## 前言
 本文默认您已熟练掌握C语言基本语法，在语法与用法上不做讲解
 本文默认您已经对STM32/51单片机有所了解。

 一些闲话：
 我所在的赛区是西部赛区，2022疫情特别严重，我们学校也是在七月初就全部让离校，尽管林老师在尽力争取留校机会，但疫情严重赛点最后还是被迫取消，如果要继续参赛就必须前往其他赛点，我们实验室经过协商选择一部分前往成都比赛另一部分前往东北参加比赛，在去成都的路上我们联系到了电子科技大学的王老师，决定前往电子科技大学参加比赛，但是到了成都，疫情也跟到了成都，我们无法继续调车，只能在酒店天天盼望着进入赛点调车，终于那一天到来了，但电科所在的区域出现疫情，我们无法进入学校，在王老师的协助下我们联系到了成都工业学院的宋老师，在成都工业学院进行比赛，比赛前一天我和我的队友奋战一天，终于完赛，但比赛时在第二圈出现问题始终无法进入环岛，单片机莫名复位，成绩大幅度退步，无缘国赛，但这段经历我会终身难忘。

 
# 第一章 做一辆巡黑线的智能车
## 硬件材料需求
小车底盘
L298N电机驱动
红外模块
STC89C52最小系统
航模电池
降压电池

## 基础知识讲解
### 1.硬件电路连接
#### 1.1 L298N与单片机连接 
![在这里插入图片描述](https://img-blog.csdnimg.cn/33dcdf9371624279977a3bac628d93b5.png#pic_center)

 如上图所示，两个电机的正负极分别接两个L298N的蓝色电机接口，至于到底哪个接正极，哪个接负极，根据你电机安装的方式而定，建议先把电机的两根线焊上，然后把底盘安装起来，这样电机的安装方式就确定了，先随便把两个L298N的4个绿色电机接口跟电机相接，等到把其他信号线接好后，再判断对错并调节，调节方法如下：在程序中让小车往前跑，观察车轮的转向，往前转的车轮的线不用变，把往后转的电机对应的L298N绿色接口的两根线换一下就行了。
   
   剩下的就是L298N的信号线与单片机的连接了，介绍如上图所示，在这里我采用的是双驱的接法，也就是左边两个点击用同一个信号控制，右边两个电机用同一个信号控制，单片机的I/O口自行选择，与程序配合起来就行，我选用的是 ENA接P10 ENB 接P11 IN1接P06 IN2接P05 IN3接P04 IN4接P03 若改为4驱所需的I/O将扩大一倍。


#### 1.2 红外循迹模块与单片机连接
![在这里插入图片描述](https://img-blog.csdnimg.cn/8ef0ebb156e34709a95520f04ea4b455.png)
**1、线路连接**
   传感器与控制板之间的连接很简单，每个传感器与控制板都有3根线相接，即一根VCC，一根GND，还有一根信号线，传感器和控制板上都有白色标识，连线很方便，稍微细心一下就行，别把VCC和GND接反了就行（接反了，一通电传感器可能就烧坏了，我烧过…），控制板与单片机之间的连接，有6根线，一根VCC，一根GND，4根信号线，接法如下：DO1— 第1路TTL电平输出，接在单片机选定的管脚上，如P10，D02—第2路TTL电 平输出，接在单片机选定的管脚上，如P11，DO3—第3路TTL电 平输出，接在单片机选定的管脚上，如P12，DO4—第4路TTL电平输出，接在单片机选定的管脚上，如P13，GND— 接单片机的GND管脚，VCC— 接单片机的5V管脚
   **2、工作原理**
   每1路的传感器的红外发射管不断发射红外线，当发射出的红外线没有被反射回来或被反射回来但强度不够大时，红外接收管一直处于关断状态，此时模块的TTL输出端为高电平，相应指示二极管一直处于熄灭状态;当被检测物体出现在检测范围内时，红外线被反射回来且强度足够大，红外接收管导通，此时模块的TTL输出端为低电平，指示二极管被点亮。
   简单点说，当传感器检测到障碍物时，对应的TTL输出低电平，比如第一路传感器信号线连接在单片机的P10口，当第一路传感器检测到障碍物时，单片机P10口就为低电平，也就是说通过读取传感器信号线连接的单片机I/O口的高低电平，就可以知道传感器前方有没有障碍物。

   **3、检测距离的调节**
   当模块检测到前方障碍物信号时，电路板上红色指示灯点亮电平，同时oUT端口持续输出低电平信号，也就是说，我们可以通过在传感器前面一定距离放置障碍物，通过观察电路板上的指示灯的亮灭，来调节检测距离，检测距离可以通过电位器进行调节，顺时针调电位器，检测距离增加;逆时针调电位器，检测距离减少，官方介绍该模块检测距离2~30cm，但是根据我的实测在20cm以上时，随着距离的增加会趋向不稳定，尤其是在30cm附近，车处于运动状态时可能会由于车的震动从而使传感器始终处于检测到障碍物状态，所以检测距离一般调节在20几厘米左右较好。
   **4、注意事项**
   （1）使用本模块时候，避免探头阳光直射。光线对模块有干扰作用。也就是说本模块受阳光干扰严重，在室外传感器大概率不能正常工作，当然可以采取一定的防护措施，但是效果有限，这也是本次我不采用这种传感器的原因
   （2）灵敏度调节不应过高，过高的灵敏度可能引起误触发。
   （3）在临界值时，会出现ED微亮，这种情况是未触发状态。此时输出为高电平。

### 2. STC89C52单片机基本的外设讲解
#### 2.1 GPIO
概念
GPIO（general purpose intput output）是通用输入输出端口的简称，可以通过软件来控制其输入和输出。51 单片机芯片的 GPIO 引脚与外部设备连接起来，从而实现与外部通讯、 控制以及数据采集的功能。过 GPIO 最简单的应用还属点亮 LED 灯了，只需通过软件控制 GPIO 输出高低电平即可。当然GPIO 还可以作为输入控制，比如在引脚上接入一个按键，通过电平的高低判断按键是否按下。

###### 2.1.1点亮一个LED
![在这里插入图片描述](https://img-blog.csdnimg.cn/b7920abdbb6947f7ad67a75fc0fb1d88.png)
按照上图，我们要如何点亮D1？
答案是让P20处于低电平状态，那么代码如何编写呢？

```c
#include <reg52.h>  //此文件定义单片机的一些特殊功能寄存器
 
sbit D1=P2^0;	   //将单片机的P2.0端口定义为D1
 
void main()      //每一个main.c中必须包含一个主函数，程序从这里开始执行
{
	while(1)   //while死循环，程序将在这里进行不断重读执行
	{
		D1=0;	//P2.0端口设置为低电平，根据二极管原理点亮LED灯			
	}		
}
```

###### 2.1.2 按键检测
![在这里插入图片描述](https://img-blog.csdnimg.cn/bdf0f72e5f2c4c249a1323b355d907a7.png)
如上图所示，我们要如何用程序读取P31按键的状态呢？
如何用按键控制之前的LED呢？

```c
#include <reg52.h>
#include <intrins.h>         //有nop
 
sbit D1=P2^0;
sbit k1=P3^1;
 
void Delay11ms()		//@11.0592MHz，11毫秒 延时函数使用工具生成
{
	unsigned char i, j;
	_nop_();
	_nop_();
	_nop_();
	i = 119;
	j = 82;
	do
	{
		while (--j);
	} while (--i);
}
 
void indpendent_key()
{
	if(k1==0)
	{
		Delay11ms;//延时消抖
		if(k1==0)
		 while(!k1);      //检测按键是否松开
		 D1=!D1;
	}
}
 
 
void main()
{
	while(1)
	{
		independent_key();
	}
}	
```
###### 2.1.3红外模块数据读取
将红外避障模块的数据储存在EYE数组中
```c
#include <REGX52.H>
#include <stdio.h>

sbit left_trace=P3^7;//左边红外避障模块
sbit right_trace=P3^5;//中间红外避障模块
sbit mid_trace=P3^6;//右边红外避障模块
uint8_t ERE[3]=0;
void main()
{
	
	while(1)
	{
		EYE[0]=left_trace;
		EYE[1]=right_trace;
		EYE[2]=mid_trace;

	}
}

```

#### 2.2中断的概念
先看百度百科是怎么定义中断的:

> 中断是指计算机运行过程中，出现某些意外情况需主机干预时，机器能自动停止正在运行的程序并转入处理新情况的程序，处理完毕后又返回原被暂停的程序继续运行。
> —— 百度百科

那么怎么理解中断？看下面的例子。

> 关于中断： 小A正在学习。这时，他的朋友小B叫他一块儿吃鸡，小A停止学习，转去玩吃鸡游戏。玩了几局后，关掉游戏，继续学习。 关于中断优先级：
> 小A正在学习。这时，他的朋友小B叫他一块儿吃鸡，小A停止学习，转去玩吃鸡游戏，（吃鸡过程中，小A女朋友打来电话，于是挂机游戏，去接电话，接完电话，继续游戏。）玩了几局后，关掉游戏，继续学习。

**几个重要概念：**
中断：小A学习被小B打断的过程就称为中断。
中断源：小B被称为中断源。
中断服务程序：小A执行的玩游戏操作称为中断服务程序
中断优先级：小A女朋友的电话比游戏优先级高

**在89c52单片机中，有3类中断源：**

1、外部中断：当外部中断引脚信号产生跳变（低电平→高电平）时引起中断。
2、定时器/计数器中断：当计数器计满溢出时引起跳变。
3、串口中断：串行端口完成一帧数据的发送/接受时引起（如蓝牙传输）。

**其优先级如下表：**

| 中断源 |  优先级 |中断服务号 |
|:--------:| :-------------:|:--------:|
| INT0 外部中断0|最高  |0
| T0 定时器0中断|第二  |1
| INT1 外部中断1|第三  |2
| INT0 外部中断|第四 |3
| 串口中断|第五|4
| T2 定时器0中断|第六  |5


下面以定时器中断为例，讨论中断的编程方法。
##### 2.2.1 为什么要用中断
根据现有的知识，如果要在程序中等待一端时间，想到的操作应该是通过执行若干次空指令，达到延时的效果。
如下：

```c
//延时xms
void delayms(uint xms){
  uint i,j;
  for(i = 0; i < xms; ++i)
    for(j = 0; j < 110; ++j)
    ;
}

```
但是，假设要实现以下功能：

> 1、8位数码管动态扫描显示。 2、LED灯每隔1s闪烁一次。

![在这里插入图片描述](https://img-blog.csdnimg.cn/94a558b67b3c4601a6a9ff43d16eb556.png)

```c
//代码不完整，仅为举例说明
void main(){
  P2 = 0x01;       //数码管从最低位开始扫描
  while(1){
    //功能1：执行数码管动态扫描
    //P2控制显示哪一个数码管，P0控制数码管显示什么内容
    P2 = P2<<1;      //扫描更高一位的数码管
    P0 = xxxx;       //输出段码
    delayms(5);      //延时5ms后显示下一位数码管
  
    //功能2：执行LED灯闪烁
    led = ~led;     //LED灯状态取反
    delayms(1000);  //延时1000ms
  }
}

```
功能1和功能2单独写都没有问题。
但是如果组合在一起，写在一个while循环中，就会有问题了：
一个while循环中有两个延时函数，因此执行一次while循环，共延时了1005ms。这并不是我们所希望的结果。我们希望led闪烁的延时不影响数码管动态扫描的延时。
因此我们可以使用RTOS或者定时器中断来改良这个程序，接下来让我们看看定时器中断是如何实现上述要求的。
##### 2.2.2 中断寄存器
要使用硬件定时，主要涉及到寄存器的操作。51单片机里的关于中断的寄存器如下：
|IE  |–中断允许控制寄存器  |
|-|--|
| IP   |中断优先级控制寄存器  |
| TMOD  |定时器工作方式寄存器  |
|TCON |定时器控制寄存器|  
| SCON  | 串口控制寄存器  
| THx/TLx  |–定时器初值寄存器  


在定时器中断中，需要设置的有TMOD、THx/TLx、TCON、IE。
下面只介绍使用定时器中断所需要设置的寄存器，其余寄存器可自行查阅资料。

##### 2.2.3 中断允许控制寄存器 IE
该寄存器的主要功能是控制中断的开启与关闭，共7个有效位，包含一个全局中断控制位和6个中断源的控制位。
中断允许控制寄存器 IE各位的定义如下表：

![](https://img-blog.csdnimg.cn/99d4d47e9ff34076a8a2c86925000704.png)

说明：
EA 全局中断允许位，当此位是1时中断可用。（重要）
ET2 定时器/计数器2中断允许位
ES 串口中断允许位
ET1 定时器/计数器1中断允许位
EX1 外部中断1允许位
ET0 定时器/计数器0中断允许位 （重要）
EX0 外部中断0允许位
要使用定时器中断，需要将IE寄存器中的EA位设置为1，以及需要将ETx(x = 0,1,2)设置为1。

##### 2.2.4 定时器工作方式寄存器 TMOD
该寄存器的主要功能是设置定时器/计数器中断的工作方式。如设置位定时器模式、定时器模式的计数位的位数。以下是详细介绍：
定时器工作方式寄存器 TMOD各位的定义如下表：

![](https://img-blog.csdnimg.cn/807068b61ce84329afeaf2c0709bdda6.png)

说明：
GATE 定时器/计数器的开关控制选项。常将该位置0，即定时器/计数器的开关控制仅由TCON寄存器中的TRx(x = 0,1)控制。（见2.2.3的TRx）
C/T 定时器模式和计数器模式选择位，将该位置0则为定时器模式。
M1M0 设置定时器/计数器工作方式，常将该两位设置为0 1,其定义如下表：

![、](https://img-blog.csdnimg.cn/c8cf92251db64055a494ed8177bd6e49.png)

##### 2.2.5 定时器控制寄存器 TCON
该寄存器用于控制中断，如控制定时器的启动，停止、判断定时器的溢出和中断情况。
定时器控制寄存器 TCON各位的定义如下表：

**序号	D7	D6	D5	D4	D3	D2	D1	D0
符号	TF1	TR1	TF0	TR0	IE1	IT1	IE0	IT0**
说明：
**TF1 定时器1溢出标志位
TR1 定时器1运行控制位，将该位置1时启动定时器1
TF0 定时器0溢出标志位
TR0 定时器0运行控制位，将该位置1时启动定时器0 （重要）
IE1 外部中断1请求标志
IT1 外部中断1触发方式选择位
IE0 外部中断0请求标志
IT0 外部中断0触发方式选择位**

##### 2.2.6  定时器初值寄存器 THx/TLx
以定时器T0为例，其的工作原理是，每当晶振产生一次脉冲，就将该寄存器TL0加一，当TL0加满溢出后，将TL0清空，TH0加一，TH0计满后产生定时中断。即TH0与TL0组成了一个16位的计数器，这个计数器可以从0x0000（0）加到0xffff（65535）。
**以12Mhz的晶振、定时10ms为例：
51单片机为12分频单片机，因此执行一条指令的时间是12×(1/12M) s，即计数器每1us加一。**
**若定时10ms，则共需要加10000次。
因此将TH0、TL0设置从（65536-10000）= 55536开始计数。55536 的16进制为0xD8F0。因此将TH0设置为0xD8,TL0 设置为0xF0。**


#### 2.3 利用定时器生产PWM波

###### 2.3.1什么是PWM
​ 脉冲宽度调制(PWM)，是英文“Pulse Width Modulation”的缩写，简称脉宽调制，是利用微处理器的数字输出来对模拟电路进行控制的一种非常有效的技术，广泛应用在从测量、通信到功率控制与变换的许多领域中。 ​



###### 2.3.2PWM的频率：

是指1秒钟内信号从高电平到低电平再回到高电平的次数(一个周期)；

也就是说一秒钟PWM有多少个周期
单位： Hz
表示方式： 50Hz 100Hz

###### 2.3.3PWM的周期：

T=1/f
周期=1/频率
50Hz = 20ms 一个周期

如果频率为50Hz ，也就是说一个周期是20ms 那么一秒钟就有 50次PWM周期

###### 2.3.4占空比：
是一个脉冲周期内，高电平的时间与整个周期时间的比例
单位： % (0%-100%)
表示方式：20%

**周期：** 一个脉冲信号的时间         1s内测周期次数等于频率
**脉宽时间：** 高电平时间
![在这里插入图片描述](https://img-blog.csdnimg.cn/dec6520695e34cd1a4f9d35b291c7d13.png)

上图中 脉宽时间占总周期时间的比例，就是占空比

比方说周期的时间是10ms，脉宽时间是8ms 那么低电平时间就是2ms 总的占空比 8/(8+2)= 80%

这就是占空比为80%的脉冲信号

而我们知道PWM就是脉冲宽度调制 通过调节占空比，就可以调节脉冲宽度(脉宽时间) 而频率 就是单位时间内脉冲信号的次数，频率越大

以20Hz 占空比为80% 举例 就是1秒钟之内输出了20次脉冲信号 每次的高电平时间为40ms

我们换更详细点的图
![在这里插入图片描述](https://img-blog.csdnimg.cn/221a02770e99472ba9c4f7b8bcc71675.png)


上图中，周期为T
T1为高电平时间
T2 为低电平时间

假设周期T为 1s 那么频率就是 1Hz 那么高电平时间0.5s ，低电平时间0.5s 总的占空比就是 0.5 /1 =50%

###### 2.3.5PWM原理
以单片机为例，我们知道，单片机的IO口输出的是数字信号，IO口只能输出高电平和低电平

假设高电平为5V 低电平则为0V 那么我们要输出不同的模拟电压，就要用到PWM，通过改变IO口输出的方波的占空比从而获得使用数字信号模拟成的模拟电压信号

我们知道，电压是以一种连接1或断开0的重复脉冲序列被夹到模拟负载上去的（例如LED灯，直流电机等），连接即是直流供电输出，断开即是直流供电断开。通过对连接和断开时间的控制，理论上来讲，可以输出任意不大于最大电压值（即0~5V之间任意大小）的模拟电压

比方说 占空比为50% 那就是高电平时间一半，低电平时间一半，在一定的频率下，就可以得到模拟的2.5V输出电压 那么75%的占空比 得到的电压就是3.75V
![在这里插入图片描述](https://img-blog.csdnimg.cn/722391c84caf43848ed8641b88fd3678.png)


pwm的调节作用来源于对“占周期”的宽度控制，“占周期”变宽，输出的能量就会提高，通过阻容变换电路所得到的平均电压值也会上升，“占周期”变窄，输出的电压信号的电压平均值就会降低，通过阻容变换电路所得到的平均电压值也会下降

也就是，在一定的频率下，通过不同的占空比 即可得到不同的输出模拟电压

pwm就是通过这种原理实现D/A转换的。

总结：
PWM就是在合适的信号频率下，通过一个周期里改变占空比的方式来改变输出的有效电压

PWM频率越大，相应越快，
###### 2.3.6 51单片机使用定时器中断输出PWM
第一步按上文提到的寄存器配置定时器。
在定时器中断中比较Counter 和 Compare的值 ，当Counter 小于 Compare，P31输出低电平，在定时器中断中每100微秒Counter进行自增，大于5后为高电平，大于20后置0，所以5-20为高电平，0-5为低电平，周期为100微秒x20，占空比为75&。

```c
#include <reg51.h>
 
#define uint unsigned int
#define uchar unsigned char
#define ulong unsigned long
 
sbit GPIO_OUT= P3 ^ 1; //定义P3^1口为输出
 
uchar Counter = 0, Compare = 5;
ulong Ture_int = 0, Flast_int = 0; //用于记录高低电平产生的次数，本代码没有使用
 
void Timer0Init(void) //100微秒@12.000MHz
{
	TMOD &= 0xF0; //设置定时器模式
	TMOD |= 0x02; //设置定时器模式
	TL0 = 0x9C;	  //设置定时初值
	TH0 = 0x9C;	  //设置定时重载值
	TF0 = 0;	  //清除TF0标志
	TR0 = 1;	  //定时器0开始计时
	ET0 = 1;	  //定时器0中断开关
	EA = 1;		  //中断总开关
}
void main()
{
	Timer0Init();
	while (1)
	{
	}
}
 
void Timer0_Routune() interrupt 1 //中断函数，定时器0的中断号为1
{
	if (Counter == 20)
	{
		Counter = 0;
	}
	Counter++;
	if (Counter <= Compare)
	{
		//Ture_int++;
		GPIO_OUT = 0;
	}
	else
	{
		//Flast_int++;
		GPIO_OUT = 1;
	}
}
```


### 3. L298N电机驱动使用方法
![在这里插入图片描述](https://img-blog.csdnimg.cn/53ebd4e78b554a8181f7e4521636c9b6.png)4.1 对于ENA、ENB，通道使能引脚。

> 通过将引脚接为高电平或低电平控制接通还是关断，上图就是通过一个跳线帽接到高电平使能。ENA使能左侧电机，ENB使能右侧电机。

4.2 对于逻辑输入引脚IN1、IN2

       控制电机的正反转，将其接在单片机的I/O口上控制即可。
![在这里插入图片描述](https://img-blog.csdnimg.cn/37a690fabe91471ea08e43ef5e743d8c.png)
#### 3.1 利用L298N让电机转起来
①不考虑电机调速：使能引脚ENA置1后，直接对IN1、IN2接高低电平即可，此时电机以最快速度运转。

②考虑电机调速：使能引脚ENA置1后，逻辑控制(IN1、IN2)引脚需要接PWM输出。
上文已提到如何生产PWM波，希望你能自己完成，当然代码也会在下方贴出。

       i、只进行调速不控制转换方向：IN1→PWM，IN2→GND

       ii、即调速又转换方向：IN1→PWM1，IN2→PWM2。

                正转时让PWM2输出占空比为0的波形(相当于置0)，通过调节PWM1的占空比进行调速；

                反转时让PWM1输出占空比为0的波形(相当于置0)，通过调节PWM2的占空比进行调速；
            
            
下面的程序生产的是50%占空比的PWM波，在L298N IN2和IN4接地后使电机转动
```c
#include <reg51.h>
 
#define uint unsigned int
#define uchar unsigned char
#define ulong unsigned long
 
uchar Counter = 0, Compare = 5;
bit Left_moto_stop =1;
bit Right_moto_stop =1;
unsigned char pwm_val_left =0;
unsigned char push_val_left =5; 
unsigned char pwm_val_right =0;
unsigned char push_val_right=5;


void pwm_out_left_moto(void)     //左电机调速
{ 
 if(Left_moto_stop) 
 { 
	if(pwm_val_left<=push_val_left) 
 	Left_moto_pwm=1; 
 	else 
	Left_moto_pwm=0; 
 } 
}

void pwm_out_right_moto(void)   //右电机调速
{ 
if(Right_moto_stop) 
  { 
	if(pwm_val_right<=push_val_right) 
	Right_moto_pwm=1; 
	else 
	 Right_moto_pwm=0; 
	}
}


 
void Timer0Init(void) //100微秒@12.000MHz
{
	TMOD &= 0xF0; //设置定时器模式
	TMOD |= 0x02; //设置定时器模式
	TL0 = 0x9C;	  //设置定时初值
	TH0 = 0x9C;	  //设置定时重载值
	TF0 = 0;	  //清除TF0标志
	TR0 = 1;	  //定时器0开始计时
	ET0 = 1;	  //定时器0中断开关
	EA = 1;		  //中断总开关
}
void main()
{
	Timer0Init();
	while (1)
	{
	
	}
}
 
void Timer0_Routune() interrupt 1 //中断函数，定时器0的中断号为1
{
	push_val_left++;
	push_val_right++;
	pwm_out_right_moto();
	pwm_out_left_moto();
	if(pwm_val_right>=10) 
		pwm_val_right=0; 
	if(pwm_val_left>=10) 
		pwm_val_left=0; 
} 
}

```


### 5.循迹算法实现
循迹原理非常简单

> EYE数组有001 011 111 101 110 100 010 000几种可能
> 当001时 说明小车偏左，100则小车偏右，以此类推。
> 小车偏左时，左轮转速大于右轮即可修正。

```c
void follow_way()
{   
    if((EYE[0]==0)&&(EYE[2]==1))
    {
	    flag = 0;//左边检测到黑线
    }
    else if((EYE[0]==1)&&(EYE[2]==0))
    {
		  flag = 1;//右边检测到黑线
	}
    else if(EYE[0]==1)&&(EYE[2]==1)
	{
			flag = 3; //两边都没检测到黑线
	}
		else
	{
		  flag = 4;
	}
		switch(flag)
	{
		case 0:  Right_turning();break;
		case 1:  Left_turning();break;
		case 3:  forward_move();break;
		case 4:  forward_move();break;
		default: forward_move();break;
	}
}
```

#  第二章 电磁循迹小车设计
本章对于外设驱动不再讲解只降解核心内容
## 电感循迹的原理
![在这里插入图片描述](https://img-blog.csdnimg.cn/f0473336b5e8437ab73b1ae7db987227.png)
         首先要了解车模位于赛道中的不同位置时所采集的电感数据。参加比赛的同学在学习智能车如何制作前都会先阅读规则，规则中有说明，电磁引导是通过在赛道中心铺设漆包线并通以20khz的交变信号。我们通过中学物理知识可以得知，导线通以交变电流之后会产生交变磁场，电感线圈在交变磁场中会产生交变电压，电感距离导线越近电感产生的电压峰峰值则越大，通过对电感感应出来的电压进行放大并整流可以得到一个直流信号，直流信号电压越高距离导线则越近。需要注意的是电感摆放的位置需要让磁感线能够穿过电感的线圈，这样才能正确的感应到磁场大小。接下来我们根据距离导线越近数值越大的理论来合理的假设电感的数据，然后建立一个表格来分析下数据并使用差比和公式计算结果，**差比和的公式为:(a-b)/(a+b)**。
![在这里插入图片描述](https://img-blog.csdnimg.cn/f0547d4f932a4355906e0ce70aa2bd34.png)
从计算出来的结果我们看出来，当车模放置在赛道中间时计算出来的数值为0，当车模放置在赛道的左侧时计算出来的数值为-0.5，当车模放置在赛道的右侧时计算出来的数值为+0.5。那么我们可以通过判断计算出来的数值符号来判断车模在赛道的左边还是右边。那么差比和计算出来的数值又表示什么呢？我们可以在假设两组数据来计算一下，将图2与图3中车模的位置都往赛道中心线靠近一点，我们知道电感距离漆包线越近所感应到的电压也就越大。因此图2中车模移动位置之后电感1的数值将会变大，电感2的数值将会变小，同理图3中电感1的数值将会变小，电感2的数值将会变大。通过计算后：
![在这里插入图片描述](https://img-blog.csdnimg.cn/f8471da0d9b34c7f8b3d0900df199498.png)
我们会发现，车模距离赛道中心线的距离变近之后差比和计算出来的绝对值也就变小了。因此数值大小表示偏离赛道的程度，在一定范围内车模偏离赛道越远计算出来的值越大。得到了车模偏离赛道的程序之后我们就可以用此数据控制舵机，来使得车模一直沿着赛道中心线前进了


## 电感数据采集
通过adc采集的函数uint16 adc_mean_filter(ADCN_enum adc, ADCCH_enum ch, uint8 count)；我们确定了用哪个通道，和采集次数，采样率一般是固定的，也可以自己改，我的电感采集是12位，也就是0~4096，但是在实际赛道上采样的值不会超过3600，一定要注意不要将原始的值调到满幅值。这里推荐的方法是：第一步，将车放到环岛四条线交叉（或者环岛两条（因为今年是铺了两圈）），调节电磁（与四条线垂直的地方）在此时稍微调小电位器，电磁值会有减小，调大值几乎不变（即几乎为最大值），左右横电感都是这样，粗调结束（记录此时的最大值）；第二步，将电感放在直赛道上，将电感和电磁线垂直放置，此时最大值为之前最大值的90%以下，要是不到的话就调小，然后把车放在赛道上，保证在赛道最中间（此时电磁线是在两横电感连线中线上），将一侧作为基准，调节另一侧使值相差在（最大值-0）的千分之一内（这样是最好的，但是和你的精度有关，我最大是3600左右，两边值的原始差在5左右）。这样横电感的调节结束；向外的v形电感，在环岛处的较易获得的值是其他元素最大时都无法达到的值即可（就是你在环岛那从预环岛开始稍微转车，或者接近环岛时车即使有些偏移也可以得到的值，但是其他元素这么转都很难得到的值）。内v形电感和水平电感处理类似。具体原因的话，我在元素判断中解释。
## 电感数据处理
1.归一化
就是用第一步采集的最大值（左右对称的电感用同一个），用adc采集的值ADC_value。进行 ADC_value/max*100,将值变道0~100的区间内。这样做的好处是，1、可以方便自己对数据的感知，在普通元素和特殊元素间；2、在赛道更换后，测新的赛道的最大值，改变max的值即可，有较强的适应性。3、方便数据处理。

2.差比和
首先我们不去了解电感具体原理，我们会知道，当靠近电磁线时，电磁值就会变大，远离就会减小。那么如果用L-R,若值为正，说明车向右侧偏；反之，向左，这样我们就可以知道车的偏向了。那么差比和（L-R）/（L+R）的作用就是使得到的曲线L-R曲线变得平滑，理论上平滑曲线会使得车的控制效果更好，但是我因为能力有限没有去关注。


```c
#include "headfile.h"  
#define ADC_LEFT_CHANNEL    ADC1_CH3_B14//定义左电感通道  
#define ADC_RIGHT_CHANNEL   ADC1_CH4_B15//定义右电感通道  
uint8 ad_left;  
uint8 ad_right;  
int16 ad_sum;  
int16 ad_diff;  
int16 position;  
int16 ad_max_left;
int16 ad_max_right;
int16 ad_min_left;
int16 ad_min_right;
int main(void)  

{  

    DisableGlobalIRQ();  
    board_init();//务必保留，本函数用于初始化MPU 时钟 调试串口  
    adc_init(ADC_1,ADC_LEFT_CHANNEL,ADC_8BIT);//初始化B14引脚为ADC功能，分辨率为8位  
    adc_init(ADC_1,ADC_RIGHT_CHANNEL,ADC_8BIT);//初始化B15引脚为ADC功能，分辨率为8位 
    EnableGlobalIRQ(0);  

    while (1)  

    {  

        ad_left = adc_mean_filter(ADC_1,ADC_LEFT_CHANNEL,5);//采集左电感数值 采集5次求平均  
        ad_right = adc_mean_filter(ADC_1,ADC_RIGHT_CHANNEL,5);//采集右电感数值 采集5次求平均 
        //为差比和做准备
        if(ad_left>ad_max_left)
	ad_max_left=ad_left;
	if(ad_right>ad_max_right)
	ad_max_right=ad_right; 
	if(ad_left<ad_max_left)
	ad_min_left=ad_left; 
	if(ad_right<ad_min_right)
	ad_min_right=ad_right; 
	//进行归一化将数据归于0~100
	Aad_left =(uint16)(99*(ad_left-ad_min_left)/(ad_max_left-ad_left)+1);
	Aad_right =(uint16)(99*(ad_right-ad_min_right)/(ad_max_right-ad_right)+1);

        ad_sum = ad_left + ad_right;//计算电感之和  
        ad_diff = (int16)ad_left - ad_right;//计算电感之差  
        
        error = (ad_diff)/(ad_sum+1);//计算差比和数值   

 

        

    }  

}  
```





## 位置式PID算法及参数整定
## 增量式PID算法及参数整定

#  第三章 摄像头循迹小车
### 摄像头循迹的原理
### 摄像头数据采集
### 图片数据二值化方法
### 搜寻边界的方法
### 赛道特殊元素的识别
### 模糊PID算法及参数整定

# 第四章 自平衡单车设计
### 平衡原理讲解
### 串级PID算法的讲解


---

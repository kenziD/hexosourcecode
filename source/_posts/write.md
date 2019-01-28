title: "科研报告" 
date: 2015-08-18 16:37:21
tags: 科研
---
####  FLY 竹蜻蜓
-----

暑假回来，有一点假期综合征，完全不想科研，为了学习一下markdown，就在sublime text3 上安装了markdown preview 插件来写报告，暂时不使用latex。

为了给导师汇报，那就总结一下一个月前的科研工作（对，暑假放了快一个月）。

先总结一下上学期做的工作，和需要完成的工作:

- [x] 利用NRF24L01模块，将战舰开发板与四轴对接，做成了一个简单的遥控器。（遇到了丢包的问题不过换了原装的就好了）
- [x] 搞清楚了ANO FLY 匿名上位机的协议，由上位机看姿态角波形，为调PID提供方便。
- [x] 利用上位机调试PID，能勉强实现单通道的姿态平衡。
- [ ] 开发原装遥控器
- [ ] 调节好PID，使飞机能飞起来
- [ ] 学习飞控算法，阅读相关论文

<!--more-->
## NRF24L01模块

NRF24L01使用SPI接口进行设置（之前已经用过的蓝牙模块和usb转串口模块是使用IIC接口进行通讯），再次熟悉了SPI通信协议。

由于战舰开发板已经有成套的NRF24L01的程序，所以选择了使用战舰开发板作为遥控器，缩短开发时间。只是把接收端从另一个战舰开发板（源程序是两块战舰开发板互相通信）改为四轴即可。

## #程序流程：

#### 1. 发送端
![发送端程序流程图](http://7xk7fp.com1.z0.glb.clouddn.com/15.png)
#### 2. 接收端
![接收端程序流程图](http://7xk7fp.com1.z0.glb.clouddn.com/14.png)

### 遇到的问题：

* NRF24L01的调试一直失败

> 问题描述：在检测是否NRF24L01存在的程序中，一直false，即四轴上的NRF24L01模块无法成功配置。由于是战舰开发板的程序直接移植过来的，在战舰开发板上也能调通，所以查了一晚上也没能找到问题。

> 问题原因：发现战舰开发板使用的是SPI2模块，当时也没有多想为什么要使用SPI2而不是直接使用SPI1。其实关键问题就是出在这里。战舰开发板的主控芯片是STM32103ZET6，144引脚；而我用的四轴使用的是STM32103C8T6，是48引脚。不同的型号相同功能的引脚配置可能不一样。所以我只是根据四轴的PCB原理图，对SPI驱动程序中的引脚进行修改，但仍然使用SPI2。但没想到，STM32103C8T6中SPI功能与引脚是有对应关系的。
![四轴NRF24L01管教定义](http://7xk7fp.com1.z0.glb.clouddn.com/1.png)
图中的引脚图就对应的是SPI1，而非SPI2。所以只能使用SPI1而不能使用SPI2。
![四轴NRF24L01管教定义](http://7xk7fp.com1.z0.glb.clouddn.com/2.png)
![STM32单片机SPI1重映像](http://7xk7fp.com1.z0.glb.clouddn.com/3.png)

* NRF24L01丢包严重

> 问题描述：NRF24L01调通以后，就可以互相通讯，并且实现了无线遥控控制电机的转速。我是在低转速的情况下调试的程序，当电机转速加快的话，就出现非常严重的丢包。表现为时而能收到遥控数据，时而收不到。而且随着电机转速的增加，收不到的几率越来越大。

> 问题原因：经过查阅，都说是NRF24L01的模块质量不过关，质量不好的受到电机的影响很大。我现在使用的模块大概1元一个，我换了原装的20元一个的模块，丢包现象就消失了。能够很稳定的收到数据。

## 匿名上位机的学习

匿名上位机功能很多，可以发送并接收PID数据，也可以看多个通道的数据波形，也可以直接按照它写好的协议直接查看特定姿态角的波形，同时可以显示电机的油门和电压。目前就用到了这几个功能。原来觉得协议会很复杂，现在学习后感觉还好，而且很方便，功能也很多，还能帮助理解程序。下面就具体讲讲怎么使用匿名上位机。

![匿名上位机界面](http://7xk7fp.com1.z0.glb.clouddn.com/4.png)
下位机发送自定义数据，格式为：0x88+FUN+LEN+DATA+SUM
FUN可以是 0xA1到0xAA，共10个；LEN为DATA的长度（不包括0x88、FUN、LEN、SUM）。
SUM是0x88一直到DATA最后一字节的和，uint8格式。

* 例：发送一个uint8 类型的数据15(0X0F)：

选择uint8
![匿名上位机高级收码部分](http://7xk7fp.com1.z0.glb.clouddn.com/5.png)
发送格式为：0x88+0xA1+0X01(一个uint8 类型的数据长度为8位，1字节)+0X0F+SUM

* 例：发送一个float 类型的数据：

选择Float
![匿名上位机高级收码部分](http://7xk7fp.com1.z0.glb.clouddn.com/6.png)
发送格式为：0x88+0xA1+0X04(一个浮点型数据长度为32位，4字节)+DATA+SUM，其中Float型数据传首地址，第二字节传 转换成char类型后的首地址指针 加1，以此类推，传够四个字节。
```C
#define BYTE0(DATA)       (*(char *)(&DATA)) //(char *)(&DATA)取DATA地址，是一个指针，把这个指针转换成char类型，*(char *)(&DATA)，然后再读取这个指针指向的地址中的变量。因为char只有1字节。所以相当于只读取了DATA的前8位。
#define BYTE1(DATA)       (*((char *)(&DATA) + 1))
#define BYTE2(DATA)       (*((char *)(&DATA) + 2))
#define BYTE3(DATA)       (*((char *)(&DATA) + 3))
```

* 例：同时发送一个uint8 0x03，和一个uint16类型的数据 0x30E0：

选择第一位为uint8，第二位为uint16：
![匿名上位机高级收码部分](http://7xk7fp.com1.z0.glb.clouddn.com/7.png)

发送格式为：0x88+0xA1+0X03(8位+16位共3字节)+DATA+SUM
DATA:send(0x03)+send(BYTE0(0X30E0))+send(BYTE1(0X30E0))
在上位机中读取数据：
选择第一帧的第一位，在右边的方框中即可看到uint8类型的数据
![匿名上位机高级收码部分](http://7xk7fp.com1.z0.glb.clouddn.com/8.png)
选择第一帧的第二位，在右边的方框中即可看到uint16类型的数据
![匿名上位机高级收码部分](http://7xk7fp.com1.z0.glb.clouddn.com/9.png)
如果想要看波形的话：
首先设置通道1显示第一帧的第一位：
![匿名上位机高级收码部分](http://7xk7fp.com1.z0.glb.clouddn.com/10.png)
设置通道2显示第一帧的第二位：
![匿名上位机高级收码部分](http://7xk7fp.com1.z0.glb.clouddn.com/11.png)
在波形显示中勾选serial1 和 serial2即可同时观察这两个数据的波形
![匿名上位机波形显示部分](http://7xk7fp.com1.z0.glb.clouddn.com/12.png)

显示飞控参数举例：
根据帮助文档里的描述：飞控显示对应的帧FUN为0xAF，（帧格式：0x88+0xAF+0x1C+ACC DATA+GYRO DATA+MAG DATA+ANGLE DATA+ 0x00 0x00 + 0x00 0x00+SUM，共32字节，ACC/GYRO/MAG/ANGLE(roll/pitch/yaw)数据为int16格式，其中ANGLE的roll和pitch数据为实际值乘以100以后得到的整数值，yaw为乘以10以后得到的整数值，上位机在显示时再 除以100和10）。

对应程序为：
```C
unsigned char TxBuffer[32];//一共发送的字节数 记得改
unsigned char count=0;

#define BYTE0(dwTemp)       (*(char *)(&dwTemp))
#define BYTE1(dwTemp)       (*((char *)(&dwTemp) + 1))
#define BYTE2(dwTemp)       (*((char *)(&dwTemp) + 2))
#define BYTE3(dwTemp)       (*((char *)(&dwTemp) + 3))

/**************************向物理串口发一个字节***************************************
*******************************************************************************/
__inline unsigned char UART_Putc(unsigned char data)			//
{
	USART_SendData(USART1,  (uint8_t)data);
	while (USART_GetFlagStatus(USART1, USART_FLAG_TC) == RESET);
	return data;
}

void Uart1_Send_AF(int16_t aa,int16_t bb,int16_t cc,int16_t dd,int16_t ee,int16_t ff,int16_t gg,int16_t hh)
{
	unsigned char sum = 0;
	count=0;
	sum += Uart1_Put_Char(0x88);
	sum += Uart1_Put_Char(0xAF);
	sum += Uart1_Put_Char(0x1C);//共14字节
	sum += Uart1_Put_Char(BYTE1(aa));//1
	sum += Uart1_Put_Char(BYTE0(aa));
	sum += Uart1_Put_Char(BYTE1(bb));//2
	sum += Uart1_Put_Char(BYTE0(bb));
	sum += Uart1_Put_Char(BYTE1(cc));//3 ACC DATA
	sum += Uart1_Put_Char(BYTE0(cc));
	sum += Uart1_Put_Char(BYTE1(dd));//4
	sum += Uart1_Put_Char(BYTE0(dd));
	sum += Uart1_Put_Char(BYTE1(ee));//5
	sum += Uart1_Put_Char(BYTE0(ee));
	sum += Uart1_Put_Char(BYTE1(ff));//6 GYRO DATA
	sum += Uart1_Put_Char(BYTE0(ff));
	Uart1_Put_Char(0);//7
	Uart1_Put_Char(0);
	Uart1_Put_Char(0);//8
	Uart1_Put_Char(0);
	Uart1_Put_Char(0);//9 磁力计
	Uart1_Put_Char(0);
	sum += Uart1_Put_Char(BYTE1(gg));//10 ANGLE DATA ROLL
	sum += Uart1_Put_Char(BYTE0(gg));
	sum += Uart1_Put_Char(BYTE1(hh));//11 PITCH
	sum += Uart1_Put_Char(BYTE0(hh));
	Uart1_Put_Char(0);//YAW
	Uart1_Put_Char(0);
	
	Uart1_Put_Char(0);
	Uart1_Put_Char(0);
	Uart1_Put_Char(0);
	Uart1_Put_Char(0);
	Uart1_Put_Char(sum);
}
unsigned char Uart1_Put_Char(unsigned char DataToSend)
{
	TxBuffer[count++] = DataToSend;  
	return DataToSend;
}
void send_wave(int tx_num)//一共发送几个字节
{
	char count_1=0;
	while(count_1<tx_num)
      UART_Putc(TxBuffer[count_1++]);
}
/***********主函数调用***************/
Uart1_Send_AF(0x00,0x00,0x00,0x00,0x00,0x00,(signed short int)(ypr[2]*100),(signed short int)(ypr[1]*100));//只发送了roll和pitch
send_wave(32);
```

使用飞控帧0XAF显示的好处就是，可以在下图中，直接看到各变量的显示
![匿名上位机飞控状态部分](http://7xk7fp.com1.z0.glb.clouddn.com/13.png)

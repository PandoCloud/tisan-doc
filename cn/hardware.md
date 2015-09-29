## 概述  
Tisan物联网开发板由核心板和底板组成。  
Tisan的核心板基于乐鑫的ESP8266。ESP8266是一款SOC WIFI芯片，可将设备连接到Wi-Fi网络上，进行互联网或局域网通信，实现联网功能。核心板引出了11个通用GPIO（8个可用于PWM输出），1个模拟输入口（alalog input），一个复位脚，板上配有一个信号灯和一个按键，一个Micro USB用于直流5V供电。  
Tisan底板，也叫Tisan基板，连接核心板和支持接入各种外设板，基板也板载一些典型的传感器。外设板可以多种多样，可根据不同的应用配备不同的扩展板。我们默认的底板配备了如下传感器模块：三色灯、电机、温湿度传感器、光敏传感器、继电器，另外还配备了USB转串口芯片。  
## 技术规格  
 
核心板的核心模块型号是ESP-12，里面封装了ESP8266EX芯片、26MHz的晶振以及存储容量为4M字节的Flash。ESP8266是一款高度集成的芯片，该芯片专门针对无线连接的需求而开发，是一个完整且自成系统的 Wi-Fi 网络解决方案。芯片详情可下载查看[参考手册](http://bbs.espressif.com/download/file.php?id=562)  
  

### Tisan 技术规格   

| 类型 | 规格/参数 |  
| --- | --------- |  
| 芯片 | ESP8266EX |  
| 工作电压 | 3.3V |  
| 输入电压 | 5V |  
| 数字IO管脚数 | 11 |    
| PWM管脚 | 自定义、可扩展 |  
| 模拟管脚数 | 1个 |  
| SPI | 1个 |
| UART | 2个，其中一个只有TXD功能 |  
| Flash存储 | 4MB（25Q32） |  
| 按键 | 3个。配置按键、复位按键，和烧写按键 |  
| LED | 1个用户LED |  
| 板载应用模块 | 三色灯模块、温湿度模块、继电器模块、电机模块，以及光敏模块 |  
| 尺寸 | 69mm＊45mm＊19mm |  



## 硬件开源文档 
### 包括：原理图、电路图和装配图  
**TISAN开发板原理图**包括了[核心板原理图](reference/tisan-core-board.png)和[底板原理图](reference/tisan-base-board.pdf)  
**TISAN电路图**包括了[核心板电路图](reference/tisan-core_pcb.pdf)和[底板电路图](reference/tisan_base_pcb.pdf)   

### 编程说明  
Tisan的核心板的核心是ESP8266模块，通过底板的USB转串口的进行烧写程序，操作步骤可以参考[开发环境](environment.md)。 
  
### 电源  
Tisan开发板可以通过USB连接或者外接电源来提供电源。电源是自动选择的。  
外部电源（非USB）既可以通过AC-DC适配器来提供，通过连接中心正极为2.1毫米的插头插入主板的电源插孔。  
电源管脚说明：  
- 电源输入。 通过USB接入或者直流5V插头连接提供电源；  
- 5V。 5V电源提供给外设。  
- 3V3。 3.3V 由板上的电源调节器产生   
- GND。 接地管脚。  

### MCU  
Tisan核心板上的ESP-12模块是封装了ESP8266EX芯片的模组。ESP8266EX内置了Tensilica L106超低功耗32位微型MCU，带有16位精简模式，主频支持80MHz和160MHz。目前WiFi协议栈只用了20%的MIPS，其他都可以用了做应用开发。可通过以下接口和芯片其他部分协同工作：  
- 连接存储控制器、也可以用来访问外接闪存的编码 RAM/ROM接口（iBus）  
- 同样连接存储控制器的数据RAM接口（dBus）
- 访问寄存器的AHB接口  
   
### 存储  
Tisan核心板上ESP-12模块封装了ESP8266EX芯片和一个25Q32的Flash芯片。  
ESP8266EX内置SRAM，由于需要加载基本的固件，用户可用剩余SRAM控件约为50KB（station模式下，连上路由后，heap+data区大致可用50KB左右。）。目前ESP8266EX片上没有可编程的ROM，用户程序存放在外部的SPI Flash中。  
[ESP8266EX芯片使用指南](http://bbs.espressif.com/viewtopic.php?f=21&t=412&p=1545#p1545)   
**25Q32**，4M字节的Flash，与芯片通过SPI连接，存放用户程序。支持云端升级。支持的SPI模式：Stanard SPI、Dual SPI、DIO SPI、QIO SPI，以及Quad SPI。  
注意，在下载固件是需要在下载工具中选择对应模式，否则下载后程序将无法得到正确的运行。Tisan采用的是QIO SPI模式  
### 管脚说明    
底板的管脚是从核心板上引出。ESP8266EX芯片的数据手册、硬件使用指南、管脚清单等资料可以在乐鑫官网的论坛里面下载,[芯片使用指南](http://bbs.espressif.com/viewtopic.php?f=21&t=412&p=1545#p1545)  
开发板有11个通用GPIO可以设置为输入或输出，工作电压为3.3V。每一路IO口最大驱动能力为12mA。  
另外，其中一些IO口有专用功能：  
- 串口：ESP8266EX芯片定义了2个UART，两个UART接口均有硬件实现：  
 - 串口0 GPIO1(TXD)，GPIO3(RXD)。对应了Tisan板上的TXD和RXD管脚，用于接收或者发送TTL电平信号的串口数据。这些管脚连接到相应的CH340的USB转TTL电平的串口芯片上。  
 - 串口1 只有TX部分可以用，GPIO2（TXD）  
- PWM： PWM接口由软件实现，GPIO的PWM功能可自行扩展和定义。PWM通过timer中断实现，最小分辨率可达44ns。频率在1KHz时，占空比最小可以达到1/22727。PWM周期为1000us~10000us（100HZ~1KHz）    
- SPI：GPIO12(MISO),GPIO13(MOSI),GPIO14(CLK),GPIO15(CS)  
- LED：板载LED由GPIO5控制。当该管脚电平为高时，LED亮，当该管脚电平为低时，LED灭。   
- 通过IO口复用，可以启用Tisan板载的外设：  GPIO15（三色灯的蓝色控制），GPIO14（三色灯的红色控制），GPIO13（三色灯的绿色控制、电机控制脚），GPIO12（继电器控制、温湿度模块的数据读取、电机控制脚）  
  
Tisan有一个ADC管脚，连了光敏模块。该ADC管脚支持精度为10bit，可检测的模拟输入电压范围是0~1V。
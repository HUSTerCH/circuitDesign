# 实验二：MOS管放大电路（插板）

专业班级：**通信2101班**

姓名：        **罗畅**

学号：        **U202113940**

## 实验名称

MOS管放大电路（插板）

## 实验目的

- 了解MOS管共源放大电路工作原理

- 掌握共源放大电路参数调整方法

- 掌握共源放大电路的基本原理与参数测量方法

- 掌握分立元件复杂电路搭建与调试方法

## 实验元器件

***MOSFET晶体管：2N7000；电阻：1kΩ，5.1kΩ，100kΩ；电容：1μF，4.7μF，47μF；电位器：470k，100k，100***

## 实验原理

### MOSFET共源极放大电路

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E5%AE%9E%E9%AA%8C%E5%8E%9F%E7%90%86_%E5%85%B1%E6%BA%90%E6%9E%81%E6%94%BE%E5%A4%A7%E7%94%B5%E8%B7%AF.png)

- 图3.3.6为N沟道增强型MOSFET共源极放大电路，其静态工作点可由下式估算：

$$
式3.3.3
$$

$$
V_{GSQ} = \frac{R_{g2}}{R_{g1}+R_{g2}}×V_{DD}-I_{DQ}R_s
$$

$$
I_{DQ}=K_n(V_{GS}-V_{TN})^2
$$

$$
V_{DSQ}=V_{DD}-I_{DQ}(R_d+R_s)
$$

- 动态性能指标可由下式估算：

$$
式3.3.4
$$

$$
A_v=-g_mR_d
$$

$$
R_i=R_{g1}//R_{g2}
$$

$$
R_o=R_d
$$

- 数据手册通常会给出$V_TN$和某工作点下的$g_m$。对于MOS管2N7000，当$I_D$=200mA时，$g'_m$=100mS,则由(3.3.3)第二式可得

$$
K_n=\frac{(g'_m/2)^2}{I_D}=12.5mA/V^2
$$

而(3.3.4)第一式中的$g_m$是电路静态工作点下MOS管的互导，同样由式子

$$
r_{ds}=\frac{∂v_{DS}}{∂i_D}|_{V_{GS}}
$$

可得

$$
g_m=g'_m\sqrt{\frac{I_{DQ}}{I_D}}
$$

即

$$
g_m=10\sqrt{\frac{I_{DQ}}{2}} ms
$$

此外$V_{TN}$在0.8-3V之间，这里取$V_{TN}$=1.75V

设置静态工作点时，调整电位器$R_p$，使$V_p$为5-6V

## 实验任务

### 一、测试静态工作点

- 按照图3.3.6连接后，检查无误后接通电源。

- 用数字万用表的直流电压档测量电路的$V_D$（漏极对地电压）

- 调整电位器$R_P$，使$V_D$为5~6V

- 再测出电路的$V_G$（栅极对地电压）和$V_S$（源极对地电压），填入表3.3.2中，并计算静态工作点Q（$I_{DQ}$、$V_{GSQ}$、$V_{DSQ}$）。

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E8%A1%A83.3.2.png)

***注意：接下来的测试不要再改动静态工作点***

### 二、测试放大电路的输入、输出波形和通带电压增益

- 按照下图搭建放大电路实验测试平台

- 调整信号源，使其输出峰一峰值为30mV、频率为1kHz 的正弦波，作为放大电路的$v_i$,分别用示波器的两个通道同时测试$v_i$和$v_o$

- 在实验报告上定量画出$v_i$和$v_o$的波形（时间轴上下对齐）

- 分别测试负载开路和$R_L$=5.1kΩ两种情况下的$v_i$和$v_o$,完成表3.3.3。

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E5%9B%BE3.2.7.png)

表3.3.3

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E8%A1%A83.3.3.png)

### 三、测试放大电路的输入电阻

- 采用在输入回路中串入已知电阻的方法测量输入电阻。

- 由于MOSFET放大电路的输入电阻较大，所以当测量仪器的输入电阻不够大时，采用图3.2.8所示的方法可能存在较大误差，改用如图3.3.7所示的测量输出电压的方法更好

- R取值尽量与$R_i$接近(此处可取R=51 KΩ)。信号源仍旧输出峰峰值30mV、1kHz正弦波

- 用示波器的一个通道始终监视$v_i$波形，用另一个通道先后测量开关S闭合和断开时对应的输出电压$v_{o1}$和$v_{o2}$，则输入电阻为
  
  $$
  R_i=\frac{v_{o2}}{v_{o1}-v_{o2}}R
  $$

- 测量过程要保证$v_o$不出现失真现象

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E5%9B%BE3.2.8.png)

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E5%9B%BE3.3.7.png)

### 四、测试放大电路的输出电阻

- 采用改变负载的方法测试输出电阻。

- 分别测试负载开路输出电压$v'_o$和接入已知负载$R_L$时的输出电压$v_o$测量过程同样要保证$v_o$不出现失真现象。实际上在表3.3.3 中已得到$v'_o$和$v_o$则输出电阻为
  
  $$
  R_o=\frac{v'_o-v_o}{v'_o}×R_L
  $$
  
  $R_L$越接近$R_o$误差越小

### 五、测试放大电路的通频带

- 在图3.3.6 中，输入$v_i$为峰-峰值30mV、1kHz的正弦波，用示波器的一个通道始终监视输入波形的峰-峰值，用另一个通道测出输出波形的峰-峰值。

- 保持输入波形峰峰值不变，调节信号源的频率，逐渐提高信号的频率，观测输出波形的幅值变化，并相应适时调节示波器水平轴的扫描速率，保证始终能清晰观测到正常的正弦波。

- **持续提高**信号频率，直到输出波形峰-峰值降为1kHz时的**0.707倍**，此时信号的频率即为上限频率$f_H$，记录该频率;

- 类似地，**逐渐降低**信号频率，直到输出波形峰峰值降为1kHz时的**0.707 倍**，此时的频率即为信号频率$f_L$，记录该频率，完成表3.3.4。

- **要特别注意，测试过程必须时刻监视输入波形峰-峰值，若有变化，需调整信号源的输出幅值，保持$v_i$的峰-峰值始终为30mV。**
  
  **通频带(带宽)为BW=$f_H$-$f_L$。**
  
  ![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E8%A1%A83.3.4.png)

### 六、观察失真波形

- 调整信号源频率调回1kHz，分别用示波器的两个通道同时观测$v_i$和$v_o$，不断调整电位器$R_P$，观察$v_o$波形的变化，直至出现明显的非线性失真。

- 在表3.3.5中定性画出失真波形形状，并用万用表的直流电压档测量电路的$V_D$、$V_G$和$V_S$填入表3.3.5，计算静态工作点Q（$I_{DQ}$、$V_{GSQ}$、$V_{DSQ}$）。

- 再反方向调整$R_P$，直至$v_o$波形出现另一种非线性失真现象，再次测量静态工作点，完成表3.3.5的内容。（注意，如果调不出失真现象，可以适当增大输入信号的幅值，再调整$R_P$）
  
  ![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E8%A1%A83.3.5.png)

## 实验记录

### 一、测试静态工作点

表3.3.2记录如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E8%A1%A83.3.2_%E8%AE%B0%E5%BD%95.png)

### 二、测试放大电路的输入、输出波形和通带电压增益

示波器图示如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BA%8C%E3%80%81%E8%B4%9F%E8%BD%BD%E5%BC%80%E8%B7%AF.PNG)

$$
无RL（负载开路）
$$

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BA%8C%E3%80%81%E8%B4%9F%E8%BD%BDRL%3D5.1k.PNG)

$$
负载RL=5.1kΩ
$$

表3.3.3记录如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E8%A1%A83.3.3_%E8%AE%B0%E5%BD%95.png)

### 三、测试放大电路的输入电阻

实验测量示波器图示如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%B8%89%E3%80%81R%E6%8E%A5.PNG)

$$
接入R=51kΩ
$$

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%B8%89%E3%80%81R%E6%9C%AA%E6%8E%A5.PNG)

$$
不接R
$$

由

$$
R_i=\frac{v_{o2}}{v_{o1}-v_{o2}}R
$$

有

$$
R_i=\frac{313.9}{520.1-313.9}×51k=77.6377k
$$

又

理论值$R_i$=($R_{g1}$+$R_p$)//$R_{g2}$=**76.3467k**

则相对误差为

$$
\frac{77.6377-76.3467}{76.3467}=1.691\%
$$

### 四、 测试放大电路的输出电阻

实验示波器测量图示如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E5%9B%9B%E3%80%81%E6%9C%89RL.PNG)

$$
有RL
$$

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E5%9B%9B%E3%80%81%E6%97%A0RL.PNG)

$$
无RL
$$

由

$$
R_o=\frac{v'_o-v_o}{v'_o}×R_L
$$

有

$$
R_o=\frac{984.0-520.1}{984.0}×5.1k=2.4044k
$$

### 五、测试放大电路的通频带

实验示波器测量图示如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BA%94%E3%80%811kHz_984.PNG)

$$
1kHz,v_{opp}=984.0mV
$$

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BA%94%E3%80%8126hz_696.PNG)

$$
26Hz,v_{opp}=696.0mV
$$

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BA%94%E3%80%81700kHz_696.PNG)

$$
700kHz,v_{opp}=696.1mV
$$

实验记录表3.3.4如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E8%A1%A83.3.4_%E8%AE%B0%E5%BD%95.png)

### 六、观察失真波形

实验示波器测量图示如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E5%85%AD%E3%80%81%E5%A4%B1%E7%9C%9F%E6%88%AA%E6%AD%A2.PNG)

$$
截止失真
$$

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E5%85%AD%E3%80%81%E5%A4%B1%E7%9C%9F%E9%A5%B1%E5%92%8C.PNG)

$$
饱和失真
$$

实验记录表3.3.5如下

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E8%A1%A83.3.5_%E8%AE%B0%E5%BD%95.png)

## 实验小结

这次实验增强了我对MOSFET的理解，亲身学习并调试出了两种失真波形，复习并更好的理解了模电的知识,实验过程中发现理解实验原理，掌握MOS管的工作原理和相关计算方法以及相关仪器的使用方法至关重要。

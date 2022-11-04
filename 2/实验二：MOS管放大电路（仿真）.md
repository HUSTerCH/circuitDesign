# 实验二：MOS管放大电路（仿真）

专业班级：**通信2101**

姓名：        **罗畅**

学号：        **U202113940**

## 实验名称

MOS管放大电路仿真实现

## 实验目的

- 学习掌握PSpice软件的使用

- 学习共源放大电路工作原理

- 实现MOSFET共源放大电路的仿真实现

## 实验元器件

***电脑 PSpice软件***

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
r_ds=\frac{∂v_{DS}}{∂i_D}|_{V_{GS}}
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

- 按照图3.3.6连接实验电路图

- 测试静态工作点Q($I_{DQ}$,$V_{GSQ}$,$V_{DSQ}$)

- 记录输入、输出电压波形，并计算电压增益$A_v$

- 记录幅频响应曲线db(V($V_o$)/V($V_s$:+))，测量中频增益、上限频率$f_H$和下限频率$f_L$

- 记录相频响应曲线$V_p$($V_o$)-$V_p$($V_s$:+) 或 P(V($V_o$)/V($V_s$:+))

- 记录输入电阻的频率响应:$R_i$--V($V_i$)/I($V_S$)

- 记录输出电阻的频率响应:$R_o$--V($V_i$)/I($V_S$)

- 观察记录非线性失真现象

***注意：
1.设置静态工作点时，调整电位器$R_P$，使$V_D$为5~6V。（漏极对地电压）
2.仿真时输出端必须接负载，否则会报错。（可以将阻值设为很大的值，如1t（$10^{12}$Ω）来仿真开路情况。）***

## 实验记录

### 电路图&静态工作点

仿真分析 静态工作点 电路如图所示  

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BB%BF%E7%9C%9F_%E7%94%B5%E8%B7%AF_%E9%9D%99%E6%80%81%E5%B7%A5%E4%BD%9C%E7%82%B9.png)

数据如下：

<img src="https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BB%BF%E7%9C%9F_%E6%95%B0%E6%8D%AE_%E9%9D%99%E6%80%81%E5%B7%A5%E4%BD%9C%E7%82%B9.png" title="" alt="" width="225">

电路工作在饱和区，符合条件

静态工作点：

$V_{GSQ}$=1.92V

$V_{DSQ}$=4.53V

$I_{DQ}$=1.21mA

### 输入输出波形/电压增益

仿真实验 输入波形如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BB%BF%E7%9C%9F_%E7%BB%98%E5%9B%BE_vi.png)

输出波形如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BB%BF%E7%9C%9F_%E7%BB%98%E5%9B%BE_%E5%BC%80%E8%B7%AF_vo.png)

**电压增益为$A_v$=1159.4/29.995=38.67**

### 幅频响应曲线

仿真实验 幅频响应曲线如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BB%BF%E7%9C%9F_%E7%BB%98%E5%9B%BE_%E5%B9%85%E9%A2%91%E5%93%8D%E5%BA%94%E6%9B%B2%E7%BA%BF.png)

则可以得到：

- 中频增益 $A_v$=31.804dB=38.922>10 符合要求

- 上限频率 $f_H$=1.44MHz>100kHz 符合要求

- 下限频率 $f_L$=29.139Hz<100Hz 符合要求

### 相频响应曲线

仿真实验 相频响应曲线

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BB%BF%E7%9C%9F_%E7%BB%98%E5%9B%BE_%E7%9B%B8%E9%A2%91%E5%93%8D%E5%BA%94%E6%9B%B2%E7%BA%BF.png)

### 输入电阻响应曲线

仿真实验 输入电阻响应曲线

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BB%BF%E7%9C%9F_%E7%BB%98%E5%9B%BE_%E8%BE%93%E5%85%A5%E7%94%B5%E9%98%BB%E9%A2%91%E7%8E%87%E5%93%8D%E5%BA%94.png)

可得$R_i$=73.357kΩ>51kΩ 符合要求

### 输出电阻响应曲线

仿真实验 输出电阻频率响应曲线

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BB%BF%E7%9C%9F_%E7%BB%98%E5%9B%BE_%E8%BE%93%E5%87%BA%E7%94%B5%E9%98%BB%E9%A2%91%E7%8E%87%E5%93%8D%E5%BA%94.png)

可得$R_o$=5.0997kΩ<5.1kΩ 符合要求

### 非线性失真现象

仿真实验 非线性失真 截止失真现象

![Base/仿真_绘图_截止失真.png at master · HUSTerCH/Base · GitHub](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BB%BF%E7%9C%9F_%E7%BB%98%E5%9B%BE_%E6%88%AA%E6%AD%A2%E5%A4%B1%E7%9C%9F.png)

仿真实验 非线性失真 饱和失真现象

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/ex2/%E4%BB%BF%E7%9C%9F_%E7%BB%98%E5%9B%BE_%E9%A5%B1%E5%92%8C%E5%A4%B1%E7%9C%9F.png)

## 实验小结

- 本次实验经历各种各样的波折，如调不出失真曲线、误用示波器测量静态工作点等，在经过不断的调试和重做之后，最终顺利完成了实验。

- 实验过程中发现理解实验原理，掌握MOS管的工作原理和相关计算方法以及相关仪器的使用方法至关重要。

- 此外，在本次实验中还掌握了仿真软件PSPice的基本用法，学会了用其进行一些基本实验的模拟操作，感觉在日后的实验中会有助力。

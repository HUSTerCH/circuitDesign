# 实验三：并行IO接口设计

专业班级：**通信2101班**
姓名：        **罗畅**
学号：        **U202113940**

## 实验名称

并行IO接口设计

## 实验目的

- 掌握GPIO IP核的工作原理和使用方法
- 掌握中断控制方式的IO接口设计原理
- 掌握中断程序设计方法
- 掌握IO接口程序控制方法

## 实验仪器

***Vivado 2018.3、Vivado SDK、Visual Studio Code-Insiders***

## 实验任务

- 所有实验任务要求分别采用程序控制方式、中断方式,中断方式时, GPIO输入、延时都采用中断实现。
  - 嵌入式计算机系统将独立按键以及独立开关作为输入设备, LED灯、七段数码管作为输出设备。LED灯实时显示独立开光对应位状态,同时8个七段数码管实时显示最近按下的独立按键位置编码字符( C,U,L,D,R)。
  - 程序控制方式提示:程序以七段数码管动态显示控制循环为主体,在循环体内的延时函数内读取开关值更新LED、读取按键值更新段码。
  - 扩展：实现向左移位显示

## 实验原理

### 硬件电路框图

![Base/硬件电路框图1.png at master · HUSTerCH/Base · GitHub](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/%E5%BE%AE%E6%9C%BA%E5%8E%9F%E7%90%86/ex3/%E7%A1%AC%E4%BB%B6%E7%94%B5%E8%B7%AF%E6%A1%86%E5%9B%BE1.png)

![Base/硬件电路框图2.png at master · HUSTerCH/Base · GitHub](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/%E5%BE%AE%E6%9C%BA%E5%8E%9F%E7%90%86/ex3/%E7%A1%AC%E4%BB%B6%E7%94%B5%E8%B7%AF%E6%A1%86%E5%9B%BE2.png)

根据硬件电框图搭建的硬件平台整体框图如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/%E5%BE%AE%E6%9C%BA%E5%8E%9F%E7%90%86/ex3/%E7%A1%AC%E4%BB%B6%E5%B9%B3%E5%8F%B0%E6%95%B4%E4%BD%93%E6%A1%86%E5%9B%BE.png)

### 软件流程图

![Base/软件流程图.png at master · HUSTerCH/Base · GitHub](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/%E5%BE%AE%E6%9C%BA%E5%8E%9F%E7%90%86/ex3/%E8%BD%AF%E4%BB%B6%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

## 实验源码

### 程序控制方式

```c
/*
 * ProgramControl.c
 *
 *  Created on: Apr 11, 2023
 *      Author: Luo Chang
 */

#include <stdio.h>
#include "xil_printf.h"
#include "xil_io.h"
#include "xgpio_l.h"

// show on seg
void display(char segcode[], short tmp[], short position, int k) {
    // k is shift bit
    for (int i = 0; i < 8; i++) {
        Xil_Out8(XPAR_AXI_GPIO_1_BASEADDR + XGPIO_DATA2_OFFSET, segcode[tmp[(i + k) % 8]]);  // display signal at each bit
        Xil_Out8(XPAR_AXI_GPIO_1_BASEADDR + XGPIO_DATA_OFFSET, position);  // position change
        for (int j = 0; j < 10000; j++);  // light on 8 segs
        position = position >> 1;
    }
}

int main() {
    unsigned short lastSwitchState, currentSwitchState;  //switch state at last cycle and this cycle
    unsigned short led;  // output to LED
    Xil_Out16(XPAR_AXI_GPIO_0_BASEADDR + XGPIO_TRI_OFFSET, 0xffff);  // set switch as input
    Xil_Out16(XPAR_AXI_GPIO_0_BASEADDR + XGPIO_TRI2_OFFSET, 0x0);  // set LED as output

    unsigned short currentBtn, lastBtn, realBtn;
    char segcode[6] = {0xc6, 0xc1, 0xc7, 0x88, 0xc0, 0xff};  // seg signal as {C, U, L, R, D, null}
    short tmp[8] = {5, 5, 5, 5, 5, 5, 5, 5};
    int k = 0;  // shift bit
    short position = 0xff7f;
    Xil_Out8(XPAR_AXI_GPIO_1_BASEADDR + XGPIO_TRI_OFFSET, 0x0);
    Xil_Out8(XPAR_AXI_GPIO_1_BASEADDR + XGPIO_TRI2_OFFSET, 0x0);
    Xil_Out16(XPAR_AXI_GPIO_2_BASEADDR + XGPIO_TRI_OFFSET, 0x1f);
    while (1) {
        lastSwitchState = currentSwitchState;
        currentSwitchState = Xil_In16(XPAR_AXI_GPIO_0_BASEADDR + XGPIO_DATA_OFFSET) & 0xffff;
        if (lastSwitchState != currentSwitchState) {  // change switch state when switch toggle
            led = currentSwitchState;
        }
        Xil_Out16(XPAR_AXI_GPIO_0_BASEADDR + XGPIO_DATA2_OFFSET, led);  // output to LED

        display(segcode, tmp, position, k);  // show on seg
        position = 0xff7f;
        currentBtn = Xil_In8(XPAR_AXI_GPIO_2_BASEADDR + XGPIO_DATA_OFFSET) & 0x1f;
        if (currentBtn) {  // btn onClick
            while (currentBtn) {  // btn not spring up
                display(segcode, tmp, position, k);
                currentBtn = (Xil_In8(XPAR_AXI_GPIO_2_BASEADDR + XGPIO_DATA_OFFSET) & 0x1f);
                realBtn = (currentBtn ^ lastBtn) & lastBtn;  // get unpressed btn
                lastBtn = currentBtn;
                if (realBtn) {
                    break;
                }
            }
            // judge which btn was pressed
            switch (realBtn) {
            case 0x01: 
                tmp[k] = 0;
                k = (k + 1) % 8;
                break;

            case 0x02: 
                tmp[k] = 1;
                k = (k + 1) % 8;
                break;

            case 0x04: 
                tmp[k] = 2;
                k = (k + 1) % 8;
                break;

            case 0x08: 
                tmp[k] = 3;
                k = (k + 1) % 8;
                break;

            case 0x10: 
                tmp[k] = 4;
                k = (k + 1) % 8;
                break;
            }
        }
    }
    return 0;
}
```

### 中断控制方式

```c
/*
 * InterruptControl.c
 *
 *  Created on: Apr 11, 2023
 *      Author: Luo Chang
 */
#include "xil_io.h"
#include "stdio.h"
#include "xgpio_l.h"
#include "xintc_l.h"
#include "xtmrctr_l.h"
#define RESET_VALUE 100000

void segTimerCounterHandler();
void btnHandler();
void switchHandler();
void My_ISR() __attribute__((interrupt_handler));
int i = 0;

unsigned short currentBtn, lastBtn, realBtn;
char segcode[6] = {0xc6, 0xc1, 0xc7, 0x88, 0xc0, 0xff};
short tmp[8] = {5, 5, 5, 5, 5, 5, 5, 5};
int k = 0;
short pos = 0xff7f;

int main() {
    Xil_Out32(XPAR_AXI_GPIO_0_BASEADDR + XGPIO_TRI2_OFFSET, 0x0);  // set LED as output
    Xil_Out32(XPAR_AXI_GPIO_0_BASEADDR + XGPIO_TRI_OFFSET, 0xffff);  // Switch as output

    Xil_Out32(XPAR_AXI_GPIO_0_BASEADDR + XGPIO_IER_OFFSET, XGPIO_IR_CH1_MASK);
    Xil_Out32(XPAR_AXI_GPIO_0_BASEADDR + XGPIO_GIE_OFFSET, XGPIO_GIE_GINTR_ENABLE_MASK);

    Xil_Out32(XPAR_AXI_INTC_0_BASEADDR + XIN_IER_OFFSET, XPAR_AXI_GPIO_0_IP2INTC_IRPT_MASK);  // interrupt channel
    Xil_Out32(XPAR_AXI_INTC_0_BASEADDR + XIN_MER_OFFSET, XIN_INT_MASTER_ENABLE_MASK | XIN_INT_HARDWARE_ENABLE_MASK);

    Xil_Out32(XPAR_AXI_GPIO_1_BASEADDR + XGPIO_TRI_OFFSET, 0x0);  // set seg show signal as output
    Xil_Out32(XPAR_AXI_GPIO_1_BASEADDR + XGPIO_TRI2_OFFSET, 0x0);  // set seg pos signal as output
    Xil_Out32(XPAR_AXI_GPIO_2_BASEADDR + XGPIO_TRI_OFFSET, 0x1f);  // btn input

    Xil_Out32(XPAR_AXI_GPIO_2_BASEADDR + XGPIO_IER_OFFSET, XGPIO_IR_CH1_MASK);  // allow channel 1 interrupt
    Xil_Out32(XPAR_AXI_GPIO_2_BASEADDR + XGPIO_GIE_OFFSET, XGPIO_GIE_GINTR_ENABLE_MASK);  // allow GPIO interrupt output

    Xil_Out32(XPAR_AXI_TIMER_0_BASEADDR + XTC_TCSR_OFFSET,
              Xil_In32(XPAR_AXI_TIMER_0_BASEADDR + XTC_TCSR_OFFSET) & ~XTC_CSR_ENABLE_TMR_MASK);  // write TCSR, stop counter

    Xil_Out32(XPAR_AXI_TIMER_0_BASEADDR + XTC_TLR_OFFSET, RESET_VALUE);  // write TLR, preset counter value

    Xil_Out32(XPAR_AXI_TIMER_0_BASEADDR + XTC_TCSR_OFFSET,
              Xil_In32(XPAR_AXI_TIMER_0_BASEADDR + XTC_TCSR_OFFSET) | XTC_CSR_LOAD_MASK);  // get counter initial value

    Xil_Out32(XPAR_AXI_TIMER_0_BASEADDR + XTC_TCSR_OFFSET,
              (Xil_In32(XPAR_AXI_TIMER_0_BASEADDR + XTC_TCSR_OFFSET) & ~XTC_CSR_LOAD_MASK)
              | XTC_CSR_ENABLE_TMR_MASK | XTC_CSR_AUTO_RELOAD_MASK | XTC_CSR_ENABLE_INT_MASK | XTC_CSR_DOWN_COUNT_MASK);

    Xil_Out32(XPAR_AXI_INTC_0_BASEADDR + XIN_IER_OFFSET, XPAR_AXI_GPIO_2_IP2INTC_IRPT_MASK | XPAR_AXI_TIMER_0_INTERRUPT_MASK);  //Enable the interrupt controller

    Xil_Out32(XPAR_AXI_INTC_0_BASEADDR + XIN_MER_OFFSET, XIN_INT_MASTER_ENABLE_MASK | XIN_INT_HARDWARE_ENABLE_MASK);
    microblaze_enable_interrupts();  // allow microbalze enable interrupt
    return 0;
}

void My_ISR() {
    int status;
    status = Xil_In32(XPAR_AXI_INTC_0_BASEADDR + XIN_ISR_OFFSET);
    if((status & XPAR_AXI_GPIO_0_IP2INTC_IRPT_MASK) == XPAR_AXI_GPIO_0_IP2INTC_IRPT_MASK) {
            switchHandler();
        }
    Xil_Out32(XPAR_AXI_INTC_0_BASEADDR + XIN_IAR_OFFSET, status);
    if ((status & XPAR_AXI_TIMER_0_INTERRUPT_MASK) == XPAR_AXI_TIMER_0_INTERRUPT_MASK) {
        segTimerCounterHandler();
    }
    if ((status & XPAR_AXI_GPIO_2_IP2INTC_IRPT_MASK) == XPAR_AXI_GPIO_2_IP2INTC_IRPT_MASK) {
        btnHandler();
    }
    Xil_Out32(XPAR_AXI_INTC_0_BASEADDR + XIN_IAR_OFFSET, status);
}

void segTimerCounterHandler() {
    Xil_Out8(XPAR_AXI_GPIO_1_BASEADDR + XGPIO_DATA2_OFFSET, segcode[tmp[(i + k) % 8]]);
    Xil_Out8(XPAR_AXI_GPIO_1_BASEADDR + XGPIO_DATA_OFFSET, pos);
    pos = pos >> 1;
    i++;
    if (i == 8) {
        i = 0;
        pos = 0xff7f;
    }
    Xil_Out32(XPAR_AXI_TIMER_0_BASEADDR + XTC_TCSR_OFFSET, Xil_In32(XPAR_AXI_TIMER_0_BASEADDR + XTC_TCSR_OFFSET));  // clear interrupt
}

void btnHandler() {
    int btncode;
    btncode = Xil_In32(XPAR_AXI_GPIO_2_BASEADDR + XGPIO_DATA_OFFSET);
    switch (btncode) {
    case 0x01: 
        tmp[k] = 0;
        k = (k + 1) % 8;
        break;
    case 0x02: 
        tmp[k] = 1;
        k = (k + 1) % 8;
        break;
    case 0x04: 
        tmp[k] = 2;
        k = (k + 1) % 8;
        break;
    case 0x08: 
        tmp[k] = 3;
        k = (k + 1) % 8;
        break;
    case 0x10: 
        tmp[k] = 4;
        k = (k + 1) % 8;
        break;

    }
    Xil_Out32(XPAR_AXI_GPIO_2_BASEADDR + XGPIO_ISR_OFFSET, Xil_In32(XPAR_AXI_GPIO_2_BASEADDR + XGPIO_ISR_OFFSET));
}

void switchHandler() {
    Xil_Out32(XPAR_AXI_GPIO_0_BASEADDR + XGPIO_DATA2_OFFSET, Xil_In32(XPAR_AXI_GPIO_0_BASEADDR + XGPIO_DATA_OFFSET));
    Xil_Out32(XPAR_AXI_GPIO_0_BASEADDR + XGPIO_ISR_OFFSET, Xil_In32(XPAR_AXI_GPIO_0_BASEADDR + XGPIO_ISR_OFFSET));
}
```

## 实验结果

实验效果如下：

![Base/实验效果.png at master · HUSTerCH/Base · GitHub](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/%E5%BE%AE%E6%9C%BA%E5%8E%9F%E7%90%86/ex3/%E5%AE%9E%E9%AA%8C%E6%95%88%E6%9E%9C.png)

## 实验小结

本次实验让我对Vivado的使用更加熟练了，还让我熟悉了Vivado SDK的使用，虽然硬件平台的搭建困难重重，但是最终还是成功跑通了。除此之外，还让我对IO中断控制的知识有了更深层对的理解。

还是那句话：工欲善其事，必先利其器！

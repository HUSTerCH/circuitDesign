# Verilog HDL程序设计与仿真 实验报告

专业班级：**通信2101班**

姓名：        **罗畅**

学号：        **U202113940**

## 各芯片代码与测试用例

### CD4532

CD4532.v：

```verilog
/*
@author Luo Chang
@UID U202113940
@date 2022/11/23
*/

module CD4532(EI,I,Y,GS,EO);
/*
输入端口：EI使能信号，I为输入的数。
输出端口：Y为对应的二进制数。
GS,EO用于多片的级联：
当输入信号存在有效信号，GS=1,EO=0；
当输入信号全部无效时，GS=0,EO=1；??????;
*/
input EI;
input [7:0] I;
output reg [2:0] Y;
output reg GS,EO;

always @(*) 
    begin
        if (EI==0) 
            begin
                Y[2:0] = 3'b000;

                GS = 0;
                EO = 0;
            end
        else
            begin
                GS = 1;
                EO = 0;
                casex(I[7:0])
                    8'b1xxx_xxxx : Y[2:0] = 3'b111;
                    8'b01xx_xxxx : Y[2:0] = 3'b110;
                    8'b001x_xxxx : Y[2:0] = 3'b101;
                    8'b0001_xxxx : Y[2:0] = 3'b100;
                    8'b0000_1xxx : Y[2:0] = 3'b011;
                    8'b0000_01xx : Y[2:0] = 3'b010;
                    8'b0000_001x : Y[2:0] = 3'b001;
                    8'b0000_0001 : Y[2:0] = 3'b000;
                    default 
                        begin
                            GS = 0;
                            EO = 1;
                            Y[2:0] = 3'b000;
                        end
                endcase
            end
    end
endmodule
```

Cd4532_tb.v:

```verilog
/*
@author Luo Chang
@UID U202113940
@date 2022/11/23
*/

`timescale 1ns/1ns
module Cd4532_tb;
reg [7:0] I;
reg EI;
wire [2:0] Y;
wire GS,EO;

CD4532 U0(EI,I,Y,GS,EO);

initial $monitor($time,":tI = %b,EI = %b,EO = %b,GS = 
%b,Y = %b\n",I,EI,EO,GS,Y);

initial
    begin
//使能信号无效
        EI = 0;
        I=8'b1111_1111;      
        #10
        EI = 0; 
        I=8'b0111_1111;
        #10
//使能信号有效
        EI = 1;
        I=8'b1111_1111;
        #10
        EI = 1;
        I=8'b0111_1111;
        #10
        EI = 1;
        I=8'b0001_1111;
//停止仿真
        $stop;            
    end
endmodule
```

测试波形如下：

<img src="https://github.com/HUSTerCH/Base/raw/master/circuitDesign/verilog%20HDL/CD4532%E6%B5%8B%E8%AF%95%E6%B3%A2%E5%BD%A2.png" title="" alt="" width="500">

### 74X138

74X138.v

```verilog
/*
@author Luo Chang
@UID U202113940
@date 2022/11/23
*/

module _74X138(E1,E2,E3,A,Y);
input E1,E2,E3;
input [2:0] A;
output reg [7:0] Y;

always @(*)
    begin
//The input signal of the enable terminal is valid
        if (E1 == 0 && E2 == 0 && E3 == 1)
            begin
                casex(A[2:0])
                    3'b000 : Y = 8'b0111_1111;
                    3'b001 : Y = 8'b1011_1111;
                    3'b010 : Y = 8'b1101_1111;
                    3'b011 : Y = 8'b1110_1111;
                    3'b100 : Y = 8'b1111_0111;
                    3'b101 : Y = 8'b1111_1011;
                    3'b110 : Y = 8'b1111_1101;
                    3'b111 : Y = 8'b1111_1110;
                endcase
            end
//The input signal of the enable terminal is invalid
        else
            Y = 8'b1111_1111;
    end
endmodule
```

74X138_tb.v:

```verilog
/*
@author Luo Chang
@UID U202113940
@date 2022/11/23
*/

`timescale 1ns/1ns
module _74X138_tb;
reg [2:0] A;
reg E1,E2,E3;
wire [7:0] Y;

_74X138 U0(E1,E2,E3,A,Y);

initial $monitor($time,":A = %b,E1 = %b,E2 = %b,E3 = %b,Y = %b\n",A,E1,E2,E3,Y);

initial 
    begin
//The input signal of the enable terminal is invalid
        E1 = 0;
        E2 = 1;
        E3 = 0;
        A=3'b010;      
        #10
        E1 = 1;        
        E2 = 0;
        A=3'b110;
        #10
//The input signal of the enable terminal is valid
        E1 = 0;
        E2 = 0;
        E3 = 1;
        A=3'b101;
        #10
        E1 = 0;
        E2 = 0;
        E3 = 1;
        A=3'b000;
        #10
        E1 = 0;
        E2 = 0;
        E3 = 1;
        A=3'b111;   
        $stop;        
    end
endmodule
```

测试波形如下：

![](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/verilog%20HDL/74X138%E6%B5%8B%E8%AF%95%E6%B3%A2%E5%BD%A2.png)

### 74HC4511

_74HC4511.v

```verilog
/*
@author Luo Chang
@UID U202113940
@date 2022/11/24
*/

// 74HCT4511 is a BCD to 7-segment latch/decoder/driver

module _74HC4511(LE,BL,LT,D,Q);
input LE,BL,LT;
input [3:0] D;
output reg [6:0] Q;

always @(*)
    begin
        if (LE == 0 && !BL == 1 && !LT == 1)
            begin
                casex(D[3:0])
                    4'b0000 : Q = 7'b1111_110;
                    4'b0001 : Q = 7'b0110_000;
                    4'b0010 : Q = 7'b1101_101;
                    4'b0011 : Q = 7'b1111_001;
                    4'b0100 : Q = 7'b0110_011;
                    4'b0101 : Q = 7'b1011_011;
                    4'b0110 : Q = 7'b0011_111;
                    4'b0111 : Q = 7'b1110_000;
                    4'b1000 : Q = 7'b1111_111;
                    4'b1001 : Q = 7'b1111_011;
                    default : Q = 7'b0000_000;
                endcase
            end
        else if (!LT == 0)
            Q = 7'b1111_111;
        else if (!BL == 0 && !LT == 1)
            Q = 7'b0000_000;
        else
            Q <= Q;
    end
endmodule
```

_74HC4511_tb.v

```verilog
/*
@author Luo Chang
@UID U202113940
@date 2022/11/24
*/

`timescale 1ns/1ns

module _74HC4511_tb;

reg [3:0] D;
reg LE,BL,LT;
wire [6:0] Q;

_74HC4511 U0(LE,BL,LT,D,Q);

initial $monitor($time,"D = %b,Q = %b,LE = %b,BL = %b,LT = %b",D,Q,LE,BL,LT);

initial
    begin
// 74HC4511 on test
        LE = 1;
        BL = 0;
        LT = 1;
        D = 4'b1101;
        #10
// 74HC4511 turn off
        LE = 0;
        BL = 1;
        LT = 0;
        D = 4'b1011;
        #10
// 74HC4511 turn on
        LE = 0;
        BL = 0;
        LT = 0;
        D = 4'b0000;
        #10
        D = 4'b1001;
        #10
        D = 4'b0110;
        #10
        D = 4'b1010;
        #10
// 74HC4511 latched
        LE = 1;
        BL = 0;
        LT = 0;
        #15
        $stop;
    end
endmodule
```

测试波形如下：

<img src="https://github.com/HUSTerCH/Base/raw/master/circuitDesign/verilog%20HDL/74HC4511%E6%B5%8B%E8%AF%95%E6%B3%A2%E5%BD%A2.png" title="" alt="" width="500">

### 74HC151

_74HC151.v

```verilog
/*
@author Luo Chang
@UID U202113940
@date 2022/11/25
*/

module _74HC151(D,S,E,Y,Y_); 
input E;
input [7:0] D; 
input [2:0] S; 
output reg Y,Y_;
always @(*)
    begin 
        if (! E ==1)
            begin 
                Y = 0;
                Y_ = 1;
            end 
        else 
             begin 
                 casex (S[2:0])
                    3'b000 : Y = D[0];
                    3'b001 : Y = D[1];
                    3'b010 : Y = D[2];
                    3'b011 : Y = D[3];
                    3'b100 : Y = D[4];
                    3'b101 : Y = D[5];
                    3'b110 : Y = D[6];
                    3'b111 : Y = D[7];
                endcase
                Y_ = ~Y;
            end
    end
endmodule
```

__74HC151_tb.v

```verilog
/*
@author Luo Chang
@UID U202113940
@date 2022/11/24
*/

`timescale 1ns/1ns

module _74HC151_tb;

reg [2:0] S;
reg E;
reg [7:0] D;

wire Y,Y_;

_74HC151 U0(D,S,E,Y,Y_);

initial $monitor($time,"E = %b,S = %b,D = %b,Y = %b,Y_ = %b",E,S,D,Y,Y_);

initial
    begin
// enabled terminal invalid
        E = 0;
        D = 8'b1101_1110;
        S = 3'b101;
        #10
        D = 8'b1001_0110;
        S = 3'b111;
        #10
// enabled terminal valid
        E = 1;
        D = 8'b0000_0001;
        S = 3'b000;
        #10
        D = 8'b1110_1111;
        S = 3'b100;
        #10
        D = 8'b0111_1111;
        S = 3'b111;
        #10
        D = 8'b0000_0010;
        S = 3'b001;
        #10
        $stop;
    end
endmodule
```

测试波形：

![Base/74HC151测试波形.png at master · HUSTerCH/Base · GitHub](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/verilog%20HDL/74HC151%E6%B5%8B%E8%AF%95%E6%B3%A2%E5%BD%A2.png)

### 74HC85

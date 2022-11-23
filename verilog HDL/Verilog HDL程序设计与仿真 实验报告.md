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



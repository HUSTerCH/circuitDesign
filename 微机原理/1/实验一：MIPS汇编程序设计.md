# 实验一：MIPS汇编程序设计

专业班级：**通信2101班**

姓名：        **罗畅**

学号：        **U202113940**

## 实验名称

MIPS汇编程序设计

## 实验目的：

1. 熟悉常见的MIPS汇编指令
2. 掌握MIPS汇编程序设计
3. 了解MIPS汇编语言与机器语言之间的对应关系
4. 了解C语言语句与汇编指令之间的关系
5. 掌握MARS的调试技术
6. 掌握程序的内存映像

## 实验仪器

***Mars MIPS汇编编译器***

## 实验任务

- 在数据段定义两个int型变量a, b;
- 在数据段定义一个int型数组c[40]，不初始化
- 通过系统调用功能从键盘输入a, b的值（不大于20）
- 采用MIPS汇编指令实现c[a+b] = a*b
- 通过系统调用功能分别显示c[a+b]所在的存储地址和值
- 指出程序运行后a, b, c[a+b]所在的数据段储存位置以及取值，验证程序功能的正确性

## 实验源代码

```nasm
.data
a: .word 0
b: .word 0
c: .word 0:20

inputAStr: .asciiz "Please input the value of a:"
inputBStr: .asciiz "Please input the value of b:"

outputAddr: .asciiz "The data address is:"
outputData: .asciiz "\nThe data is:"

.text
.globl main

main:
    la $a0, inputAStr #print "Please input the value of a:"
    li $v0, 4
    syscall

    li $v0, 5 #read a's value from terminal
    syscall

    add $t0, $v0, $zero #save a's value to $t0

    la $a0, a
    sw $t0, 0($a0) #save a's value to memory


    la $a0, inputBStr #print "Please input the value of b:"
    li $v0, 4
    syscall

    li $v0, 5 #read b's value from terminal
    syscall

    add $t1, $v0, $zero #save b's value to $t1

    la $a0, b #save b's value to memory
    sw $t1, 0($a0)

    add $t2, $t0, $t1 #calculate the array subscript (a+b)
    mul $t3, $t0, $t1 #calculate the data (a*b)
    addi $s0,$zero,4
    mul $t4,$s0,$t2 #offset correction

    la $a0, c #get c's first address
    add $a0, $a0, $t4 #get right addr

    sw $t3, 0($a0) #save data to memory

    add $s0, $a0,$zero #save the address to $to

    la $a0,outputAddr #print "The data address is:"
    li $v0, 4
    syscall

    add $a0, $s0,$zero #print the data address
    li $v0, 1
    syscall

    la $a0,outputData #print "\nThe data is:"
    li $v0,4
    syscall

    add $a0,$t3,$zero #print the data value 
    li $v0,1
    syscall

    li $v0,10 #exit
    syscall
```

## 实验结果

### 程序代码段映像

![Base/代码段映像.png at master · HUSTerCH/Base · GitHub](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/%E5%BE%AE%E6%9C%BA%E5%8E%9F%E7%90%86/ex1/%E4%BB%A3%E7%A0%81%E6%AE%B5%E6%98%A0%E5%83%8F.png)

### 输入输出端口测试

![Base/输入输出端口测试.png at master · HUSTerCH/Base · GitHub](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/%E5%BE%AE%E6%9C%BA%E5%8E%9F%E7%90%86/ex1/%E8%BE%93%E5%85%A5%E8%BE%93%E5%87%BA%E7%AB%AF%E5%8F%A3%E6%B5%8B%E8%AF%95.png)

### 程序数据段映像

![Base/数据段映像1.png at master · HUSTerCH/Base · GitHub](https://github.com/HUSTerCH/Base/raw/master/circuitDesign/%E5%BE%AE%E6%9C%BA%E5%8E%9F%E7%90%86/ex1/%E6%95%B0%E6%8D%AE%E6%AE%B5%E6%98%A0%E5%83%8F1.png)

<img title="" src="https://github.com/HUSTerCH/Base/raw/master/circuitDesign/%E5%BE%AE%E6%9C%BA%E5%8E%9F%E7%90%86/ex1/%E6%95%B0%E6%8D%AE%E6%AE%B5%E6%98%A0%E5%83%8F2.png" alt="Base/数据段映像2.png at master · HUSTerCH/Base · GitHub" width="274">

### 结果分析

从I/O端口输入的数据正确的运算并存入了a、b、c[a+b]对应的内存映像中，实验正确

## 实验小结

   本次实验我使用了Mars软件进行汇编语言的练习，学会了使用syscall来进行数据的输入和输出，最后实验结果正确，收获很大！

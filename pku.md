## 一、实验内容
在编译原理课程实践中, 大家需要开发一个将 SysY 语言编译到 RISC-V 汇编的编译器. 当然, 这一步跨得有些太大了, 所以我们把它分成了两个步骤:
```
将 SysY 语言编译到 Koopa IR.
将 Koopa IR 生成到 RISC-V 汇编.
```
Koopa IR 是我们为编译原理课程实践设计的一种中间表示 (IR, intermediate representation). 相比于之前编译实践课采用的 IR, Koopa IR 在设计上更为合理. 它在形式上类似于 LLVM IR, 但简化了相当多的内容, 只专注于实践需要的部分.

## 二、教学资源
#### 1.KoopaIR生成器
https://github.com/pku-minic/koopa
#### 2.oj平台-基于希冀
https://course.educg.net/main.jsp
#### 3.Linux使用手册
https://nju-projectn.github.io/ics-pa-gitbook/ics2021/linux.html
#### 4.Git使用手册
https://nju-projectn.github.io/ics-pa-gitbook/ics2021/git.html
#### 5.GDB/LLDB使用手册
https://darkdust.net/files/GDB%20Cheat%20Sheet.pdf
#### 6.Makefile使用手册
https://darkdust.net/files/GDB%20Cheat%20Sheet.pdf

https://pku-minic.github.io/online-doc/#/preface/prerequisites
#### 7.Docker使用手册及环境
https://pku-minic.github.io/online-doc/#/preface/prerequisites
```
docker pull maxxing/compiler-dev
```
#### 8.项目模板
https://github.com/pku-minic/sysy-make-template

https://github.com/pku-minic/sysy-cmake-template

https://github.com/pku-minic/sysy-cargo-template
#### 9.实例程序-基于Rust，C开发中
https://github.com/pku-minic/kira-rs


## 三、实验内容
采取增量开发的方式完成实验框架
#### 1.main函数
完成一个程序，对只有注释和return语句的main函数完成从源程序，经过词法语法分析输出AST，经过语义分析输出符号表并进行类型检查，经过IR生成生成Kooper
#### 2.代码生成
将第一个实验输出的IR进行分析，并完成目标代码MIPS指令的生成
#### 3.表达式
增量开发一元表达式、算术表达式、比较和逻辑表达式
#### 4.常量变量
增量开发常量声明、变量的声明和赋值
#### 5.语句块和作用域
增量开发{}完成语句块的划分
#### 6.if语句
增量开发分支语句
#### 7.while语句
增量开发循环语句
#### 8.函数和全局变量
增量开发函数定义和调用、知识SysY库函数、全局变量和调用
#### 9.数组
增量开发数组、多维数组的数组结构、数组作为参数传递
#### 10.提升和优化
增量开发不同的寄存器分配算法、死代码冗余load机器无关相关等优化策略、实现SSA形式

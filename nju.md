## 一、概述
编译原理的教学目标是使得学生掌握如下知识：和编译器设计相关的形式语言理论基础；编译程序的体系结构，编译器的各个组成部分的原理和技术，包括词法分析、语法分析、语义分析和目标代码生成、代码优化，以及设计环境与程序错误的检查和校正；初步了解一些编译器生成工具的基础理论和应用。另外，该课程将通过上机实验使得学生初步了解一个设计编译器的过程

## 二、教学资源
#### 1.教学课件
https://cs.nju.edu.cn/_upload/tpl/07/94/1940/template1940/courses/compiler-2023/lectures/1-intro.pdf
#### 2.习题资料
https://cs.nju.edu.cn/_upload/tpl/07/94/1940/template1940/courses/compiler-2023/exercises/1000.pdf
#### 3.项目框架
https://cs.nju.edu.cn/_upload/tpl/07/94/1940/template1940/courses/compiler-2023/projects/Lab.zip
#### 4.实验要求及指导
https://cs.nju.edu.cn/_upload/tpl/07/94/1940/template1940/courses/compiler-2023/projects/Project_1.pdf
#### 5.测试用例
https://cs.nju.edu.cn/_upload/tpl/07/94/1940/template1940/courses/compiler-2023/projects/Tests_1_Normal.zip
#### 6.虚拟机测试中间代码
https://cs.nju.edu.cn/_upload/tpl/07/94/1940/template1940/courses/compiler-2023/projects/irsim.zip
#### 7.oj-SDUOJ
https://compiler.pascal-lab.net/contest

## 三、实验内容
#### 1.词法分析和语法分析
使用GNU Flex和GNU Bison，联合C语言完成。要求在Ubuntu20.04、GCC7.5.0、GNU Flex2.6.4、GNU Bison3.5.1的环境依赖下完成测试，测试集给出一部分，隐藏测试机在oj平台
#### 2.语义分析
对C源代码进行语义分析和类型检查，并打印分析结果。要求词法、语法、语义分析部分全部都要手搓。设定了19个需要识别的错误类型。和实验一同环境下完成。测试机同样在oj部分隐藏
#### 3.中间代码生成
将中间代码输出成线性结构，从而可以使用我们提供的虚拟机小程序（附录B）来测试中间代码的运行结果。测试集同样在oj平台部分隐藏
#### 4.目标代码生成
将C−−源代码翻译为MIPS32指令序列（可以包含伪指令），并在SPIM Simulator上运行。
#### 5.中间代码优化
在本次实验中，对于给定的程序，在实验三生成的中间代码IR基础上，编译器对IR进行中间代码优化。实验假设与实验三完全相同，输入的源程序不包含词法、语法和语义分析错误，可以正常生成IR。要求实现局部公共子表达式消除、局部无用代码消除、局部常量折叠三种优化策略
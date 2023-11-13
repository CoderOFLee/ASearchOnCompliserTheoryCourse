## 一、概述
（1）围绕单词识别，开展三种词法分析器的构造，包括手工构造词法分析器以及分别利用 Flex、ANTLR 词法分析器生成工具产生词法分析器源码；

（2）基于 Flex、Bison 为一个实验语言构造能生成抽象语法树（AST）的程序（可先构造检查输入程序合法性的分析器，再按语法制导翻译技术生成AST）

（3）基于 ANTLR 为实验语言构造词法分析器和生成分析树的语法分析器，然后引导学员学习并使用访问者模式实现由分析树生成 AST；

（4）继续使用访问者模式从 AST 生成基于 LLVM IR 的中间代码表示，而后对接 LLVM 这一编译器基础设施，从而利用 LLVM 优秀的编译器后端。

（5）本教学团队提供基于LLVM的驱动器框架及示例，学习使用 Clang/LLVM 应用编程接口构建编译器、程序分析器或优化器。框架中定义了可扩展的Driver类，它调用Clang/LLVM API实现对输入的源程序文件的解析，产生AST、LLVM IR等中间表示，并在这些中间表示上开展程序分析和变换。

## 二、教学资源
#### 1.头歌
https://www.educoder.net/paths/r528n3ot

## 三、实验内容
采用图形化和命令窗口的形式，使学生能够将git和gcc得到实操
#### 1.熟悉GCC/Clang工具链的使用
使用头歌平台的工具，搭载命令行窗口，练习编译命令的使用
#### 2.词法分析
使用VNC虚拟机提供可视化界面，但是整体要求为，编写一个词法分析器，它能对输入的一行字符流进行词法分析，依次识别出其中的关系运算符和其他符号，并输出对应的记号序列。
#### 3.高级语言特性
1）智能指针：构造两个程序展示c++智能指针不安全的情况、并在md中写下unique_str能否作为实参的思考————人工检查

2）RTTI机制：构造两个程序展示c++的typeid关键字和强制类型转换、并在md中写下对样例程序某个块的思考————人工检查

3）多线程编程：在md中回答四个简答题————人工检查

4）线程池管理：在md中回答四个简答题————人工检查
#### 4.语法分析：
1）使用Flex、Bison生成对表达式语言L-exp解析的语法分析器，上传到github，然后再头歌平台贴上链接，由助教人工检查。提给L-exp的文法：
```
input ::= ε  | input line
line ::= EOL | expr EOL
expr ::= NUMBER 
       | expr PLUS expr     # PLUS  – ‘+’加号
       | expr MINUS expr    # MINUS – ‘-’减号
       | expr MULT expr     # MULT  – ‘*’乘号
       | expr DIV expr      # DIV   – ‘/’除号
       | MINUS expr         # MINUS – ‘-’负号
       | expr EXPON expr    # EXPON – ‘**’乘方
       | LB expr RB         # LB, RB – ‘(’, ’)’ 左右括号
```
2）为C1语言，构造分析器。题给C1文法：
```
CompUnit    → [ CompUnit ] ( Decl | FuncDef ) 
Decl        → ConstDecl 
            | VarDecl
ConstDecl   → 'const' BType ConstDef { ',' ConstDef } ';'
BType       → 'int' | 'float'
ConstDef    → Ident '=' Exp
            | Ident '[' [ Exp ] ']' '=' '{' Exp { ',' Exp } '}'
VarDecl     → BType VarDef { ',' VarDef } ';'
VarDef      → Ident 
            | Ident '[' Exp ']' 
            | Ident '=' Exp
            | Ident '[' [ Exp ] ']' '=' '{' Exp { ',' Exp } '}'
FuncDef     → void Ident '(' ')' Block
Block       → '{' { BlockItem } '}'
BlockItem   → Decl 
            | Stmt
Stmt        → LVal '=' Exp ';'
            | Ident '(' ')' ';'
            | Block
            | 'if' '( Cond ')' Stmt [ 'else' Stmt ]
            | 'while' '(' Cond ')' Stmt
            | ';'
LVal        → Ident
            | Ident '[' Exp ']'
Cond        → Exp RelOp Exp
RelOp       → '==' | '!=' | '<' | '>' | '<=' | '>='
Exp         → Exp BinOp Exp
            | UnaryOp Exp
            | '(' Exp ')'
            | LVal
            | Number
Number      → FloatConst
            | IntConst
BinOp       → '+' | '−' | '*' | '/' | '%'
UnaryOp     → '+' | '−'
```
3）提给使用Flex和Bison为SysYF--语言生成分析器。要求阅读demo程序，并回答demo程序是如何完成分析的。（类似给程序写注释）————人工评测
#### 5.符号表管理和语义检查
了解C1语言和SysYF语言，并回答问答
#### 6.中间代码生成
使用LLVM IR生成器，调用其接口，为程序生成LLVM中间代码并打印，由头歌平台比较输出。源程序语言有C1和SysYF--两种。
#### 7.LLVM程序及程序分析器
使用上传讲解视频的方式完成任务。任务要求学习题目所给项目框架，学习项目框架，并分享框架运行原理。题给框架：https://gitee.com/s4plus/llvm-ustc-proj/blob/master/my-llvm-driver/README.md 
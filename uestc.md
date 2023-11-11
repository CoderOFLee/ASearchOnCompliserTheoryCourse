## 一、概述
至少实现一个拥有词法分析和语法分析功能的迷你编译器，能够对（求n！的最小语言）进行分析编译，并能够识别基本的词法语法错误。提供已经设计好的P-CODE，作为扩展。

求n！的最小程序
```
begin
  integer k;
  integer function F(n);
    begin
      integer m;
      if n<= 0 then F:=1
      else F:=n*F(n-1)
    end;
  read(k);
  k:=F(k);
  write(k)
end
```

## 二、实验资源
#### 1.学生实例
https://github.com/FENG-MASTER/MiniCompiler
#### 2.实验指导
https://github.com/CoderOFLee/ASearchOnCompliserTheoryCourse/blob/main/uestc.pdf


## 三、实验内容
#### 1.词法分析
不指定工具，不指定编程语言，完成将输入*.pas，分析输出*.byd记号流
#### 2.语法分析
不指定工具，不指定编程语言，使用递归下降分析发，完成将输入*.byd，分析输出*.dys
#### 3.语义分析
不指定工具，不指定编程语言，完成将输入*.dys，分析输出*.qud，同时生成二进制文件*.ooj
#### 4.中间代码
使用指定的四元式（oprd，op1，op2，result）分析
#### 5.目标代码
使用规定好的P-CODE

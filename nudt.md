## 一、概述
编译原理是一门理论性、技术性、实践性很强的课程，理论和实践相结合是课程的重要特色。本实验课程旨在通过一组实训攻关，达到下列目标：    
1.通过动手实践，使学生理解编译的基本过运用各个编译阶段的功能，能够利用LEX(FLEX)、JavaCC和YACC(BISON)等经典工具，设计并实现给定语言的词法分析、语法分析、语义分析等功能。
2.通过分析和修改PL语言的编译程序和解释程序，获得综合利用编译程序设计的知识进行分析、设计、实现和维护编译程序的能力。

## 二、教学资源
#### 1.头歌
https://www.educoder.net/paths/9a7fs8r6

## 三、实验内容
#### 1.词法分析
使用LEX生成，完成识别出以小写字母ab结尾的所有字符串（仅含大小写字母）并给打印'Hit!'。评测系统根据输出文件对比比较。
```
%{
#include <stdio.h>
%}

%%
 /* begin */
 
 /* end */

\n				{}
.				{}
%%
int yywrap() { return 1; }
int main(int argc, char **argv)
{
	if (argc > 1) {
		if (!(yyin = fopen(argv[1], "r"))) {
			perror(argv[1]);
			return 1;
		}
	}
	while (yylex());
	return 0;
}

```
#### 2.语法分析
使用JAVACC，完成实现一个能够进行加法(+)，减法(-)，乘方(^)的计算器。评测系统比较输出结果。
```
options {
	STATIC = false;
}

PARSER_BEGIN(Calc)
import java.io.*;

class Calc {
	public static void main(String[] args) {
		for (String arg : args) {
			try {
				System.out.println(evaluate(arg));
			} catch (ParseException ex) {
				System.err.println(ex.getMessage());
			}
		}
	}

	public static long evaluate(String src) throws ParseException {
		Reader reader = new StringReader(src);
		return new Calc(reader).expr();
	}
}
PARSER_END(Calc)
 /* begin */
SKIP: { <[" ", "\t", "\r", "\n"]> }

TOKEN: {
	<INTEGER: (["0"-"9"])+>
}

long expr():
{
	Token x, y;
}
{
	x=<INTEGER> "+" y=<INTEGER> <EOF>
	{
		return Long.parseLong(x.image) + Long.parseLong(y.image);
	}
}
 /* end */
```

#### 3.语法分析
使用Bison构建波兰计算器。能够计算出合法的后缀表达式的值，包括加法、减法、乘法、除法、乘方、取反等运算。
```
%{
  #include <ctype.h>
  #include <stdio.h>
  #include <math.h>
  int yylex (void);
  void yyerror (char const *);
%}


%define api.value.type {double}
%token NUM

%% 

/* Grammar rules and actions follow.  */

input:
  %empty
| input line
;


line:
  '\n'
| exp '\n'      { printf ("%.10g\n", $1); }
;


exp:
  NUM           { $$ = $1;           }
 /* begin */

 /* end */
;

%%

/* The lexical analyzer returns a double floating point
   number on the stack and the token NUM, or the numeric code
   of the character read if not a number.  It skips all blanks
   and tabs, and returns 0 for end-of-input.  */


int yylex (void)
{
  int c;

  /* Skip white space.  */
  while ((c = getchar ()) == ' ' || c == '\t')
    continue;

  /* Process numbers.  */
  if (c == '.' || isdigit (c))
    {
      ungetc (c, stdin);
      scanf ("%lf", &yylval);
      return NUM;
    }

  /* Return end-of-input.  */
  if (c == EOF)
    return 0;
  if (c == '!')
  	return 0;
  /* Return a single char.  */
  return c;
}

int main (int argc, char** argv)
{
  return yyparse();
}


/* Called by yyparse on error.  */
void yyerror (char const *s)
{
  fprintf (stderr, "%s\n", s);
}

```
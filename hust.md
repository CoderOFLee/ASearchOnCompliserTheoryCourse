## 一、概述
本实验涉及一个完整的编译器构造过程：即构造一个将SysY语言(系C语言的一个子集)的源程序翻译成ARM/RISCV64汇编代码的编译器。本实践课程要求学生或通过手工编写，或借助Flex/Bison，Antlr4等作为前端工具，完成词法分析、语法检查与分析，语义检查，中间代码生成（要求符合LLVM IR标准），代码优化，以及目标代码生成等工作。

## 二、教学资源
#### 1.头歌
https://www.educoder.net/paths/r72isgp8
#### 2.开源文献导航
https://c9x.me/compile/bib/
#### 3.SysY2022运行时库
https://gitlab.eduxiji.net/nscscc/compiler2022/-/blob/master/SysY2022%E8%BF%90%E8%A1%8C%E6%97%B6%E5%BA%93-V1.pdf

## 三、实验内容
#### 1.编译工具链和语言定义
1）gcc/clang工具链的使用：要求编写一个linux脚本，并结合命令行完成一个脚本联编目标文件

2）了解SysY语法：要求使用SysY语言编写一个“最佳买卖股票”的算法题

3）了解arm指令集：编写一个冒泡排序算法，人工翻译为arm指令进行在线评测

4）了解risc-v指令集：编写一个数组排序算法，人工翻译为risc-v指令在线评测

5）c++智能指针：编写两个程序展示智能指针———人工评测

#### 2.词法分析
1）使用Flex生成SysY的词法分析程序（C），提给模板（后附），要求补充剩余的识别和动作代码。
2）使用antlr生成SysY的词法分析程序（C++），提供模板（后附），要求补充剩余的识别和动作代码，以及格式化输出函数。

#### 3.语法分析
1）使用Flex/Bison生成SysY的语法分析程序（C），提给模板（后附），要求补充stmt的识别。
2）使用antlr生成SysY的分析程序（C++），提给模板(后附)，要求补充
stmt的识别。

#### 4.递归下降分析
在题给框架下，完成递归下降分析函数——b_info parseS(){}，能够实现L-翻译模式的自顶向下翻译，将二进制小数转换成十进制小数。
```
S->BR  select(S->BR) = {'0','1'}
```

#### 5.静态语义检查
手搓程序，使用访问者模式，对SysY的AST进行遍历，得到符号表并进行若干种错误的识别。
#### 6.基于LLVM的代码优化
调用LLVM工具的接口，生成SysY程序的中间代码
#### 7.中间代码优化
给定模板，补充删除死代码的部分代码。后附死代码模板，其余模板在头歌平台可看。
#### 8.PL语言
1）词法/语法：使用Flex和Bison生成能够识别PL语言的词法语法分析器
2）PL0/1分析器：补充模板程序（篇幅问题，头歌查看）
https://www.educoder.net/tasks/3vax52r7gupz?subject_id=r72isgp8

#### 9.功能测试和性能测试
使用头歌的竞赛题

## 四、模板
#### 1.词法分析-flex
```
%option noyywrap
%option yylineno

%{
#include <stdio.h>

enum Token {
    INT=258, FLOAT, VOID, CONST, RETURN, IF, ELSE,  WHILE, BREAK, CONTINUE,
    LP, RP, LB, RB, LC, RC, COMMA, SEMICOLON, QUESTION, COLON,
    MINUS, NOT, TILDE, ASSIGN, ADD, MUL, DIV, MOD, AND, OR,
    EQ, NE, LT, LE, GT, GE,
    ID, INT_LIT, FLOAT_LIT,
    LEX_ERR
};

%}

DIGIT [0-9]
LETTER [A-Za-z]

MultilineComment "/*"([^\*]|(\*)*[^\*/])*(\*)*"*/"
SingleLineComment "//".*$


%%

"int" {printf("%s : INT\n", yytext); return INT; }
"float" {printf("%s : FLOAT\n", yytext); return FLOAT; }
"void" {printf("%s : VOID\n", yytext); return VOID; }
"const" {printf("%s : CONST\n", yytext); return CONST; }
"return" {printf("%s : RETURN\n", yytext); ; return RETURN; }
"if" {printf("%s : IF\n", yytext); return IF; }
"else" {printf("%s : ELSE\n", yytext); return ELSE; }
"while" {printf("%s : WHILE\n", yytext); return WHILE; }
"break" {printf("%s : BREAK\n", yytext); return BREAK; }
"continue" {printf("%s : CONTINUE\n", yytext); return CONTINUE; }

"(" {printf("%s : LP\n", yytext); return LP; }
")" {printf("%s : RP\n", yytext); return RP; }
"[" {printf("%s : LB\n", yytext); return LB; }
"]" {printf("%s : RB\n", yytext); return RB; }
"{" {printf("%s : LC\n", yytext); return LC; }
"}" {printf("%s : RC\n", yytext); return RC; }
"," {printf("%s : COMMA\n", yytext); return COMMA; }
";" {printf("%s : SEMICOLON\n", yytext); return SEMICOLON; }
"?" {printf("%s : QUESTION\n", yytext); return QUESTION; }
":" {printf("%s : COLON\n", yytext); return COLON; }
"!" {printf("%s : NOT\n", yytext); return NOT; }
"=" {printf("%s : ASSIGN\n", yytext); return ASSIGN; }
"-" {printf("%s : MINUS\n", yytext); return MINUS; }
"+" {printf("%s : ADD\n", yytext); return ADD; }
"*" {printf("%s : MUL\n", yytext); return MUL; }
"/" {printf("%s : DIV\n", yytext); return DIV; }
"%" {printf("%s : MOD\n", yytext); return MOD; }
"&&" {printf("%s : AND\n", yytext); return AND; }
"||" {printf("%s : OR\n", yytext); return OR; }
"==" {printf("%s : EQ\n", yytext); return EQ; }
"!=" {printf("%s : NE\n", yytext); return NE; }
"<" {printf("%s : LT\n", yytext); return LT; }
"<=" {printf("%s : LE\n", yytext); return LE; }
">" {printf("%s : GT\n", yytext); return GT; }
">=" {printf("%s : GE\n", yytext); return GE; }

{MultilineComment} {}
{SingleLineComment} {}


[ \n] {}
[ \r\t] {}
[ \n\t]+ {}
. {printf("Lexical error - line %d : %s\n",yylineno,yytext);return LEX_ERR;}

%%


int main(int argc, char** argv) {
    if(argc > 1) {
        if(!(yyin = fopen(argv[1], "r"))) {
           perror(argv[1]); 
           return  1; 
        }       

       int symbol; 
       while( symbol = yylex()) { };

       fclose(yyin);
    } else printf("No source file given!\n"); 

    return 0; 
    
}
```
#### 2.词法分析-antlr
```
lexer grammar SysyLex;

// keyword
INT : 'int';
FLOAT : 'float';
VOID : 'void';
CONST : 'const';
RETURN : 'return';
IF : 'if';
ELSE : 'else';
WHILE : 'while';
BREAK : 'break';
CONTINUE : 'continue'; 

// delimeter
LP : '(' ;
RP : ')' ;
LB : '[' ;
RB : ']' ;
LC : '{' ;
RC : '}' ;
COMMA : ',' ;
SEMICOLON : ';';
QUESTION : '?';
COLON : ':';

// operator
MINUS : '-';
NOT : '!';
ASSIGN : '=';
ADD : '+';
MUL : '*';
DIV : '/';
MOD : '%';
AND : '&&';
OR : '||';
EQ : '==';
NEQ : '!=';
LT : '<';
LE : '<=';
GT : '>';
GE : '>=';

// integer literal


// float literal


// fragment for float literal


// identifier

// string
STRING : '"'(ESC|.)*?'"';

// for string
fragment
ESC : '\\"'|'\\\\';

// whitespace
WS : 
    [ \t\r\n] -> skip
    ;

// comments
LINE_COMMENT : '//' .*? '\r'? '\n' -> skip;
BLOCK_COMMENT : '/*'.*?'*/'-> skip ;
```
#### 3.语法分析-Flex/Bison
```
%define parse.error verbose
%locations
%{
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <errno.h>

#include "ast.h"

ASTNode *root;

extern FILE *yyin;
extern int line_cnt;
extern int yylineno;
extern char *yytext;
extern int yylex();
extern int yyparse();
//extern void yyerror(char *msg);
void yyerror(const char* fmt, ...);
int syntax_error = 0;
char filename[100];
%}

%union {
    int int_val;
    float float_val;
    char *str_val;
    struct ASTNode *node_val;
}

%type <node_val> CompUnit ConstDecl VarDecl FuncDef ConstDef ConstInitVal VarDef InitVal FuncFParam ConstExpArray Block
%type <node_val> Root BlockItem Stmt LVal PrimaryExp UnaryExp FuncRParams MulExp Exp RelExp EqExp LAndExp Cond ConstExp
%type <node_val> ExpArray AddExp LOrExp InitVals 
//ForList

%token <str_val> ID
%token <int_val> INT_LIT
%token <float_val> FLOAT_LIT

%token <int_val> INT FLOAT VOID CONST RETURN IF ELSE WHILE BREAK CONTINUE LP RP LB RB LC RC COMMA SEMICOLON
%token <int_val> MINUS NOT ASSIGN PLUS MUL DIV MOD AND OR EQ NE LT LE GT GE LEX_ERR 
//FOR INC DEC THEN

%nonassoc THEN
%nonassoc ELSE

%start Root

%%
Root: CompUnit { root = new_node(Root, NULL, NULL, $1, 0, 0,  NULL, NonType); };
CompUnit: ConstDecl { $$ = new_node(CompUnit, NULL, NULL, $1, 0, 0, NULL, NonType); }
        | VarDecl { $$ = new_node(CompUnit, NULL, NULL, $1, 0, 0, NULL, NonType); }
        | FuncDef { $$ = new_node(CompUnit, NULL, NULL, $1, 0, 0, NULL, NonType); }
        | ConstDecl CompUnit { $$ = new_node(CompUnit, $2, NULL, $1, 0, 0, NULL, NonType); }
        | VarDecl CompUnit { $$ = new_node(CompUnit, $2, NULL, $1, 0, 0, NULL, NonType); }
        | FuncDef CompUnit { $$ = new_node(CompUnit, $2, NULL, $1, 0, 0, NULL, NonType); };

ConstDecl: CONST INT ConstDef SEMICOLON { $$ = new_node(ConstDecl, NULL, NULL, $3, 0, 0, NULL, Int); }
         | CONST FLOAT ConstDef SEMICOLON { $$ = new_node(ConstDecl, NULL, NULL, $3, 0, 0, NULL, Float); };
ConstDef: ID ConstExpArray ASSIGN ConstInitVal { $$ = new_node(ConstDef, NULL, $2, $4, 0, 0, $1, NonType); }
        | ID ConstExpArray ASSIGN ConstInitVal COMMA ConstDef { $$ = new_node(ConstDef, $6, $2, $4, 0, 0, $1, NonType); };
ConstExpArray: { $$ = NULL; }
             | LB ConstExp RB ConstExpArray { $$ = new_node(ConstExpArray, $4, NULL, $2, 0, 0, NULL, NonType); };
ConstInitVal: ConstExp { $$ = new_node(ConstInitVal, NULL, NULL, $1, 0, 0, NULL, NonType); }
            | LC RC { $$ = new_node(ConstInitVal, NULL, NULL, NULL, 0, 0, NULL, NonType); }
            | LC ConstInitVal RC { $$ = new_node(ConstInitVal, NULL, NULL, $2, 0, 0, NULL, NonType); }
            | LC ConstInitVal COMMA ConstInitVal RC { $$ = new_node(ConstInitVal, $4, NULL, $2, 0, 0, NULL, NonType); };
ConstExp: MulExp { $$ = new_node(ConstExp, NULL, NULL, $1, 0, 0, NULL, NonType); }
        | MulExp PLUS Exp { $$ = new_node(ConstExp, $3, NULL, $1, PLUS, 0, NULL, NonType); }
        | MulExp MINUS Exp { $$ = new_node(ConstExp, $3, NULL, $1, MINUS, 0, NULL, NonType); };

VarDecl: INT VarDef SEMICOLON { $$ = new_node(VarDecl, NULL, NULL, $2, 0, 0, NULL, Int); }
       | FLOAT VarDef SEMICOLON { $$ = new_node(VarDecl, NULL, NULL, $2, 0, 0, NULL, Float); };
VarDef: ID ConstExpArray { $$ = new_node(VarDef, NULL, $2, NULL, 0, 0, $1, NonType); }
      | ID ConstExpArray ASSIGN InitVal { $$ = new_node(VarDef, NULL, $2, $4, 0, 0, $1, NonType); }
      | ID ConstExpArray COMMA VarDef { $$ = new_node(VarDef, $4, $2, NULL, 0, 0, $1, NonType); }
      | ID ConstExpArray ASSIGN InitVal COMMA VarDef { $$ = new_node(VarDef, $6, $2, $4, 0, 0, $1, NonType); };
InitVal: Exp { $$ = new_node(InitVal, NULL, NULL, $1, Exp, 0, NULL, NonType); }
       | LC RC { $$ = new_node(InitVal, NULL, NULL, NULL, InitVals, 0, NULL, NonType); }
       | LC InitVals RC { $$ = new_node(InitVal, NULL, NULL, $2, InitVals, 0, NULL, NonType); };
InitVals: InitVal { $$ = new_node(InitVals, NULL, NULL, $1, 0, 0, NULL, NonType); }
        | InitVal COMMA InitVals { $$ = new_node(InitVals, $3, NULL, $1, 0, 0, NULL, NonType); };

FuncDef: INT ID LP RP Block { $$ = new_node(FuncDef, NULL, NULL, $5, 0, 0, $2, Int); }
       | FLOAT ID LP RP Block { $$ = new_node(FuncDef, NULL, NULL, $5, 0, 0, $2, Float); }
       | VOID ID LP RP Block { $$ = new_node(FuncDef, NULL, NULL, $5, 0, 0, $2, Void); }
       | INT ID LP FuncFParam RP Block { $$ = new_node(FuncDef, NULL, $4, $6, 0, 0, $2, Int); }
       | FLOAT ID LP FuncFParam RP Block { $$ = new_node(FuncDef, NULL, $4, $6, 0, 0, $2, Float); }
       | VOID ID LP FuncFParam RP Block { $$ = new_node(FuncDef, NULL, $4, $6, 0, 0, $2, Void); };;
FuncFParam: INT ID { $$ = new_node(FuncFParam, NULL, NULL, NULL, 0, 0, $2, Int); }
          | FLOAT ID { $$ = new_node(FuncFParam, NULL, NULL, NULL, 0, 0, $2, Float); }
          | INT ID LB RB ExpArray { $$ = new_node(FuncFParam, NULL, NULL, $5, 0, 0, $2, Int); }
          | FLOAT ID LB RB ExpArray { $$ = new_node(FuncFParam, NULL, NULL, $5, 0, 0, $2, Float); }
          | INT ID COMMA FuncFParam { $$ = new_node(FuncFParam, $4, NULL, NULL, 0, 0, $2, Int); }
          | FLOAT ID COMMA FuncFParam { $$ = new_node(FuncFParam, $4, NULL, NULL, 0, 0, $2, Float); }
          | INT ID LB RB ExpArray COMMA FuncFParam { $$ = new_node(FuncFParam, $7, NULL, $5, 0, 0, $2, Int); }
          | FLOAT ID LB RB ExpArray COMMA FuncFParam { $$ = new_node(FuncFParam, $7, NULL, $5, 0, 0, $2, Float); };

Block: LC BlockItem RC { $$ = new_node(Block, NULL, NULL, $2, 0, 0, NULL, NonType); };
BlockItem: { $$ = NULL; }
         | ConstDecl BlockItem { $$ = new_node(BlockItem, $2, NULL, $1, 0, 0, NULL, NonType); }
         | VarDecl BlockItem { $$ = new_node(BlockItem, $2, NULL, $1, 0, 0, NULL, NonType); }
         | Stmt BlockItem { $$ = new_node(BlockItem, $2, NULL, $1, 0, 0, NULL, NonType); };

// 以下是你需要完成的语法规则和语义计算规则
//Stmt → LVal '=' Exp ';' | [Exp] ';' | Block
//| 'if' '(' Cond ')' Stmt [ 'else' Stmt ]
//| 'while' '(' Cond ')' Stmt
//| 'break' ';' | 'continue' ';'
//| 'return' [Exp] ';'



Exp: AddExp { $$ = new_node(Exp, NULL, NULL, $1, 0, 0, NULL, NonType); };
AddExp: MulExp { $$ = new_node(AddExp, NULL, NULL, $1, MUL, 0, NULL, NonType); }
      | MulExp PLUS AddExp { $$ = new_node(AddExp, $3, NULL, $1, PLUS, 0, NULL, NonType); }
      | MulExp MINUS AddExp { $$ = new_node(AddExp, $3, NULL, $1, MINUS, 0, NULL, NonType); };
MulExp: UnaryExp { $$ = new_node(MulExp, NULL, NULL, $1, UnaryExp, 0, NULL, NonType); }
      | UnaryExp MUL MulExp { $$ = new_node(MulExp, $3, NULL, $1, MUL, 0, NULL, NonType); }
      | UnaryExp DIV MulExp { $$ = new_node(MulExp, $3, NULL, $1, DIV, 0, NULL, NonType); }
      | UnaryExp MOD MulExp { $$ = new_node(MulExp, $3, NULL, $1, MOD, 0, NULL, NonType); };
UnaryExp: PrimaryExp { $$ = new_node(UnaryExp, NULL, NULL, $1, PrimaryExp, 0, NULL, NonType); }
        | ID LP RP { $$ = new_node(UnaryExp, NULL, NULL, NULL, FuncRParams, 0, $1, NonType); }
        | ID LP FuncRParams RP { $$ = new_node(UnaryExp, NULL, NULL, $3, FuncRParams, 0, $1, NonType); }
        | PLUS UnaryExp { $$ = new_node(UnaryExp, NULL, NULL, $2, Plus, 0, NULL, NonType); }
        | MINUS UnaryExp { $$ = new_node(UnaryExp, NULL, NULL, $2, Minus, 0, NULL, NonType); }
        | NOT UnaryExp { $$ = new_node(UnaryExp, NULL, NULL, $2, NOT, 0, NULL, NonType); };
FuncRParams: Exp { $$ = new_node(FuncRParams, NULL, NULL, $1, 0, 0, NULL, NonType); }
           | Exp COMMA FuncRParams { $$ = new_node(FuncRParams, $3, NULL, $1, 0, 0, NULL, NonType); };
PrimaryExp: LP Exp RP { $$ = new_node(PrimaryExp, NULL, NULL, $2, Exp, 0, NULL, NonType); }
          | LVal { $$ = new_node(PrimaryExp, NULL, NULL, $1, LVal, 0, NULL, NonType); }
          | INT_LIT { $$ = new_node(PrimaryExp, NULL, NULL, NULL, $1, 0, NULL, Int); }
          | FLOAT_LIT { $$ = new_node(PrimaryExp, NULL, NULL, NULL, 0, $1, NULL, Float); };
LVal: ID ExpArray { $$ = new_node(LVal, NULL, NULL, $2, 0, 0, $1, NonType); };

Cond: LOrExp { $$ = new_node(Cond, NULL, NULL, $1, 0, 0, NULL, NonType); };
LOrExp: LAndExp { $$ = new_node(Cond, NULL, NULL, $1, 0, 0, NULL, NonType); }
      | LAndExp OR LOrExp { $$ = new_node(Cond, $3, NULL, $1, OR, 0, 0, NonType); };
LAndExp: EqExp { $$ = new_node(LAndExp, NULL, NULL, $1, 0, 0, NULL, NonType); }
       | EqExp AND LAndExp { $$ = new_node(LAndExp, $3, NULL, $1, AND, 0, NULL, NonType); };
EqExp: RelExp { $$ = new_node(EqExp, NULL, NULL, $1, 0, 0, NULL, NonType); }
     | RelExp EQ EqExp { $$ = new_node(EqExp, $3, NULL, $1, EQ, 0, NULL, NonType); }
     | RelExp NE EqExp { $$ = new_node(EqExp, $3, NULL, $1, NE, 0, NULL, NonType); };
RelExp: AddExp { $$ = new_node(RelExp, NULL, NULL, $1, 0, 0, NULL, NonType); }
      | AddExp LT RelExp { $$ = new_node(RelExp, $3, NULL, $1, LT, 0, NULL, NonType); }
      | AddExp GT RelExp { $$ = new_node(RelExp, $3, NULL, $1, GT, 0, NULL, NonType); }
      | AddExp LE RelExp { $$ = new_node(RelExp, $3, NULL, $1, LE, 0, NULL, NonType); }
      | AddExp GE RelExp { $$ = new_node(RelExp, $3, NULL, $1, GE, 0, NULL, NonType); };

ExpArray: { $$ = NULL; }
        | LB Exp RB ExpArray { $$ = new_node(ExpArray, $4, NULL, $2, 0, 0, NULL, NonType); };
%%

int main(int argc, char *argv[]) {
    int index = strlen(argv[1]) - 1;
    while(index > 0 && argv[1][index - 1] != '/')
        index--;
    strcpy(filename, argv[1] + index);
    freopen(argv[1], "r", stdin);
    yyparse();
    if (syntax_error == 0) 
        display(root);
    return 0;
}

/*
void yyerror(char *msg) {
    printf("%s:%d\n", name, yylineno);
    printf("error text: %s\n", yytext);
    exit(-1);
}
*/
#include<stdarg.h>
void yyerror(const char* fmt, ...)
{
    syntax_error = 1;    
    va_list ap;
    va_start(ap, fmt);
    fprintf(stderr, "%s:%d ", filename, yylineno);
    vfprintf(stderr, fmt, ap);
    fprintf(stderr, ".\n");
}	
```
#### 5.语法分析-antlr
```
grammar Sysy;

import SysyLex;

compUnit : compUnitItem* EOF;
compUnitItem
    : decl
    | funcDef
    ;

decl
    : constDecl
    | varDecl
    ;

constDecl : Const bType constDef (Comma constDef)* Semicolon;

bType
    : Int  # int
    | Float  # float
    ;

constDef : Ident (Lbracket exp Rbracket)* Assign initVal;

varDecl : bType varDef (Comma varDef)* Semicolon;

varDef : Ident (Lbracket exp Rbracket)* (Assign initVal)?;

initVal
    : exp  # init
    | Lbrace (initVal (Comma initVal)*)? Rbrace  # initList
    ;

funcDef : funcType Ident Lparen funcFParams? Rparen block;

funcType
    : bType  # funcType_
    | Void  # void
    ;

funcFParams : funcFParam (Comma funcFParam)*;

funcFParam
    : bType Ident  # scalarParam
    | bType Ident Lbracket Rbracket (Lbracket exp Rbracket)*  # arrayParam
    ;

block : Lbrace blockItem* Rbrace;

blockItem
    : decl
    | stmt
    ;

// 以下文法没有实现的部分请补充完整，请将终结符用词法规则中对应的Token符号代替
//    词法规则定义在SysyLex.g4中

// Stmt → LVal '=' Exp ';' 
// | [Exp] ';'
// | Block
// | 'if' '( Cond ')' Stmt [ 'else' Stmt ]
// | 'while' '(' Cond ')' Stmt
// | 'break' ';
// | 'continue' ';'
// | 'return' [Exp] ';'

stmt
    : lVal Assign exp Semicolon  # assign
    | exp? Semicolon  # exprStmt
    | block  # blockStmt
    | If Lparen cond Rparen stmt (Else stmt)?  # ifElse




    ;

// *************** END *********************

exp : addExp;

cond : lOrExp;

lVal : Ident (Lbracket exp Rbracket)*;

primaryExp
    : Lparen exp Rparen  # primaryExp_
    | lVal  # lValExpr
    | number  # primaryExp_
    ;

intConst
    : DecIntConst  # decIntConst
    | OctIntConst  # octIntConst
    | HexIntConst  # hexIntConst
    ;
floatConst
    : DecFloatConst  # decFloatConst
    | HexFloatConst  # hexFloatConst
    ;
number
    : intConst
    | floatConst
    ;

unaryExp
    : primaryExp  # unaryExp_
    | Ident Lparen funcRParams? Rparen  # call
    | Add unaryExp  # unaryAdd
    | Sub unaryExp  # unarySub
    | Not unaryExp  # not
    ;

stringConst : StringConst;
funcRParam
    : exp
    | stringConst
    ;
funcRParams : funcRParam (Comma funcRParam)*;

mulExp
    : unaryExp  # mulExp_
    | mulExp Mul unaryExp  # mul
    | mulExp Div unaryExp  # div
    | mulExp Mod unaryExp  # mod
    ;

addExp
    : mulExp  # addExp_
    | addExp Add mulExp  # add
    | addExp Sub mulExp  # sub
    ;

relExp
    : addExp  # relExp_
    | relExp Lt addExp  # lt
    | relExp Gt addExp  # gt
    | relExp Leq addExp  # leq
    | relExp Geq addExp  # geq
    ;

eqExp
    : relExp  # eqExp_
    | eqExp Eq relExp  # eq
    | eqExp Neq relExp  # neq
    ;

lAndExp
    : eqExp  # lAndExp_
    | lAndExp And eqExp  # and
    ;

lOrExp
    : lAndExp  # lOrExp_
    | lOrExp Or lAndExp  # or
    ;
```
#### 6删除死代码
```
#include "DeleteDeadCode.h"
#include "ConstSpread.h"

std::set<std::string> OptFunc = {"getint",          "getfloat",
                                 "getch",           "getarray",
                                 "getfarray",       "putint",
                                 "putfloat",        "putch",
                                 "putarray",        "putfarray",
                                 "_sysy_starttime", "_sysy_stoptime",
                                 "memcpy",          "memclr",
                                 "memset",          "llvm.memset.p0.i32",
                                 "__aeabi_memcpy4", "__aeabi_memclr4",
                                 "__aeabi_memset4"};

void DeadCodeDeletion::initFuncPtrArg() {
  for (auto foo : m->function_list_) {
    if (foo->basic_blocks_.empty())
      continue;
    for (auto arg : foo->arguments_)
      if (arg->type_->tid_ == Type::PointerTyID) {
        if (!funcPtrArgs.count(foo))
          funcPtrArgs[foo] = {};
        funcPtrArgs[foo].insert(arg);
      }
  }
}
void DeadCodeDeletion::Init(Function *foo) {
  storePos.clear();
  for (auto bb : foo->basic_blocks_) {
    for (auto ins : bb->instr_list_) {
      if (ins->op_id_ == Instruction::GetElementPtr) {
      } else if (ins->op_id_ == Instruction::Store) {
        if (!storePos.count(ins->get_operand(1))) {
          storePos.insert({ins->get_operand(1), {}});
        }
        storePos[ins->get_operand(1)].push_back(ins);
        if (dynamic_cast<GlobalVariable *>(ins->get_operand(1)))
          OptFunc.insert(foo->name_);
        if (dynamic_cast<GetElementPtrInst *>(ins->get_operand(1)))
          OptFunc.insert(foo->name_);
        if (funcPtrArgs[foo].count(ins->get_operand(1)))
          OptFunc.insert(foo->name_);
      } else if (ins->op_id_ == Instruction::Call) {
        auto f = ins->get_operand(ins->operands_.size() - 1);
        if (OptFunc.count(f->name_))
          OptFunc.insert(foo->name_);
      }
    }
  }
}
bool DeadCodeDeletion::checkOpt(Function *foo, Instruction *ins) {
  if (ins->op_id_ == Instruction::Ret) {
    exitBlock = ins->parent_;
    return true;
  } else if (ins->op_id_ == Instruction::Call) {
    auto f = ins->get_operand(ins->operands_.size() - 1);
    return OptFunc.count(f->name_);
  } else if (ins->op_id_ == Instruction::Store) {
    if (dynamic_cast<GlobalVariable *>(ins->get_operand(1)))
      return true;
    if (dynamic_cast<GetElementPtrInst *>(ins->get_operand(1)))
      return true;
    if (funcPtrArgs[foo].count(ins->get_operand(1)))
      return true;
    return false;
  }
  return false;
}

void DeadCodeDeletion::findInstr(Function *foo) {
  std::list<Value *> workList;
  for (auto bb : foo->basic_blocks_) {
    for (auto ins : bb->instr_list_) {
      if (checkOpt(foo, ins)) {
        uselessInstr.insert(ins);
        workList.push_back(ins);
      }
    }
  }
  while (!workList.empty()) {
    auto ins = dynamic_cast<Instruction *>(workList.back());
    workList.pop_back();
    if (ins == nullptr) {
      continue;
    }
    for (auto operand : ins->operands_) {
      auto temp = dynamic_cast<Instruction *>(operand);
      if (!temp)
        continue;
      if (uselessInstr.insert(temp).second) {
        workList.push_back(temp);
      }
    }
    if (ins->op_id_ == Instruction::PHI) {
      for (int i = 1; i < ins->operands_.size(); i += 2) {
        auto bb = dynamic_cast<BasicBlock *>(ins->get_operand(i));
        auto br = bb->get_terminator();
        if (uselessInstr.insert(br).second) {
          workList.push_back(br);
        }
      }
    }
    if (storePos.count(ins)) {
      for (auto curInstr : storePos[ins]) {
        if (uselessInstr.insert(dynamic_cast<Instruction *>(curInstr)).second) {
          workList.push_back(curInstr);
        }
      }
      storePos.erase(ins);
    }
    if (uselessBlock.insert(ins->parent_).second) {
      for (auto RFrontier : ins->parent_->rdom_frontier_) {
        auto t = RFrontier->get_terminator();
        if (uselessInstr.insert(t).second) {
          workList.push_back(t);
        }
      }
    }
  }
}
void DeadCodeDeletion::deleteInstr(Function *foo) {
  int deleteCnt = 0, changeCnt = 0;
  for (auto bb : foo->basic_blocks_) {
    std::vector<Instruction *> ins2Del;
    for (auto ins : bb->instr_list_) {
      if (!uselessInstr.count(ins)) {
        if (ins->op_id_ != Instruction::Br) {
          ins2Del.push_back(ins);
        } else {
          if (ins->operands_.size() == 3) {
            changeCnt++;
            auto trueBB = dynamic_cast<BasicBlock *>(ins->get_operand(1));
            auto falseBB = dynamic_cast<BasicBlock *>(ins->get_operand(2));
            trueBB->remove_pre_basic_block(bb);
            falseBB->remove_pre_basic_block(bb);
            bb->remove_succ_basic_block(trueBB);
            bb->remove_succ_basic_block(falseBB);
            BasicBlock *temp = exitBlock;
            std::vector<BasicBlock *> rdoms(bb->rdoms_.begin(),
                                            bb->rdoms_.end());
            std::sort(rdoms.begin(), rdoms.end(),
                      [=](BasicBlock *x, BasicBlock *y) -> bool {
                        return x->rdoms_.count(y);
                      });
            for (auto rdbb : rdoms) {
              if (rdbb != bb && uselessBlock.count(rdbb)) {
                temp = rdbb;
                break;
              }
            }
            ins->remove_operands(0, 2);
            ins->num_ops_ = 1;
            ins->operands_.resize(1);
            ins->use_pos_.resize(1);
            ins->set_operand(0, temp);
            bb->add_succ_basic_block(temp);
            temp->add_pre_basic_block(bb);
          }
        }
      }
    }
    deleteCnt += ins2Del.size();
    for (auto ins : ins2Del) {
      bb->delete_instr(ins);
    }
  }
}

void DeadCodeDeletion::execute() {
  ReverseDomainTree reverseDomainTree(m);
  reverseDomainTree.execute();
  initFuncPtrArg();
  for (auto foo : m->function_list_)
    if (!foo->basic_blocks_.empty()) {
      Init(foo);
      findInstr(foo);
      deleteInstr(foo);
      DeleteUnusedBB(foo);
    }
}
```
#### 7.
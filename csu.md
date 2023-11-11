## 一、概述
采用实训任务模式，平台提供自动化评测机制。对词法分析、预测分析法、算符优先法、递归下降法进行程序设计，并且能实现读入测试用例，根据预测分析的方法进行语法分析，直到源程序结束，对遇到的语法错误做出错误处理。源程序语法是C语言子集。

全部采用手搓，基于c/java

## 二、教学资源
#### 1.头歌
https://www.educoder.net/paths/8bl6caqp

## 三、实验内容
因为篇幅问题，先简述实验内容，然后再后面依次给出csu官方给出的代码模板
#### 1.词法分析
使用模板，要求学生完善程序，补全识别逻辑。通过比较输出文件进行评测，测试集部分隐藏。

#### 2.语法分析-预测分析
加载好预定的模板，预定的模板完成了程序紧急退出、自动化测试调用、词法分析、文法消除左递归和格式化输出的部分。在给定的地方预先声明了构造first集合、follow集和预测分析表的三个函数，学生只需要补全这三个函数的内部逻辑即可。整体模板很长，可优化的点在于可以将这三个函数封装成接口，学生不需要看到黑箱外的项目主体，只需要关注黑箱内部实现即可，这样更简洁。
```
/*构造预测分析表*/
void prediction_table(void){}

/*构造first集合*/
size_t find_first(noterminal* this_noterminalproduction*this_production){}

/*构造follow集合*/
size_t find_follow(noterminal* this_noterminal，production*this_production){}
```
#### 3.语法分析-递归分析
同样加载好预定的模板，这个实验并不要求针对C语言子集进行分析，而对指定的简单文法进行分析。文法如下：
```
E->TG
G->+TG | e
T->FS
S->*FS | e
F->(E) | i
```
#### 4.语法分析-算符优先分析
类似实验二，给出模板，只需补全相关函数即可
```
/*构造firstvt,lastvt*/
void add_firstvt(string b, int a){}
void add_firstvt(char c, int a){}
void add_lastvt(string b, int a){}
void add_lastvt(char c, int a){}
string get_cur_firstvt(char c, int a) {}
string get_cur_lastvt(char c, int a){}
```

## 四、代码模板
#### 1.词法分析
```
#include<stdio.h>
#include<string.h>
#include<iostream>
using namespace std;
char prog[80], token[20];
char ch;
int syn, p, m = 0, n, row, sum = 0;
const char* rwtab[8] = { "if","int","for","while","do","return","break","continue" };
const char* rwtab1[8] = { "main","a","b","c","d","e","f","g" };

void scaner()
{
	/*
		共分为三大块，分别是标示符、数字、符号，对应下面的 if   else if  和 else
	*/
	for (n = 0; n < 8; n++) token[n] = NULL;
	ch = prog[p++];
	while (ch == ' ')
	{
		ch = prog[p];
		p++;
	}
	/*******Beign*******/
	/*进行标示符或者关键字的识别*/
	
	/********End********/

	/*******Beign*******/
	/*进行数字的识别*/
	
	/********End********/

	/*******Beign*******/
	/*进行其他字符的识别*/
	
	/********End********/
}

int main()
{
	/*******Beign*******/
	/*输入*/
	
	/********End********/
	/*******Beign*******/
	/*输出*/
	
	/********End********/
}
```
#### 2.语法分析-预测分析
```
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <ctype.h>

#ifndef success
#define success 1
#endif

#define MAX_AMOUNT 20

//定义非终结符
typedef struct NOterminal
{
    char character;
    int  first_number;  //FIRST集的判定，初始为0
    int  follow_number; //FOLLOW集的判定，初始为0
    char* FIRST;       //FIRST 集合
    char* FOLLOW;      //FOLLOW集合
    struct NOterminal* next;
} noterminal;

//定义终结符
typedef struct Terminal
{
    char  character;        //当前的字符
    struct Terminal* next;
} terminal;

//定义产生式
typedef struct PRODUCTION
{
    char source;                //产生的开始
    char* result;              //产生的结果
    struct PRODUCTION* next;    //指向下一条
} production;

int amount = 0;
char TEST_STACK[20];
char* TEST_LIST[10];
char terminal_all[10] = { 0 };
terminal   TERMINAL_HEAD, * current_terminal = &TERMINAL_HEAD;
noterminal NOTERMINAL_HEAD, * current_noterminal = &NOTERMINAL_HEAD;
production PRODUCTION_HEAD, * current_production = &PRODUCTION_HEAD;

//函数申明
size_t read(void);
size_t test(void);
size_t write(void);
void Test_read(void);
size_t STACK_FULL();
void STACK_POP(void);
size_t STACK_EMPTY();
void init_stack(void);
void prediction(void);
void test_follow(void);
void emergency(int model);
void prediction_table(void);
void STACK_PUSH(char source);
void insert_to_terminal(char get);
void insert_to_noterminal(char get);
void eliminate_left_recursion(void);
void combine(char* destinction, char* source);
size_t is_appeared(char tobejudged, char* source);
void insert_to_production(char source, char* result);
size_t find_first(noterminal* this_noterminal, production* this_production);
size_t find_follow(noterminal* this_noterminal, production* this_production);



int main(void)
{
    TERMINAL_HEAD.next = NULL;
    NOTERMINAL_HEAD.next = NULL;
    PRODUCTION_HEAD.next = NULL;
    read();
    Test_read();
    printf("\n消除左递归\n\n");
    eliminate_left_recursion();

    Test_read();

    printf("\n求FIRST集\n\n");
    prediction();
    printf("\n求FOLLOW集\n\n");
    test_follow();
    prediction_table();
    emergency(0);
    return 0;
}

//读文件
size_t read(void)
{
    int line = 0, model = 0, old_line = 0;
    int number = 0;
    char current_get = 0, read_temp[10] = { 0 };
    FILE* read_stream = fopen("/data/workspace/myshixun/test.txt", "r");
    if (!read_stream)
    {
        printf("error in open file ,can`t open file\n");
        exit(EXIT_FAILURE);
    }
    insert_to_terminal('#');    //插入栈底元素，以#表示栈底
    insert_to_terminal('^');    //插入空串元素，以^表示栈底
    while (!feof(read_stream))
    {
        current_get = fgetc(read_stream);
        while (current_get == ' ')
            current_get = fgetc(read_stream); //忽略空格
        while (current_get == '\n')
        {
            current_get = fgetc(read_stream);
            line++;     //跳过换行
        }

        switch (line)
        {
        case 0:
            insert_to_noterminal(current_get);
            break;
        case 1:
            insert_to_terminal(current_get);
            break;
        case 3:
            ungetc(current_get, read_stream);
            old_line = 1;
            break;
        default:
            perror("error in format of program \n");
            emergency(0);
            break;
        }
        if (old_line)
            break;
    }
    while (!feof(read_stream))
    {
        memset(read_temp, 0, sizeof(read_temp));
        old_line = line;
        current_get = fgetc(read_stream);
        while (current_get == ' ')
            current_get = fgetc(read_stream);     //忽略空格
        while (current_get == '\n')
        {
            current_get = fgetc(read_stream);
            line++;     //跳过换行
        }
        model = ((line - old_line) > model) ? (line - old_line) : model;
        switch (model)
        {
        case 0:
        case 1:
            fscanf(read_stream, "%s", read_temp);
            insert_to_production(current_get, read_temp);
            break;
        case 2:
            ungetc(current_get, read_stream);
            TEST_LIST[number] = (char*)malloc(20);
            memset(TEST_LIST[number], 0, 20);
            fscanf(read_stream, "%s", TEST_LIST[number++]);
            break;
        default:
            perror("error in format of program \n");
            emergency(1);
            break;
        }
    }

    fclose(read_stream);
    return success;
}
//测试
size_t test(void)
{
    noterminal* TEMP_NOTERMINAL = NOTERMINAL_HEAD.next;

    for (; TEMP_NOTERMINAL != NULL; TEMP_NOTERMINAL = TEMP_NOTERMINAL->next)
    {
        printf("%c\tfirst number=%d\tfirst=%s\n",
            TEMP_NOTERMINAL->character,
            TEMP_NOTERMINAL->first_number,
            TEMP_NOTERMINAL->FIRST);
    }
    printf("\n");
    return success;
}

/******Begin******/
//求FIRST集合
size_t find_first(noterminal* this_noterminal, production* this_production)
{
   

}
/******End******/
/******Begin******/
//求FOLLOW集合
size_t find_follow(noterminal* this_noterminal, production* this_production)
{
   
}
/******End******/


//紧急退出
void emergency(int model)
{
    current_noterminal = NOTERMINAL_HEAD.next;
    current_terminal = TERMINAL_HEAD.next;
    current_production = PRODUCTION_HEAD.next;
    while (current_noterminal)
    {
        NOTERMINAL_HEAD.next = current_noterminal->next;
        free(current_noterminal->FIRST);
        free(current_noterminal->FOLLOW);
        free(current_noterminal);
        current_noterminal = NOTERMINAL_HEAD.next;
    }
    while (current_terminal)
    {
        TERMINAL_HEAD.next = current_terminal->next;
        free(current_terminal);
        current_terminal = TERMINAL_HEAD.next;
    }
    while (current_production)
    {
        PRODUCTION_HEAD.next = current_production->next;
        free(current_production->result);
        free(current_production);
        current_production = PRODUCTION_HEAD.next;
    }
    printf("退出成功\n");
    exit(0);
}

//插入到终结符
void insert_to_terminal(char get)
{
    terminal* Temp_terminal = (terminal*)malloc(sizeof(terminal));
    if (!Temp_terminal)
    {
        perror("can`t malloc for this program\n");
        emergency(0);
    }
    Temp_terminal->character = get;
    Temp_terminal->next = NULL;
    current_terminal->next = Temp_terminal;
    current_terminal = Temp_terminal; //移向下一个节点
    return;
}

//插入到非终结符
void insert_to_noterminal(char get)
{
    noterminal* Temp_noterminal = (noterminal*)malloc(sizeof(noterminal));
    if (!Temp_noterminal)
    {
        perror("can`t malloc for this program\n");
        emergency(0);
    }
    Temp_noterminal->character = get;
    Temp_noterminal->next = NULL;
    Temp_noterminal->FIRST = NULL;
    Temp_noterminal->FOLLOW = NULL;
    Temp_noterminal->first_number = 0;
    Temp_noterminal->follow_number = 0;
    current_noterminal->next = Temp_noterminal;
    current_noterminal = Temp_noterminal;     //移向下一个节点
    return;
}

//插入到产生式
void insert_to_production(char source, char* result)
{

    char TEMP[20] = { 0 };
    int COUNT = 0, number = 0, length = 0, exit_condition = strlen(result);
    production* Temp_production;
    for (COUNT = 0; COUNT != exit_condition + 1; COUNT++)
    {
        if (*result == '-' && *(result + 1) == '>')
        {
            result += 2;
            COUNT += 2;
        }
        if ((*result != '|') && (*result))
            TEMP[number++] = *result;
        else
        {
            Temp_production = (production*)malloc(sizeof(production));
            length = strlen(TEMP) + 1;
            Temp_production->result = (char*)malloc(length);
            memset(Temp_production->result, 0, length);
            strncpy(Temp_production->result, TEMP, length - 1);
            memset(TEMP, 0, sizeof(TEMP));
            Temp_production->source = source;
            Temp_production->next = NULL;
            current_production->next = Temp_production;
            current_production = Temp_production;
            number = 0;
        }
        result++;
    }
    return;
}



//消除左递归
void eliminate_left_recursion(void)
{
    int number = 0;
    char new_char[3] = { 0 }, TEMP_RESULT[20], temp_empty[3] = { '^',0,0 };
    production* Temp_production = PRODUCTION_HEAD.next;
    production* Temp_FREE;
    terminal* temp = TERMINAL_HEAD.next;
    while (Temp_production)
    {
        if (Temp_production->source == Temp_production->result[0])
        {
            memset(TEMP_RESULT, 0, sizeof(TEMP_RESULT));
            new_char[0] = Temp_production->source - 'A' + 'a';
            //复制到新的产生式
            strcat(TEMP_RESULT, Temp_production->result + 1);
            strcat(TEMP_RESULT, new_char);
            insert_to_noterminal(new_char[0]);
            insert_to_production(new_char[0], TEMP_RESULT);
            insert_to_production(new_char[0], temp_empty);

            //修改当前的产生式
            memset(TEMP_RESULT, 0, sizeof(TEMP_RESULT));
            strcat(TEMP_RESULT, Temp_production->next->result);
            strcat(TEMP_RESULT, new_char);
            memset(Temp_production->result, 0, strlen(Temp_production->result));
            strncpy(Temp_production->result, TEMP_RESULT, strlen(TEMP_RESULT));

            Temp_FREE = Temp_production->next;
            Temp_production->next = Temp_production->next->next;
            free(Temp_FREE);
            continue;
        }
        Temp_production = Temp_production->next;
    }
    while (temp)
    {
        terminal_all[number++] = temp->character;
        temp = temp->next;
    }
    return;
}


void Test_read(void)
{
    int number = 1;
    production* TEMP_PRODUCTION = PRODUCTION_HEAD.next;
    terminal* TEMP_TERMINAL = TERMINAL_HEAD.next;
    noterminal* TEMP_NOTERMINAL = NOTERMINAL_HEAD.next;

    printf("\n产生式\n");
    for (number = 1; TEMP_PRODUCTION != NULL; TEMP_PRODUCTION = TEMP_PRODUCTION->next, number++)
    {
        printf("%d\t%c\t%s\n", number, TEMP_PRODUCTION->source, TEMP_PRODUCTION->result);
    }
    printf("\n终结符\n");
    for (; TEMP_TERMINAL != NULL; TEMP_TERMINAL = TEMP_TERMINAL->next)
    {
        printf("%c\t", TEMP_TERMINAL->character);
    }
    printf("\n");
    printf("\n非终结符\n");
    for (; TEMP_NOTERMINAL != NULL; TEMP_NOTERMINAL = TEMP_NOTERMINAL->next)
    {
        printf("%c\t", TEMP_NOTERMINAL->character);
    }
    printf("\n读取测试\n%s\n%s\n", TEST_LIST[0], TEST_LIST[1]);
    printf("\n");
    return;
}

size_t is_appeared(char tobejudged, char* source)
{
    size_t length = strlen(source), counts = 0;
    while ((counts != length) && (*source != tobejudged))
    {
        counts++;
        source++;
    }
    return counts == length ? !success : success;
}

void combine(char* destinction, char* source)
{
    char temp[2] = { 0 };
    while (*source)
    {
        if (!is_appeared(*source, destinction))
        {
            temp[0] = *source;
            strcat(destinction, temp);
        }
        source++;
    }
    return;
}


void prediction(void)
{
    noterminal* TEMP_NOTERMINAL = NOTERMINAL_HEAD.next;
    while (TEMP_NOTERMINAL != NULL)
    {
        find_first(TEMP_NOTERMINAL, PRODUCTION_HEAD.next);
        TEMP_NOTERMINAL = TEMP_NOTERMINAL->next;
    }
    test();

    TEMP_NOTERMINAL = NOTERMINAL_HEAD.next;
    while (TEMP_NOTERMINAL != NULL)
    {
        find_follow(TEMP_NOTERMINAL, PRODUCTION_HEAD.next);
        TEMP_NOTERMINAL = TEMP_NOTERMINAL->next;
    }
    return;
}

void test_follow(void)
{
    noterminal* TEMP_NOTERMINAL = NOTERMINAL_HEAD.next;

    for (; TEMP_NOTERMINAL != NULL; TEMP_NOTERMINAL = TEMP_NOTERMINAL->next)
    {
        printf("%c\tfollow number=%d\tfollow=%s\n",
            TEMP_NOTERMINAL->character,
            TEMP_NOTERMINAL->follow_number,
            TEMP_NOTERMINAL->FOLLOW);
    }
    printf("\n");
    return;
}

/******Begin******/
//生成预测分析表
void prediction_table(void)
{
    
}
/******End******/

void STACK_POP(void)
{
    if (STACK_EMPTY())
    {
        printf("栈空\n");
        emergency(2);
    }
    amount--;
    return;
}
size_t STACK_EMPTY()
{
    return amount == 0 ? success : !success;
}
size_t STACK_FULL()
{
    return amount == 19 ? success : !success;
}
void STACK_PUSH(char source)
{
    if (STACK_FULL())
    {
        printf("栈满\n");
        emergency(2);
    }
    TEST_STACK[++amount] = source;
    return;
}

void init_stack(void)
{
    amount = 0;
    TEST_STACK[amount] = '#';
    return;
}
```
#### 3.语法分析-递归分析
```

#include <iostream>
#include<stdio.h>
#include<cstring>
using namespace std;
char str[10];
int index_a = 0;
void E();			//E->TG;
void X();			//G->+TG | e
void T();			//T->FS
void Y();			//S->*FS | e
void F();			//F->(E) | i
int main()
{
	int len;
	cin >> str;
	len = strlen(str);
	str[len + 1] = '\0';
	E();
	cout << str;
	cout << "为合法符号串" << endl;
	index_a = 0;
	return 0;
}
/*******Beign*******/
/*构造递归关系及子程序*/
void E()
{

}
void X()
{

}
void T()
{

}
void Y()
{

}
void F()
{

}
/*******End*******/
```

#### 4.语法分析-算符优先
```
#include <iostream>
#include <fstream>
#include <sstream>
#include <vector>
#include <stack>
#include <iomanip>
using namespace std;
string V[100][2]; //存储拆分后的文法
int vi = 0; //存储拆分后有多少行
int t = 0;
int s = 0;
int l;
int r;
string FIRSTVT[20][2]; //存储firstvt集
string LASTVT[20][2]; //存储lastvt集
string str; //存储输入文法
string str_a = "#"; // 下堆栈
string str_b; // 剩余输入串
string analysis_table[100][5]; // 文法分析过程
char table[100][100]; // 算符优先关系表
void init_string(string &a) // 删除字符串的第一个元素
{
    for (int i = 1; i <= a.length(); ++i)
    {
        a[i - 1] = a[i];
    }
}
bool is_CHAR(char c) // 判断是否为大写字母
{
    if (c >= 'A' && c <= 'Z')
    {
        return true;
    }
    else
    {
        return false;
    }
}
bool is_in(int i, string x) // 判断从字符串x从最好一个开始算起连续的i个字符是否含有非大写字母
{
    bool flag = false;
    for (int j = 0; j < i; j++)
    {
        if (!is_CHAR(x[x.length() - j - 1]))
        {
            flag = true;
        }
    }
    return flag;
}
void split(string a) // 拆分文法，使其不含有｜
{
    for (int i = 3; i < a.length(); ++i)
    {
        V[vi][0] = a[0];
        while (a[i] != '|' && i < a.length())
        {
            V[vi][1] += a[i];
            i++;
        }
        vi++;
    }
}
void read_file(string file_path) //按行读取文件
{
    fstream f(file_path);
    vector<string> words;
    string line;
    while (getline(f, line))
    {
        words.push_back(line);
    }
    cout << "输入文法:" << endl;
    for (int i = 0; i < words.size(); i++)
    {
        cout << words[i] << endl;
        split(words[i]);
    }
}
int find_index(char a) //寻找字符a在firstvt或者lastvt中的位置
{
    for (int i = 0; i < t; ++i)
    {
        if (FIRSTVT[i][0][0] == a)
        {
            return i;
        }
    }
    return -1;
}
int find_table_index(char a) //寻找字符a在算符优先关系表中的位置
{
    for (int i = 0; i <= s; ++i)
    {
        if (table[i][0] == a)
        {
            return i;
        }
    }
    return -1;
}
void get_start() //获取非终结符
{
    for (int i = 0; i < vi; ++i)
    {
        bool flag = true;
        for (int j = 0; j < t; ++j)
        {
            if (FIRSTVT[j][0] == V[i][0])
            {
                flag = false;
            }
        }
        if (flag)
        {
            FIRSTVT[t][0] = V[i][0];
            LASTVT[t][0] = V[i][0];
            t++;
        }
    }
}
/********Beign********/
/*构造firstvt,lastvt*/
void add_firstvt(string b, int a) //判断字符串b是否在序号为a的firstvt中，没有则加入
{

}
void add_firstvt(char c, int a) //判断字符c是否在序号为a的firstvt中，没有则加入
{

}
void add_lastvt(string b, int a) //判断字符串b是否在序号为a的lastvt中，没有则加入
{

}
void add_lastvt(char c, int a) //判断字符串c是否在序号为a的lastvt中，没有则加入
{

}
string get_cur_firstvt(char c, int a) //获取当前字符的firstvt，并放入序号为a的firstvt中
{

}
string get_cur_lastvt(char c, int a) //获取当前字符的lastvt，并放入序号为a的lastvt中
{

}
/*********End*********/
void get_firstvt() //获取所有文法的firstvt
{
    for (int i = 0; i < t; i++)
    {
        get_cur_firstvt(FIRSTVT[i][0][0], i);
    }
}
void get_lastvt() //获取所有文法的lastvt
{
    for (int i = 0; i < t; i++)
    {
        get_cur_lastvt(LASTVT[i][0][0], i);
    }
}
void print_firstvt(string t, string a) //打印非终极符为t的firstvt
{
    cout << "FIRSTVT(" << t << ") = {";
    for (int i = 0; i < a.length(); ++i)
    {
        if (i == a.length() - 1)
        {
            cout << "\"" << a[i] << "\"";
        }
        else
        {
            cout << "\"" << a[i] << "\"" << ", ";
        }
    }
    cout << "}" << endl;
}
void print_lastvt(string t, string a) //打印非终结符为t的lastvt
{
    cout << "LASTVT(" << t << ") = {";
    for (int i = 0; i < a.length(); ++i)
    {
        if (i == a.length() - 1)
        {
            cout << "\"" << a[i] << "\"";
        }
        else
        {
            cout << "\"" << a[i] << "\"" << ", ";
        }
    }
    cout << "}" << endl;
}
/********Beign********/
/*构造算符优先表*/

void init_table() //初始化算符优先关系表
{

}

void get_table() //生成算符优先关系表
{

}
/*********End*********/
void print_table() //打印算符优先关系表
{
    for (int i = 0; i < s + 1; ++i)
    {
        for (int j = 0; j < s + 1; ++j)
        {
            cout << table[i][j] << " ";
        }
        cout << endl;
    }
}
char get_relationship(char a, char b) //获取终结符a，b的优先关系
{
    return table[find_table_index(a)][find_table_index(b)];
}
bool is_reduce() //判断是否可以规约
{
    for (int i = 0; i < vi; ++i)
    {
        int count = 0;
        int f = str_a.length() - 1;
        for (int j = V[i][1].length() - 1; j >= 0 && f >= 0; j--, f--)
        {
            if (is_in(V[i][1].length(), str_a))
            {
                if (is_CHAR(str_a[f]) && is_CHAR(V[i][1][j]))
                {
                    count++;
                }
                else if (str_a[f] == V[i][1][j])
                {
                    count++;
                }
            }
            else
            {
                continue;
            }
        }
        if (count == V[i][1].length())
        {
            r = i;
            return true;
        }
    }
    return false;
}
void analyze_input_string() // 生成算符优先文法的分析过程
{
    analysis_table[0][0] = "步骤";
    analysis_table[0][1] = "下堆栈";
    analysis_table[0][2] = "优先关系";
    analysis_table[0][3] = "剩余输入串";
    analysis_table[0][4] = "移进或规约";
    str_b = str;
    char relationship;
    l = 1;
    int x;
    stringstream ss;
    while (true)
    {
        ss << l;
        int index = str_a.length() - 1;
        analysis_table[l][0] = ss.str();
        analysis_table[l][3] = str_b;
        analysis_table[l][1] = str_a;
        ss.clear();
        ss.str("");
        if (is_CHAR(str_a[index]))
        {
            for (int i = str_a.length() - 1; i >= 0; i--)
            {
                if (!is_CHAR(str_a[i]))
                {
                    index = i;
                    break;
                }
            }
        }
        relationship = get_relationship(str_a[index], str_b[0]);
        analysis_table[l][2] = relationship;
        if (relationship == '=')
        {
            if (str_a[index] == '#' && str_b[0] == '#')
            {
                analysis_table[l][4] = "完成";
                break;
            }
            else
            {
                analysis_table[l][4] = "移进";
                str_a += str_b[0];
                analysis_table[l + 1][1] = str_a;
                init_string(str_b);
            }
        }
        else if (relationship == '<')
        {
            analysis_table[l][4] = "移进";
            str_a += str_b[0];
            analysis_table[l + 1][1] = str_a;
            init_string(str_b);
        }
        else if (relationship == '>')
        {
            if (is_reduce())
            {
                analysis_table[l][4] = "规约";
                str_a[str_a.length() - V[r][1].length()] = V[r][0][0];
                str_a.erase(str_a.length() - V[r][1].length() + 1, V[r][1].length() - 1);
            }
            else
            {
                cout << "输入串非法" << endl;
                exit(-1);
            }
        }
        l++;
    }
}
void print_analyze_process() //打印算符优先文法的分析过程
{
    cout << "算符优先分析过程" << endl;
    cout << setw(12) << analysis_table[0][0] << setw(16) << analysis_table[0][1] << setw(16) << analysis_table[0][2]
         << setw(24)
         << analysis_table[0][3] << setw(20)
         << analysis_table[0][4] << endl;
    for (int i = 1; i <= l; ++i)
    {
        cout.width(10);
        cout << analysis_table[i][0];
        cout.width(12);
        cout << analysis_table[i][1];
        cout.width(10);
        cout << analysis_table[i][2];
        cout.width(20);
        cout << analysis_table[i][3];
        cout << analysis_table[i][4];
        cout << endl;
    }
}
int main(int argv, char *arg[])
{
    cout.setf(std::ios::left);
    read_file("/data/workspace/myshixun/in.txt");
    cout << "拆分后文法:" << endl;
    for (int i = 0; i < vi; ++i)
    {
        cout << V[i][0] << "->" << V[i][1] << endl;
    }
    cout << "非终结符:" << endl;
    get_start();
    for (int j = 0; j < t; ++j)
    {
        cout << FIRSTVT[j][0] << endl;
    }
    cout << "FIRSTVT:" << endl;
    get_firstvt();
    for (int k = 0; k < t; ++k)
    {
        print_firstvt(FIRSTVT[k][0], FIRSTVT[k][1]);
    }
    cout << "LASTVT:" << endl;
    get_lastvt();
    for (int k = 0; k < t; ++k)
    {
        print_lastvt(LASTVT[k][0], LASTVT[k][1]);
    }
    cout << "算符优先关系表" << endl;
    init_table();
    get_table();
    print_table();
    cout << "请输入文法并以#结束:" << endl;
    cin >> str;
    analyze_input_string();
    print_analyze_process();
    return 0;
}
```
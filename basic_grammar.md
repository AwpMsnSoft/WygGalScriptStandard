# #version "0.1.1"


# WGS标准草案 基本语法篇

- 本标准草案中的指代不使用双引号，补充说明使用圆括号（），分行说明使用分号；。

## wgs的文件名:

- wgs脚本的文件名以.wgs结尾，即扩展名为.wgs。

## 引擎、解释器和通信:

- 解释器：负责接收引擎发出的脚本、信号，执行脚本中的语句，并将执行结果等信息发送给引擎。解释器既可以是客户端，又可以是服务端。
- 引擎：负责界面显示，资源获取、加载，文件格式转换，向解释器发送脚本和指挥信号。
- 通信：引擎和解释器之间应采用同步半双工方式通信，具体方式取决于实现。通信断开属于关键错误。

## 编码:

- 建议使用UTF-8或UTF-16LE编码，以防止乱码;
- 为保证脚本能够被解释器正常识别，由引擎对脚本进行编码转换后发送给解释器。

## 脚本文件读取:

- 脚本可以在运行前读取，也可在运行过程中读取，由引擎负责；
- 运行过程中，任何相关脚本文件内容不应发生变化；
- 运行过程中，脚本文件内容发生变化，或增加/删除脚本文件，均为未定义行为；
- 脚本文件无法读取属于关键错误。

## 未定义行为:

- 脚本中未规定具体结果的行为是未定义行为，未定义行为的结果依赖脚本解释器和引擎的具体实现；
- 脚本在调试模式下执行时，发现未定义行为应给出警告；
- 脚本在非调试模式下执行时，可根据具体情况决定是否给出警告；
- 除非特别说明，未定义行为不视为关键错误。

## 关键错误:

- 本标准规定的导致引擎或解释器不能继续运行的问题为关键错误；
- 脚本解释器执行到关键错误处时必须终止执行，并将结果反馈给引擎。对于服务端解释器，应恢复到初始状态。
- 除非特别说明，引擎读取、解释器预处理脚本时，发现关键错误不进行处理，只给出警告。

## 分行：

- 脚本以行为最小单元，使用默认的换行符为结束符；
- 一行中的不同关键字用一个或多个空格和Tab分隔；
- 在用一对双引号表示的文本中，空格和Tab不作为分隔符；
- 允许行首和行尾存在一个或多个空格和Tab，如果存在，这些空格和Tab在读入后被修剪;

## 注释:
-  若一行以##开头，则该行为注释行；
-  若一行以#:开头，且后续有以:#结尾的行，则该行到距离最近的以:#结尾的行作为注释行；后续没有以:#结尾的行，则该行和后续所有行均为注释行；
-  注释行不会被执行；
    ```
    ##这是单行注释
    #:从这一行开始
    多行注释
    到这一行结束:#
    ```

## 非文本字符的通用规则：

- 在用一对双引号表示的文本外，大写英文字母和小写英文字母通用，即不区分大小写；
- 除此之外，任何字符非通用，区分大小写。

## 数据类型:
数据类型有4种：默认（待定）、布尔（真假）、数（实数）、文本（字符串）。

### 默认（Default）：

- 默认类型表示待定或未定义值，只存在默认（Default）一个值.

### 布尔（Boolean）：

- 布尔类型用于表示真假，只存在真（True）、假（False）两个值；
- 布尔类型非真即假，非假即真。

### 数（Number）
- 数可以是整数或小数形式的，在脚本中必须用十进制表示，除了小数点和正负符号外，不能含有其他符号；
- 正负符号用+、-表示，正号默认省略；
- 小数点使用.表示；
- 数据精度不低于IEEE754标准规定的双精度浮点数（即float64 或 double）；
- 将数量级相差悬殊的两个数字相加或相减，结果不准确，为未定义行为；
- 数可以比较大小，具有误差（Eps），误差必须为小于0.5的非负数，且必须可用IEEE754标准规定的双精度浮点数精确表示，默认的误差是1/1048576，两个数差的绝对值小于误差时视为等于；
- 通过有关指令设置误差；
- 设置不能够用IEEE754标准的双精度浮点数精确表示的误差，是影响极小的未定义行为，结果极小概率不同；
- 设置大于等于0.5的误差或负数误差，是未定义行为；
- 有各种取整指令对数进行取整，详见取整指令。

### 文本（Text / String）：
- 文本类型表示一段文本，也叫字符串，在脚本中表示时，必须位于一对双引号""内，对应的实际文本没有两端的双引号（例如："abc"表示文本abc）；
- 文本可以不含任何字符，长度为0，在脚本中用""表示；
- 在脚本中表示的文本，不以双引号开头是未定义行为；以双引号开头的文本不以双引号结尾，是关键错误；
- 在用一对双引号表示的文本中，出现\（反斜杠）和^（Caret）是未定义行为，因为它们是保留的。
#### 文本保留常量：
- 一些特殊字符可能无法在双引号中表示，为解决这个问题，引入文本保留常量：
```  
 $ENTER 表示换行符的文本常量
 $TAB表示制表符的文本常量
 $DOUBLEQUOTE 表示双引号的文本常量
 $BACKSLASH表示反斜杠的文本常量
 $CARET 表示Caret的文本常量

 通过+运算连接多个文本使用，例如 $DOUBLEQUOTE+"abc"+$DOUBLEQUOTE 
```
- 修改文本保留常量是未定义行为。

## 自动类型转换:
- 自动类型转换是一种临时（Temporary）转换，共3种，按照优先级顺序，依次为： 
  - 如果某个操作需要Text，但是提供Boolean，则Boolean可表示Text，True表示\"True\"，False表示\"False\"；
  - 如果某个操作需要Number，但是提供Boolean，则Boolean可表示Number，True表示1，False表示0；
  - 如果某个操作需要Text，但是提供Number，则Number可表示Text，为对应数值的十进制文本，且在表示时会先在以误差为半径的邻域内取值，使整个文本尽可能短，但是绝对值小于1的小数不省略小数点前面的0；如果有多个取值可使文本长度最短，取距离原数最近的值，接下来如果仍然有两个可取值，取绝对值较小的一个值。

### 不允许的自动类型转换：
- 发生了不允许的自动类型转换是关键错误：
  - 需要Boolean，但是提供Number；
  - 需要Boolean，但是提供Text；
  - 需要Number，但是提供Text。

#### 要强制转换，需要使用比较或指令，详见比较和转换指令。 

# 基本运算
- 基本运算是指仅使用基本运算符的运算。

## 基本运算符：
- 基本运算符是用于对数据进行运算获得结果数据的符号，包括正负运算符（+、-）
算术运算符（ + 、- 、* 、\/ 、\ 、\% ）、布尔运算符（ \& 、\| 、\~或! 、\^ ）、比较运算符（ > 、\< 、 >\= 、\<\= 、 \= 或 \=\=、 <> 或 != ）、文本连接运算符（ + ）；
- 不包括位运算符等其他运算符，使用其他运算符为未定义行为。

### 运算符功能表:
|形式|A的类型|B的类型|结果类型|说明|中文说明|
|:----:|:----:|:----:|:----:|:----:|:----:|
|+A|Number||Number|Positive|取正数|
|-A|Number||Number|Negative|取负数|
|A+B|Number|Number|Number|Addition|加法|
|A+B|Text|Text|Text|Connection|连接|
|A-B|Number|Number|Number|Subtraction|减法|
|A*B|Number|Number|Number|Multiplication|乘法|
|A/B|Number|Number|Number|Division|除法|
|A\B|Number|Number|Number|the Integer part of (A/B)|除法取整|
|A\%B|Number|Number|Number|A-(B*(B\A))|余数/取模|
|A\&B|Boolean|Boolean|Boolean|And|与|
|A\|B|Boolean|Boolean|Boolean|Or|或|
|A\^B|Boolean|Boolean|Boolean|Xor|异或|
|~A<br>!A|Boolean||Boolean|Not|非|
|A>B|Number|Number|Boolean|More than|大于|
|A>\=B|Number|Number|Boolean|More than or equals|大于等于|
|A<B|Number|Number|Boolean|Less than|小于|
|A<=B|Number|Number|Boolean|Less than or equals|小于等于|
|A=B<br>A==B|Boolean|Boolean|Boolean|Equals|等于|
|A<>B<br>A!\=B|Boolean|Boolean|Boolean|Not equals|不等于|
|A=B<br>A==B|Number|Number|Boolean|Equals|等于|
|A\<>B<br>A!\=B|Number|Number|Boolean|Not equals|不等于|
|A=B|Text|Text|Boolean|Equals(English is case insensitive)|等于（不区分英文大小写）|
|A==B|Text|Text|Boolean|Equals(English is case sensitive)|等于（区分英文大小写）|
|A<>B|Text|Text|Boolean|Not equals(English is case insensitive)|不等于（不区分英文大小写）|
|A!=B|Text|Text|Boolean|Not equals(English is case sensitive)|不等于（区分英文大小写）|

- 文本比较不区分大小写仅对英文生效，对其他语种是未定义行为；
- 使用基本运算符，形式不符合上表是关键错误；
- 使用基本运算符，若A或B的类型不符合上表，则会发生自动类型转换。发生不允许的自动类型转换为关键错误。
### 操作数含有Default类型的基本运算符功能表：
|形式（运算符以空格分隔并列）|A的类型|B的类型|结果类型|结果|中文说明|
|:----:|:----:|:----:|:----:|:----:|:----:|
|+ - ~ !A|Default||Default|Default|Default运算不变|
|A+ - * / \ % & \| ^B|Default|Default|Default|Default|Default与Default运算不变|
|A= ==B|Default|Default|Boolean|True|Default相等|
|A!= <>B|Default|Default|Boolean|False|Default非不相等|
|A+B|Text|Default|Text|A|Default无作用|
|A& \| ^B|Boolean|Default|Boolean|A|Default无作用|
|A+ - * /B|Number|Default|Number|A|Default无作用|
|A\B|Number|Default|Number|the Integer part of A|Default作为1|
|A%B|Number|Default|Number|the Fractional part of A|Default作为1|
|A+B|Default|Text|Text|B|Default无作用|
|A& \| ^B|Default|Boolean|Boolean|B|Default无作用|
|A+ *B|Default|Number|Number|B|Default无作用|
|A-B|Default|Number|Number|-B|Default无作用|
|A/ \ %B|Default|Number|Number|0|Default作为0|
|A> >= < <= = == != <>B|Number|Default|Boolean|B is replaced by 0|Default作为0|
|A> >= < <= = == != <>B|Default|Number|Boolean|A is replaced by 0|Default作为0|
|A= == != <>B|Boolean|Default|Boolean|B is replaced by False|Default作为False|
|A= == != <>B|Default|Boolean|Boolean|A is replaced by False|Default作为False|
|A= == != <>B|Text|Default|Boolean|Whether or not A is equal to "Default" or "False" or "0" or ""|判断A是否为常见默认文本|
|A= == != <>B|Default|Text|Boolean|Whether or not B is equal to "Default" or "False" or "0" or ""|判断B是否为常见默认文本|
- =和<>不区分英文大小写，对其他语种是未定义行为，==和!=区分英文大小写；
- 上表可总结为：Default类型总是尽可能小地影响所参与运算的结果；
- 使用基本运算符且操作数中含有Default类型，若A或B的类型不符合上表，则会发生自动类型转换。发生不允许的自动类型转换为未定义行为。

### 运算优先级表： 

#关键字
##指令:
- 指令是表示执行一段过程的关键字，以.开头，形如：.command
  
##变量:
- 变量是用于存储一个数据单元的标识符，以\$开头，形如：$var ；
- 所有变量均为全局变量。

##数组变量:
- 数组变量是用于存储多个数据单元的标识符，以[\]开头，形如：[\]arr ；
- 数组至少含有一个元素；
- 数组的长度是可变的；
- 数组的下标范围为 **0** 到 **数组长度-1** ；
- 数组的下标为 **i** 的元素使用 **[i]arr**表示;
- 数组越界指访问数组超出了下标范围，是关键错误；
- 允许同一个数组的不同元素类具有不同类型；
- 定义多维数组，或者使数组成为另一个数组的元素，是未定义行为；
- 所有数组变量均为全局数组变量。

##常量标识符：
- 常量标识符表示不可变的数据单元，在形式上具有与变量完全相同的表示方法，例如：\$CONST、[]CONSTARR、[0]CONSTARR ；
- 对常量标识符的写操作是未定义行为；
- 所有常量标识符均为全局常量标识符。


## 表达式： 
- todo 待补充：
- 基本运算表达式，复杂运算表达式，数组表达式
- 包括单个数据和单个数据，数组和数组，数组和单个数据，单个数据和数组之间的运算

## 非注释行：
- 普通执行行 
  ```
  ##执行无参数的指令:
  .command

  ##执行有参数的指令，分隔符使用若干个空格或Tab，用法举例:
  .command  parameter_0
  .command  parameter_0  parameter_1
  .command  parameter_0  ...  parameter_n-1

  ##传递数组，批量执行有参数的指令（试验性功能，须指令支持批量执行），分隔符使用若干个空格或Tab，用法举例:
  
      .[]command  ...  
      .[][]command  ...

      #:
          对[]param_arr_0中的每个元素依次执行
          .command [i]param_arr_0 
      :#
      .[]command  []param_arr_0


      #:
          对[]param_arr_0中的每个元素依次执行
          .command [i]param_arr_0  parameter_1   
      :#
      .[]command  []param_arr_0  parameter_1  


      #:
          取得[]param_arr_0、[]param_arr_1的最大长度，
          并将不足该长度的数组使用Default扩充使数组长度相等，
          对[]param_arr_0、[]param_arr_1中的每组元素依次执行
          .command [i]param_arr_0  [i]param_arr_1
      :#
      .[]command  []param_arr_0  []param_arr_1 
 

      #:
          取得[]param_arr_0、[]param_arr_1的最大长度，
          并将不足该长度的数组使用Default扩充使数组长度相等，
          对[]param_arr_0、[]param_arr_1中的每组元素依次执行
          .command [i]param_arr_0  [i]param_arr_1  parameter_2  
      :#
      .[]command []param_arr_0  []param_arr_1  parameter_2


      #:
          对[]param_arr_0、[]param_arr_1中的每个元素按照i在外部，j在内部的i、j二重循环，依次执行
          .command [i]param_arr_0  [j]param_arr_1
      :#
      .[][]command  []param_arr_0  []param_arr_1 
  ```


- 普通赋值行
  ```
  ##变量赋值行，将第一个=右边表达式计算结果赋值给左边的变量，关键字允许用若干个空格或Tab分隔：
  $var = expression 

  ##数组单个元素赋值行，将表达式的值赋值给数组中下标为i的元素：
  [i]arr = expression
  ```
- 数组赋值行
  ```
  ##将n个表达式组合成具有n个元素的数组，赋值给第一个=左边的数组变量：
      ##数组可以只拥有一个元素，但不可以没有元素
      []arr = expression_0

      ##表达式有多少个，数组就有多少个元素，多个表达式之间使用逗号分隔
      []arr = expression_0,expression_1
      []arr = expression_0,...,expression_n-1
  ```

- 文本连接赋值行
  ```
  ##将+=左边变量类型转换为文本，计算+=右边表达式的值，再将文本与表达式进行连接成新的文本存入变量中：
  $var += expression 
  [i]arr += expression
  
  ##todo
  ##以下三种文本连接赋值行的行为待补充
  []arr += []brr
  []arr += expression
  $var += []brr
  ```

- 预处理行
  ```
  ##预处理行表示解释器必须在执行脚本之前，预先处理该行，通常以#开头
  ##预处理行仅在当前上下文中有效；

  #label
  ##普通标签（label），用于标记跳转位置，在同一个wgs中定义重名的标签是未定义行为。
  
  #start
  ##入口点标签（start label），如果定义了入口点标签，则脚本执行从入口点标签处开始，而不是脚本的第一行。
  
  #version "0.1.1"
  ##版本字符串（version string label），如果在脚本中定义了版本字符串（通常在第一行），
  则检查当前wgs版本与解释器、引擎是否兼容，不兼容为关键错误。版本字符串不能用于跳转。
  
  #include "filepath"
  ##类似于C语言的#include宏，在当前位置插入另一段文本

  #.command 
  ##预处理指令，脚本载入后执行前，解释器按照整改wgs文件按照从上往下的顺序率先执行的指令，仅支持少量指令。

  ```

#!version "0.1.0"


本标准草案中的指代不使用双引号，补充说明使用圆括号（），句内的并列词之间不使用逗号和顿号，分行说明使用分号；。

文件名（File Name）：
文件名以.wgs结尾，即扩展名为.wgs，此四个字符均为半角字符 （Half-Width）。
不符合文件名要求的脚本文件无法载入。


编码（File Encoding）：
脚本文件保存为UTF-16LE（1200，使用HexEditer查看时，文件带BOM，第一个字节（First Byte）为0xFF，第二个字节（Second Byte）为0xFE，或者读入的第一个 wchar_t是0xFEFF）。
不符合编码要求的脚本文件无法载入。

脚本文件读取（Script Reading）
脚本可以在运行前读取，也可在运行过程中读取，取决于如何实现；
运行过程中，任何相关脚本文件不应发生变化；
运行过程中，脚本文件内容发生变化，或增加/删除脚本文件，均为未定义行为；
脚本文件无法载入属于致命错误。

未定义行为（Undefined Behavior）：
脚本中未规定具体结果的行为是未定义行为；
未定义行为的结果依赖脚本解释器的具体实现；
脚本在调试（Debug）模式下执行时，发现未定义行为应给出警告（Warning）；
脚本在非调试模式下执行时，可根据具体情况决定是否给出警告；
除非特别说明，未定义行为不视为致命错误。

致命错误（Fatal Error）：
本标准规定的导致脚本不能继续运行的问题为致命错误；
脚本执行到致命错误处时必须终止执行（Stop），并警告。
除非特别说明，脚本载入时，发现致命错误不进行处理，只给出警告。


脚本的wgs版本信息（Version）：
在首行用 #!version "x.y.z" 表示，其中 #!". 必须为半角字符（0x0023）（0x0021）（0x0022）（0x002E），其中 x y z 均为不含任何标点符号的十进制自然数，且在数字不为0时没有前导零，在数字为0时仅用一个字符0表示；
首行无wgs版本信息是致命错误，且脚本载入时应终止执行，并警告。

执行（Execution）：
脚本的执行以行（Line）为单元进行；
一行中的不同关键字用一个或多个空格（0x0020）/ Tab（0x0009）（Space Characters）隔开；
行首和行尾允许存在一个或多个空格/ Tab，如果存在，这些空格/ Tab在载入后被修剪（Trim）;

非文本字符的通用规则（Synonymous Characters out of Text）：
大写（Uppercase）英文字母和小写（Lowercase）英文字母通用。
.     。（0x002E） （0x3002）2种句点（Period）通用；
"“    ”（0x0022）（0x201C）（0x201D）3种双引号（Double Quote ）通用；
(（   （0x0028）（0xFF08）2种左圆括号（Open Parentheses ）通用；
)    ）（0x0029）（0xFF09）2种右圆括号（Close Parentheses ）通用。

表示文本的""内的字符不通用（No Synonymous Characters in Text）。

数据类型（Data Type）：
数据类型有3种，布尔（Boolean） 数（Number） 和文本（Text）。

布尔（Boolean）：
布尔类型用于表示真假，只存在真（True） 假（False）两个值；
非真即假，非假即真。

数（Number）：
数可以是整数或小数，在脚本中必须用十进制表示，除了小数点和正负符号外不能含有其他符号；
正负（Sign）符号用+（0x002B）-（0x002D）表示，正号可以省略；
小数的小数点（Decimal Point）使用.（0x002E） 表示；
数据精度不低于IEEE754标准的双精度浮点数（float64 或 double）；
数可以比较大小（Compare），具有误差（Eps），误差必须为小于0.5的非负数，且必须可用IEEE754标准的双精度浮点数精确表示，默认的误差是1/1048576，两个数差的绝对值小于误差时视为等于；
设置不可用IEEE754标准的双精度浮点数精确表示的误差是影响极小的未定义行为，结果可能会因数据精度而异；
设置不小于0.5的或者为负数的误差，是未定义行为；
有各种取整指令对数进行取整，详见取整指令（Integer Command）。

文本（Text）：
文本类型表示一段文本，在脚本中表示时，必须放在一对双引号内，对应的实际文本没有两端的双引号（例如："abc"表示文本abc）；
在脚本中表示的文本内，可以使用\（0x005C）和^（0x005E）对文本进行转义（Escape）；
文本可以不含任何字符，长度为0，在脚本中用""表示（Empty Text）；
在脚本中表示的文本，不以双引号开头（Not Start with "），或不以未被转义的双引号结尾（No Matched "），是致命错误。

转义字符（Escaped Characters）：
在脚本""内表示的文本中，未被转义的\及其后一个相邻字符（Next Character）为转义字符，用于表示脚本文本中无法直接表示或难以分辨的字符；
\n表示换行	 \t表示Tab 	\s表示空格	\\表示\        ；
\^表示^		 \"表示"		 \“表示“ 	 \”表示”  ；
转义字符中\右侧的相邻字符为其他字符（\ with Other Character），是未定义行为；
未被转义的\右侧没有字符（\ with No Character），是致命错误。

转义文本（Escaped Text）：
在脚本""内表示的文本中，未被转义的^及其后一个^之间的部分（包括两端的^）为转义文本；
转义文本去除两端的^后，和实际文本一致，即其中的\不用于转义，双引号也不表示文本的结束；
转义文本为^^时，对应的实际文本不包含任何字符，长度为0；
未被转义的^之后的部分中没有^（No Matched ^），是致命错误。

举例（For Example）
"\^awpsoft：^"学院派都是瓜。\n"\^\n\^msneko：\"实用主义者都是无头苍蝇\^。\t\\n^"^^^" 
表示的实际文本为：

^awpsoft："学院派都是瓜。\n"\
^msneko："实用主义者都是无头苍蝇^。	\n"

上述文本表示中，转义内容依次为  
\^（转义字符）	 ^ "学院派都是瓜。\n"\^（ 转义文本）	\n（转义字符）   
\^（转义字符）	 \"（转义字符）	 \^（转义字符）	\t （转义字符） 
\\（转义字符）   	^"^（转义文本）	^^（转义文本）。

自动类型转换（Auto Convert）：
自动类型转换是一种临时（Temporary）转换；
如果某个操作需要（Require）Number，但是提供（Deliver）Boolean，则Boolean可表示Number，True表示1，False表示0；
如果某个操作需要Text，但是提供Boolean，则Boolean可表示Text，True表示"True"，False表示"False"；
如果某个操作需要Text，但是提供Number，则Number可表示Text，为对应数值的十进制文本，且在表示时会先在以误差（Eps）为半径的邻域内取值，使整个文本尽可能短，但是绝对值小于1的小数不省略小数点前面的0；如果有多个取值可使文本长度最短，取距离原数最近的值，接下来如果仍然有两个可取值，取绝对值较小的一个值（Error Less than Eps > Shortest Length > Nearest > Smaller Scale）。

不允许的自动类型转换（Disabled Auto Convert）有三种：
需要Boolean，但是提供Number；
需要Boolean，但是提供Text；
需要Number，但是提供Text；
发生了不允许的自动类型转换是致命错误。

要强制转换（Force Convert），需要使用比较或指令，详见比较（Compare）和转换指令（Convert Command）。

基本运算符（Basic Operators）：
基本运算符是用于对数据进行运算获得结果数据的符号，包括
算术运算符（Number Operators） +（0x002B）-（0x002D） *（0x002A）  /（0x002F）\ （0x005C）%（0x0025）；

比较运算符

布尔运算符（Boolean Operators）&（0x0026）|（0x007C）~（0x007E）^（0x0025）；

文本连接运算符（Text Append Operator）+ （0x002B）；
详细功能如下表:
形式	A的类型	B的类型	结果类型		说明				
Form	Type of A	Type of B	Type of Result	Descriptions
A+B	Number	Number	Number		Addition
A+B	Text	Text	Text		Connection 
A-B	Number	Number	Number		Subtraction
A*B	Number	Number	Number		Multiplication
A/B	Number	Number	Number		Division
A\B	Number	Number	Number		Integer part of (A/B)
A%B	Number	Number	Number		A-(B*(B\A))
A&B	Boolean 	Boolean 	Boolean 		And
A|B	Boolean 	Boolean 	Boolean 		Or
A^B	Boolean 	Boolean 	Boolean	 	Xor
~A	Boolean		Boolean		Not




使用基本运算符时，形式不符合上表是致命错误；
使用基本运算符时，类型不符合上表会发生自动类型转换，发生了不允许的自动类型转换是致命错误。



表达式（Expression）：





有效行（Valid Line）
脚本中只有满足下列格式的行才视为有效行，否则为无效行：
.command 		（无参数或省略参数的标准指令）
.command  parameters  	分隔符为至少一个空格或Tab（有参数标准指令）
.~command 		（无参数或省略参数的非标准指令）
.~command  parameters 	分隔符为至少一个空格或Tab（有参数非标准指令）
$var = expression		分隔符为第一个=（赋值语句）	
#label			（标签）
#start			（入口点标签，一个脚本文件只能有一个，有多个入口点为关键错误）
#!pragma			（杂注）
#!pragma  comment		分隔符为至少一个空格或Tab（杂注）

保留的关键错误行：
$var += expression		分隔符为唯一的+=（$var = $var + expression）	
$var -= expression		分隔符为唯一的-=（$var = $var - expression）	
$var *= expression		分隔符为唯一的*=（$var = $var * expression）
$var /= expression		分隔符为唯一的/=（（$var = $var / expression）	
$var \= expression		分隔符为唯一的\=（$var = $var \ expression）	
$var %= expression		分隔符为唯一的%=（$var = $var % expression）
$var &= expression		分隔符为唯一的&=（$var = $var & expression）
$var |= expression		分隔符为唯一的|=（$var = $var | expression）
$var ^= expression		分隔符为唯一的^=（$var = $var ^ expression）
$var++			（$var = $var + 1）
$var--			（$var = $var -  1）	

















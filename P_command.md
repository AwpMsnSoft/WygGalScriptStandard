# 流程控制指令（P指令）

## 分支判断指令（PIF指令）:
- **本文档并非包括所有分支判断指令。**  
--------
- wgs的使用者分别理解每个分支判断指令的详细说明是没有必要的，可以直接套用以下格式，请合理使用缩进以保证wgs层次明确： 

- .if和.endif的用法
    ```
    ##若$state为真，执行##do_things处的语句，否则跳过：
    .if $state
        ##do_things
    .endif
    ```
- 逻辑运算和嵌套的用法
    ```
    ##只有$state_1和$state_2全部为真，才执行##do_things处的语句，否则跳过：
    .if $state_1&$state_2
        ##do_things
    .endif
    ##允许嵌套，也可以这样：
    .if $state_1
        .if $state_2
            ##do_things
        .endif
    .endif
    ```
    ```
    ##若$state_1和$state_2至少有一个为真，执行##do_things处的语句，否则跳过：
    .if $state_1|$state_2
        ##do_things
    .endif
    ```
    ```
    ##只有$state_1和$state_2全部为假，才执行##do_things处的语句，否则跳过：
    .if !$state_1&!$state_2
        ##do_things
    .endif
    ## !运算优先级最高，可以使用括号：
    .if !($state_1|$state_2)
        ##do_things
    .endif
    ```
- .else 的用法    
    ```
    ##若$state为真，执行##do_things_1处的语句，否则执行##do_things_2处的语句：
    .if $state
        ##do_things_1
    .else
        ##do_things_2
    .endif
    ```
- .elseif 的用法
    ```
    ##若$state_1为真，执行##do_things_1处的语句，
    ##否则若$state_2为真则执行##do_things_2处的语句，
    ##否则若$state_3为真则执行##do_things_3处的语句，
    ##否则执行##do_things_4处的语句：

    .if $state_1
        ##do_things_1
    .elseif $state_2
        ##do_things_2
    .elseif $state_3
        ##do_things_3
    .else
        ##do_things_4
    .endif

    ##可以改写成嵌套形式：
    .if $state_1
        ##do_things_1
    .else
        .if $state_2
            ##do_things_2
        .else
            .if $state_3
                ##do_things_3
            .else
                ##do_things_4
            .endif
        .endif    
    .endif

    ```


### 以下是分支判断指令的详细说明:
--------
### **.if**  
> 概述：这是一个.if类指令，判断表达式的值是否为真，以进入下方不同的分支。   


- 参数列表
    |参数名称|默认值|文本|数|布尔|数组|输入|输出|表达式空格|
    |:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
    |BoolExpr|False|||✔||✔||✔|

- 指令说明：
    - 根据**BoolExpr**的值决定：   
    **True**：继续执行，直至遇到匹配的.else类或.endif指令，再从匹配的.endif指令继续；   
    **False**：跳转到下一个匹配的.else类或.endif指令。 
--------
### **.else**  
> 概述：这是一个.else类指令，可以作为最后一个分支开始的标记。 
- 参数列表
    |参数名称|默认值|文本|数|布尔|数组|输入|输出|表达式空格|
    |:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
    |无|||||||||
    
- 指令说明：
    - 与前一个未被.endif匹配的.if类指令匹配。 
--------
### **.endif**  
> 概述：这是最后一个判断分支结束的标记。 
- 参数列表
    |参数名称|默认值|文本|数|布尔|数组|输入|输出|表达式空格|
    |:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
    |无|||||||||
    
- 指令说明：
    - 与前一个尚未被.endif匹配的.if类且非.else类指令匹配，并且与二者之间所有尚未被.endif匹配的.if类和.else类指令匹配。
        
--------
### **.elseif**  
> 概述：这是一个.else类或.if类指令，该指令未被跳过时，判断表达式的值是否为真，以进入下方的不同分支。 
- 参数列表
    |参数名称|默认值|文本|数|布尔|数组|输入|输出|表达式空格|
    |:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
    |BoolExpr|False|||✔||✔||✔|

- 指令说明：
    - 根据**BoolExpr**的值决定：   
    **True**：继续执行，直至遇到匹配的.else类或.endif指令，再从匹配的.endif指令继续；   
    **False**：跳转到下一个匹配的.else类或.endif指令。 
--------


## 循环控制指令(PLOOP指令)
- **本文档并非包括所有循环控制指令。**  
--------  
- 使用循环控制指令可以反复执行一些语句，可以直接套用以下格式，请合理使用缩进以保证wgs层次明确：

- .loop和.endloop的用法：
    ```
    ##.loop为循环的开头，.endloop为循环的结尾，循环即反复执行##repeat_do_things处的语句，
    ##当执行到.endloop时，会回到.loop的位置：
    .loop
        ##repeat_do_things
    .endloop
    ```
- .continue和循环内嵌套判断的用法：    
    ```
    ##若执行.continue，则立即回到当前循环的.loop处，
    ##可以通过判断$state是否为True来决定是否提前开始下一轮循环：
    .loop
        ##script_1
        .if $state 
            ##do_things_before_continue
            .continue
        .endif
        ##script_2
    .endloop
    ```
- .break的和循环内嵌套判断的用法：    
    ```
    ##若执行.break，则立即结束循环，跳到当前循环的.endloop之后，
    ##可以通过判断$state是否为True来决定是否退出当前循环：
    .loop
        ##script_1
        .if $state 
            ##do_things_before_break
            .break
        .endif
        ##script_2
    .endloop
    ```
- 反复执行有限次数的循环可通过变量和.break实现：
    ```
    ##执行##repeat_do_things处的语句100次：
    .defvar $i 0
    .loop
        .if $i>=100
            .break
        .endif
        ##repeat_do_things
        $i = $i+1
    .endloop    
    .undef $i
    ```
- 循环允许嵌套，例如：    
    ```
    ##执行##repeat_do_things处的语句1+2+3+4+...+100次：
    .defvar $i 0
    .defvar $j
    .loop
        .if $i>=100
            .break
        .endif
        $j = 0
        .loop
            .if $j>$i
                .break
            .endif
            ##repeat_do_things
            $j = $j+1
        .endloop
        $i = $i+1
    .endloop
    .undef $j    
    .undef $i
    ```


## 跳转指令 (PCT指令)





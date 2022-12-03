# 保留字
- 保留字不允许作为用户定义常量、变量、数组的名称，否则是关键错误。

# 包括
## 保留关键字：
    Default
    True
    False
    
    Type

    Char
    Text
    String
    Boolean
    Bool
    Number
    Integer
    Fraction
    
    Global
    Local

    Language
    Locale

    Unicode
    UTF8
    UTF16
    UTF16LE
    UTF16BE
    UTF32

    Start
    Main

    Debug
    Release

## 只读保留变量：
    $Result
    $CurrentLine
    $CurrentWgs
    $Data
    $Time
    
    []WindowSize
    []ZoomSize
    []Pair
    []CurrentStacks

## 保留常量
    $SPACE
    $ENTER
    $TAB
    $DOUBLEQUOTE
    $BACKSLASH
    $CARET 

## 保留指令
    .goto

    .end
    
    .do
    .while
    .wend

    .if*
    .else*
    
    .winapi
    .posix

## 特殊保留指令

    .system
    .cmd
    .powershell
    .runas
    .run
    .exec
    .bash
    .sudo
    .su
    .rm
    .rmdir
    .del
    
## 以及 标准指令集中的全部指令。
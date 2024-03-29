---
layout:     post
title:      Series Article of UbuntuOS -- 05 
subtitle:   linux中正则表达的一点儿小知识     
date:       2021-05-08
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Ubuntu OS
---      

##   正则表达式基础表

|表达式（md中是否需要 \\ 转义）|描述|示例|    
|:---|:---|:---|
|^（否）|行起始标记|^tux 匹配以 tux 起始的行|    
|\$（是）|行尾标记|tux\$ 匹配以 tux 结尾的行|    
|\.（是）|匹配单个字符| Hack\. 可以匹配 Hackz 或 Hack7，但不能匹配Hack23 或 Hacka0|
|\[\]（是）|匹配方括号内单个字符|Hack\[b4\]只能匹配 Hackb 或 Hack4|   
|\[^（否）\]|匹配除方括号内的任意单个字符|Hack\[^b4\]可以匹配 Hack0, Hackz等等，无法匹配 Hackb, Hack4这两项|    
|\[\-（是）\]|匹配连字符范围内的任意单个字符|\[1\-5\]可以匹配1,2,3,4,5之中的任意一个字符|
|?（否）|匹配之前的项一次或零次|colou?r可以匹配color, colour，但无法匹配colouur|
|\+（是）|匹配之前的项一次或多次|a\-1+ 可以匹配a\-1, a\-111，但不可匹配a\-|
|\*（是）|匹配之前的项任意次|co\*l 可以匹配 cl, col, cool, coooool|    
|\(\)（是）|穿件一个用于匹配的子串|ma\(tri\)x 可以匹配 max 或 matrix |
|\{n\}（是）|匹配前项n次|\[0\-9\]\{3\} 匹配任意一个三位数，等效于\[0\-9\]\[0\-9\]\[0\-9\]|
|\{n,\}（是）|匹配前项至少n次|\[0\-9\]\{2,\} 匹配一个两位或两位以上的数|   
|\{n,m\}（是）|匹配前项至少n次之多m次|\[0\-9\]\{2,5\} 匹配一个两位或三位或四位或五位数|    
|\\|（是）| a\\\.b 用以匹配 a\.b 而不是 a\(任一个字符\)b，转义符号\\ 消除了 \. 的特殊含义|   



## 注意点    
使用正则表达式的时候一定要注意`. [ \ * ^ $`这六个特殊字符：   
* 点号 `.` 可以匹配任意**一个**字符，但不包括空字符NUL和结尾换行符。    
* 反斜线`\` 的特殊含义是对特殊字符进行转义，表示特殊字符自身。例如，点号 `.` 具有特殊含义，如果要匹配点号 `.` 这个字符，需要写为 `\.` 。需要匹配反斜线 `\` 的时候，它自身也需要被转义，写为 `\\` 。     
* 星号 `*` 是通配符，可以匹配零个或一个或连续多个字符。例如，`a*`可以匹配`a`、`ab`、`acdft&`等。    

以上三者的特殊含义只有在方括号`[]`外时 work。

* `^`出现在方括号 `[]` 表达式之外，用于表示匹配字符串开头，例如，`^ab` 表达式可以匹配 abcdef 字符串开头的 ab，但不匹配 cdefab 字符符末尾的 ab；出现在方括号 `[]` 表达式开头的第一个字符，紧跟在左大括号 `[` 之后，表示不匹配方括号内的所有字符。例如，`[^abc]` 表达式不匹配字符 a、b、c，会匹配这三个字符之外的任意一个字符。     
* `$` 字符只有出现在整个正则表达式末尾的最后一个字符时才有特殊含义，用于表示匹配字符串末尾。例如，`ab$` 表达式可以匹配 cdefab 字符串末尾的 ab，但不匹配 abcdef 字符串开头的 ab。     
* 当同时使用 `^ 和 $` 时，可以全词匹配一个字符串。例如，`^abcdef$` 表达式只能匹配 abcdef 这个字符串。     



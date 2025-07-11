
---
title: "魔兽程序语言 JASS"
---

## 简介

`JASS 2`是魔兽3的程序语言，用于控制游戏和地图的进行，也是魔兽游戏和地图的基础。地图编辑器中摆放的单位(Unit)，区域(Region) ，触发(Trigger)……等，最终都会被翻译成JASS语言存在地图文件里，在游戏时被使用。JASS在语法结构上比较接近Basic，同时也引用了许多C语言的东西。

触发编辑器中的 Edit =＞Convert To Custom Text 将触发转成文字型态。
在触发编辑器下面选 Actions =＞Custom Script 可以插入单行JASS叙述。

## IDE

https://www.hiveworkshop.com/threads/jass-shop-pro-v-1-4-3-4.62921/
https://soft.3dmgame.com/down/202780.html

## 语法

```sh
// 变量定义与赋值
local integer i=1
local real a=i+0.3
set i=i-100

// 函数调用
// I2S 即 int2str
call BJDebugMsg( I2S( 5000 ) )

// 条件语句
if ( ) then

elseif (  ) then

else

endif

// 循环语句
loop

exitwhen ( )

endloop

// 定义函数
function fn_name takes nothing returns nothing

endfunction
```
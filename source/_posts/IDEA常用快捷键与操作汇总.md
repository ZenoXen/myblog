---
title: IDEA常用快捷键与操作汇总
date: 2022-06-24 11:15:26
tags:
  - IDEA
  - 生产工具
categories:
  - 生产工具
description: 记录一下IDEA各类常用操作，基本都来源于IDEA的“学习新特性”功能 
---

## 上下文与搜索功能

1. 智能代码修正：alt+enter
2. 全局搜索（类、方法、变量、文件、）：双击shift弹出搜索栏；在搜索结果条目下按ctrl+Q可以显示该条目相关文档

## 代码编辑

1. 扩大代码选取：ctrl+w；包括字符串选取都可以用这个方法扩大
2. 缩小代码选取：ctrl+shift+w
3. 注释：ctrl+/
4. 复制代码：ctrl+d；可配合使用shift+方向键上包含选取上一行后再复制
5. 删除代码：ctrl+y
6. 移动代码行：alt+shift+方向键上/方向键下
7. 移动方法：光标移到方法头，按ctrl+shift+方向键上/方向键下
8. 折叠代码块：ctrl+减号
9. 打开代码块：ctrl+等号
10. 打开当前文件所有代码块：ctrl+shift+等号
11. 折叠当前文件所有代码块：ctrl+shift+减号
12. 包裹当前代码：ctrl+alt+t，如if、try catch、for、while
13. 取消包裹代码：ctrl+shift+delete
14. 选取符号：alt+j，一直按可以一直选取下一个同类符号
15. 取消选取上一个符号：alt+shift+j
16. 选取所有同类符号：ctrl+alt+shift+j

## 代码补全

1. 自动补全：ctrl+space，选择补全选项后，使用tab，可以替换当前光标内的字符，而不是插入
2. 添加分号：ctrl+shift+enter
3. 自动类型补全：ctrl+shift+space，可以用在赋值声明和return语句上
4. 后缀补全：在一条已经写好的表达式后面使用句号，可以选择合适的补全方式
5. 语句补全：ctrl+shift+enter，可以补全if、for的圆括号、大括号

## 快速重构 

1. 重命名：shift+F6
2. 提取变量：ctrl+alt+v
3. 提取方法：ctrl+alt+m
4. 重构菜单：ctrl+alt+shift+t，可以用于提取参数、提取行内变量、提取常量等

## Local History

git history只能显示本次提交与上次提交之间的区别，假设本次修改了许多东西，而我们想回滚其中一部分的改动，则撤回操作就不那么好用了，此时可以使用IDEA的Local History功能，在编辑页面右键选择Local History，每次编辑代码时都会产生一个新的版本，选择其中的版本，并回滚自己需要的改动就可以了

## 代码助手

1. 格式化代码：ctrl+alt+l
2. 显示格式化代码设置：ctrl+alt+shift+l
3. 查看方法签名：ctrl+p
4. 快速查看文档：ctrl+q
5. 快速查看定义：对方法、变量等，可以使用ctrl+shift+i查看其定义
6. 跳转到下一个错误：F2
7. 高亮所有引用处：ctrl+shift+F7

## 导航

1. 全局搜索字符串：ctrl+shift+f，搜出来的结果可能是包含搜索的字符串，如果想要匹配整个词语，可以使用alt+w限定整词查询
2. 全局替换字符串：ctrl+shift+r
3. 文件结构导航：ctrl+F12，可以查看该文件的整体结构，所有定义，而不必关注它各个方法的实现
4. 查看实现：ctrl+alt+b，使用ctrl+u可以返回父级
5. 查看近期打开的文件：ctrl+e
6. 预览近期打开的文件：ctrl+shift+e，可以搜索这些文件中的字符串


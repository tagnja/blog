---
title: 如何阅读源码
date: 2020-07-02 14:09:50
categories:
- 其它
- 个人随笔
tags:
---



## Understanding the Technique

- Answer questions: what is it? How it works? What is the project mission?
- Its structure and functionality of modules.
- Use it. Get experience with the technology.

## Prepared for Starting Reading Source code

- Collecting questions.
- Choosing a version of source code.
- Start with a point of a question.

## The Process of Reading Source Code

- 对该技术有一些了解。最好用过它。能够理清它的目标使命，功能模块，核心功能。
- 从问题入手。先准备好，看源码时你要解决的问题。
- 通过不断地多次 debug 程序 （需要注意 debug 的技巧，快捷键跳转和看日志信息(DEBUG or TRACE)）和记笔记（主要记录关键的源文件，核心方法，核心代码，关键变量的值，以及一些类的继承关系的层次结构）。最终，了解问题实现的整个大致流程。
- 整理信息，画一个初步的时序图。
- 了解完整的流程。继续不断地 debug 程序，画出一个详细的时序图。
- 了解所有步骤的具体实现。一行一行代码去详细地了解问题的每一步的具体实现。
- 用自己的话简单地总结这个问题的实现过程。

## Methods of Reading Source Code

- 洋葱阅读法。逐渐深入细节。
- 用自己的话总结一段代码。
- 使用 IDE 的快捷键进行代码跳转。使用独立窗口打开重要的源码文件。
- 使用比较早的比较简单的版本的源码。
- 用 UML 图表示代码逻辑和结构，帮助你理解和记忆。
- 修改和测试源码。
- 打印该技术框架的 DEBUG 日志。

## References

[1] [How to read code without ripping your hair out](https://medium.com/launch-school/how-to-read-source-code-without-ripping-your-hair-out-e066472bbe8d)

[2] [What is the best way to read source code?](https://www.quora.com/What-is-the-best-way-to-read-source-code)

[3] [BECOME A BETTER DEVELOPER BY READING SOURCE CODE](https://www.stevejgordon.co.uk/become-a-better-developer-by-reading-source-code)
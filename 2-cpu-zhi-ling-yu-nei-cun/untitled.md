---
description: What is a CPU, anyway?
---

# 2.1 CPU到底是什么？

That might seem like a silly question to some but I’m sure there are plenty of competent programmers out there who are used to program in high level managed environments haven’t seen a register in their entire life. Let me make the introductions.

也许这个问题很可笑，但我相信有很多能干的程序员习惯于在高级托管**(**managed)环境中编程，他们一生都没有见过寄存器(register)。下面将介绍计算机组成原理的基础知识。

For our first version of the PlayStation CPU, I’m going to make some simplifying assumptions. I’m going to ignore the caches for instance and assume that it directly accesses the system bus. We’re going to implement a [Von Neumann architecture](https://en.wikipedia.org/wiki/Von\_Neumann\_architecture). As we make progress we’ll have to revisit this design to add the missing bits when they are needed.

对于我们第一个版本的PlayStation CPU，我将做一些简化的假设。例如，我将忽略缓存，并假设它直接访问系统总线。我们将实现一个冯-诺依曼架构。随着我们的进展，我们将不得不重新审视这个设计，以便在需要的时候添加缺少的小组件。

The objective of this section is to implement all the instructions and try to reach the part of the BIOS where it starts to draw on the screen. As we’ll see there’s a bunch of boring initialization code to run before we get there.

本节的目的是实现(implement)所有的指令，并尝试运行BIOS中开始在屏幕上绘图的部分。在此之前，有一堆无聊的初始化代码要运行。

There are 67 opcodes in the Playstation MIPS CPU. Some take one line to implement, others will give us more trouble. To make the process more interactive and less tedious we’ll implement them as they’re encountered while we’re running the original BIOS code. This way we’ll immediately be able to see our emulator in action.

在Playstation MIPS CPU中有67个操作码(opcode)。有些只需一行代码即可实现，有些却比较麻烦。为了使这个过程更有互动性和不那么乏味，我们将在运行原始BIOS代码时实现它们。这样，我们就能立即看到我们的模拟器在运行。

But first things first, before we start implementing instructions we need to explain how a CPU works.

但首先要做的是，在开始实现指令之前，我们需要解释一下CPU的工作原理。

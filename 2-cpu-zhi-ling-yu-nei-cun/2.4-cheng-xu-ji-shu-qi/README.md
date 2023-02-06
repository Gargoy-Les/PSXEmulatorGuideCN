---
description: The Program Counter register
---

# 2.4 程序计数器

[Registers](https://en.wikipedia.org/wiki/Processor\_register) are very small and very fast special-purpose memories built inside the CPU. Most CPU instructions manipulate those registers by adding them, multiplying them, masking them, storing their content to memory or fetching it back...

[寄存器](https://zh.wikipedia.org/wiki/%E5%AF%84%E5%AD%98%E5%99%A8)是内建于CPU的极其小而快的特定用途的存储器。大多数CPU指令通过加法、乘法、屏蔽(masking)将其内容存储至内存或取回以操作这些寄存器。

[The Program Counter](https://en.wikipedia.org/wiki/Program\_counter) (henceforth referred to as PC) is one of the most elementary registers, it exists in one form or another on basically all computer architectures (although it goes by various names, on x86 for instance it’s called the Instruction Pointer, IP). Its job is simply to hold the address of the next instruction to be run.

[程序计数器](https://zh.wikipedia.org/wiki/%E7%A8%8B%E5%BC%8F%E8%A8%88%E6%95%B8%E5%99%A8)(后文中简称PC)是最基本的寄存器之一，它以某种形式存在于几乎所有的计算机体系结构中(尽管名字不同。例如，x86把它称为指令指针(Instruction Pointer, IP))。它的工作是简单地保存下一条要运行的指令的地址。

As we’ve seen, the PlayStation uses 32bit addresses, so the PC register is 32bit wide (as are all other CPU registers for that matter).

如你所见，PlayStation使用32位地址，所以程序计数器寄存器是32位宽的(就像所有其他CPU寄存器一样)。

A typical CPU execution cycle goes roughly like this:

1. Fetch the instruction located at address PC,
2. Increment the PC to point to the next instruction,
3. Execute the instruction,
4. Repeat

一个典型的CPU执行周期(execution cycle)大致如此:

1. 取得程序计数器所指向的指令
2. 增加程序计数器的值
3. 执行指令
4. 重复

We need to know how big an instruction is to know how many bytes to fetch and how much we need to increment the PC to point at the next instruction. Some architectures have variable-length instructions (x86 and derivatives are a common example) which means we’d have to decode the instruction to know how many bytes it takes. Fortunately for us, the PlayStation uses a fixed-length instruction set ([The MIPS instruction set](https://en.wikipedia.org/wiki/MIPS\_instruction\_set)) and all instructions are 32bit long.

得知一条指令的大小，才能得知要取多少字节，和PC增加的量(以指向下一条指令)。一些架构有可变长度的指令(x86及其派生品(derivatives)是一个常见的例子)，这意味着我们必须解码指令才能知道它需要多少个字节。幸运的是，PlayStation使用固定长度的指令集([MIPS指令集](https://zh.wikipedia.org/wiki/MIPS%E6%9E%B6%E6%A7%8B))，所有的指令都是32位长。

With all that in mind, we can finally start writing some code!

‌Here’s what the CPU state looks like at that point:

考虑到所有这些，我们终于可以开始写一些代码了!

这是此时CPU的状态：

```rust
/// CPU state
/// CPU的状态
pub struct Cpu {
    /// The program counter register
    /// 程序计数器
    pc : u32,
     }
```

And here’s the implementation of our CPU cycle described above:&#x20;

这是上述的 CPU 周期(cycle)的实现(implementation)：\


```rust
impl Cpu {
    pub fn run_next_instruction(&mut self) {
        let pc = self.pc;
        // Fetch instruction at PC
        // 取得程序计数器所指向的指令
        let instruction = self.load32(pc);
        // Increment PC to point to the next instruction
        // 增加程序计数器的值以指向下一条指令
        self.pc = pc.wrapping_add(4);
        self.decode_and_execute(instruction);
    }
}
```

In Rust wrapping\_add means that we want the PC to wrap back to 0 in case of an overflow (i.e. 0xfffffffc + 4 => 0x00000000). We'll see that most CPU operations wrap on overflow (although some instructions catch those overflows and generate an exception, we'll see that later).

在 Rust 中 wrapping\_add的作用是程序计数器在溢出的情况下返回 0（即 0xfffffffc + 4 => 0x00000000）。 我们将看到大多数 CPU 操作都在溢出时结束（尽管有些指令会捕获这些溢出并生成异常，我们稍后会看到）。

If you’re coding in C you don’t need to worry about that if you use uint32\_t since the C standard mandates that unsigned overflow wraps around in this fashion. Rust however says that overflows are undefined and will generate an error in debug builds if an unchecked overflow is detected, that’s why I need to write pc.wrapping\_add(4) instead of pc + 4.

&#x20;C语言在这种情况下无需担心是否使用 uint32\_t，因为 C 标准要求无符号溢出以这种方式返回。 然而，Rust 规定溢出是未定义的，如果检测到未经检查的溢出，将在debug builds中产生错误，这就是为什么我需要编写 pc.wrapping\_add(4) 而不是 pc + 4。

We now finally have some code but it doesn’t build yet.

We’re still missing 3 pieces of the puzzle before we can run this piece of code:

* What’s the initial value of PC when starting up?
* How do we implement the fetch32 function?
* How do we implement the decode\_and\_execute function?

代码太少不够生成(build)，运行代码前还有三个难题：

* 启动时程序计数器的初始值是多少？
* 如何实现 fetch32 函数？
* 如何实现 decode\_and\_execute 函数？

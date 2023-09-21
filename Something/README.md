In Chapter 2, we explored how **logic gates can represent numbers and perform simple arithmetic operations**. Now, our focus shifts to using logic gates for **storing values over time**, specifically, how we can make a variable like "x" hold a value and keep it until we replace it with another value. To achieve this, we will introduce a new family of **memory chips**.

Up until now, the chips we built in Chapters 1 and 2, including the #ALU, were time-independent or combinational. They respond instantly to various input combinations, with the only delay being the time required for their internal computations. However, in this chapter, we introduce and **construct sequential chips**. Unlike combinational chips, sequential chips not only consider the current inputs but also **depend on inputs and outputs processed in previous time intervals.**

Understanding time is crucial for **sequential chips**. It involves the concepts of "**current**" and "**previous**" states, where you remember what was stored in memory before. To model the progression of time in logic, we use a **clock that generates a continuous stream of binary signals**, often referred to as "tick" and "tock." The time span between the start of a tick and the end of the subsequent tock is known as a cycle, and these cycles regulate the operations of all the memory chips used in the computer.

It starts with an introduction to **memory devices** from a user's perspective. Then, it delves into sequential logic, which will be employed to create **time-dependent chips**. The chapter will proceed to construct various memory devices such as registers, **RAM** (**Random Access Memory**) devices, and counters. Collectively, these memory components, in combination with the arithmetic devices developed in Chapter 2, form the essential chips required to build a complete and versatile computer system. This computer system challenge will be explored further in Chapter 5.

## Memory Devices
Computer programs rely on **variables**, **arrays**, and **objects** to manage and store data over time. Hardware systems facilitate this capability by **providing memory devices** capable of retaining data states. Human memory, which is electro-chemical in nature, allows us to remember things over time effortlessly. However, implementing this ability in classical logic, which lacks awareness of time and state, is challenging. Therefore, we need to develop a method to model the passage of time and imbue logic gates with the capacity to maintain state and respond to time-related changes.

To address this challenge, we introduce a **clock** and an **elementary time-dependent logic gate** known as the **data flip-flop (DFF)**. The #DFF can transition between **two stable states**, representing 0 and 1, making it the foundational building block for all memory devices. Despite its central role, DFFs often remain **low-profile**. Unlike **registers**, **RAM** **devices**, and **counters**, which have **prominent roles** in computer architecture, DFFs are used implicitly as essential components deeply embedded within other memory devices.

The critical role of the DFF is evident in **Figure 3.1**, where it forms the basis of the memory hierarchy we are about to construct. We will demonstrate how DFFs can create **1-bit registers** and how multiple registers can be combined to form **n-bit registers**. Additionally, we will develop a **RAM** device containing numerous registers and establish a method for addressing and instantaneously accessing any selected register from the RAM.

![][Screenshot 2023-09-20 at 12.32.32 PM.png|700]
Before setting out to build these chips, though, we’ll present a methodology and tools that enable modeling the progression of time and maintaining state over time.

## Sequential Logic
In chapters 1 and 2, we focused on chips rooted in classical logic, which is not **time-dependent**. To create memory devices, we must enhance our gate logic so that it can respond not just to input changes but also to the **rhythmic ticking of a clock**. Much like how we remember the meaning of the word "dog" from the moment we initially stored it in our memory, our computer architecture needs to incorporate a temporal dimension. This involves constructing tools that can **manage time using Boolean functions**, allowing us to maintain and handle states over time.

### Time Matters
In **Nand to Tetris** so far, we've assumed that chips **respond to their inputs instantaneously**. For instance, you input 7, 2, and "subtract" into the **ALU**, and it immediately outputs 5. However, in reality, there are always **delays in the outputs for at least two reasons**. First, the inputs of the **chips don't magically appear**; they come from the **outputs of other chips**, and this travel takes time. Second, the **computations chips perform also take time**, and the **more complex a chip's logic**, the longer it takes for the chip's **outputs to be fully generated**.

Therefore, time is a crucial factor that needs to be considered. In Figure 3.2, time is often depicted as a continuous, forward-moving arrow. However, this continuous view of time is **too abstract for computer scientists**. Instead, we prefer to break **time into discrete**, **fixed-length intervals** called **cycles**. These cycles are unable to divide or separate, and changes in the system only occur during transitions between cycles. Within a cycle, the system remains static.
![[Screenshot 2023-09-20 at 2.17.10 PM.png|700]]
The world is always changing, but when we work with computers, we choose to look at time in chunks. We only care about what's happening at the end of each chunk of time, and we don't pay attention to what's happening in between. This way of looking at time helps in two ways when designing computer systems. First, it helps us deal with the **unpredictability of communication** and **processing delays**. Second, it helps us **coordinate the actions of different parts** of a computer system.

For example, imagine we're using a "**Not gate**" in a computer, (**Fig 3.2**). When we give it the number 1 as input, it takes a little time to process and give us an output of 0. But because our time chunks are designed to be longer than this delay, when we check the output at the end of a chunk, it already shows 0. We don't see the delay; it seems like the gate instantly turned 1 into 0. To make this work, we need to make sure our time chunks are long enough to cover any possible delays in the system. But we also want them to be as **short as possible** to make the computer **faster**.

So, we choose a chunk length that's a bit longer than the longest delay in the system. Thanks to advances in technology, we can now make these chunks incredibly short, which makes computers very fast. We measure time using something that keeps **switching back and forth**, like a **pendulum**(**clock in Fig 3.2**). Each full swing back and forth represents one **chunk of time**. We use a signal, like 0 and 1, to show which part of the swing we're in (like "tick" and "tock"). This signal is sent to every part of the computer, making sure they all work together in sync.

In each part of the computer, this signal helps decide when to change to a new state and when to give an output. This way, everything in the computer works together smoothly, even though the world is always changing.

### Flip-Flops
Memory chips are created to hold onto or store information for a period. To make this storing process happen, we use small components known as **flip-flop gates**. There are various types of these gates, and in the Nand to Tetris project, we specifically use a gate called a **data flip-flop** or **DFF**. This #DFF has a setup with a single-bit data input and a single-bit data output, as illustrated at the top of Figure 3.3.
![[Screenshot 2023-09-20 at 2.41.56 PM.png|700]]
Additionally, the **DFF** includes a clock input that receives its signal from the **master clock**. When you combine the data input and the clock input, it allows the **DFF** to carry out a straightforward time-based action, where the output at the current time, out(t), equals the input value at the previous time, in(t-1).

Here, "**in**" and "**out**" represent the values going into and coming out of the gate. "**t**" stands for the **current time unit**, and we'll use the terms "**time unit**" and "**cycle**" interchangeably from now on. Don't concern yourself with the technical details of how this behavior is achieved. For now, just notice that at the conclusion of each **time unit**, the DFF provides the output that corresponds to the **input value** from the **previous time unit**.

Much like **Nand** gates, **DFF** gates are an essential part of the computer's hardware, but they are located deeper within the hardware hierarchy. As shown in **Figure 3.1**, all memory components in the computer, including **registers, RAM units**, and **counters**, **rely** on #DFF gates at their core. These **DFF** gates are synchronized by the **same master clock**, creating a large, coordinated group.

At the end of each clock cycle, all the #DFF gates in the computer update their **outputs** **based on the inputs from the previous cycle**. However, between these clock cycles, the #DFF gates are "latched," meaning any changes in their inputs don't immediately affect their outputs. This process happens many times per second, depending on the computer's **clock frequency**.

To manage this **time-dependent operation**, hardware implementations use a **dedicated clock bus** that distributes the master clock signal to all the #DFF gates simultaneously. Hardware simulators achieve the same effect through software emulation. Specifically, the Nand to Tetris hardware simulator provides a **clock icon** that lets users manually advance the clock and also offers **"tick" and "tock"** commands that can be scripted for testing purposes.
![[Screenshot 2023-09-21 at 10.46.08 AM.png|700]]
### Combinational and Sequential Logic
In the previous chapters (1 and 2), all the chips we developed, from basic logic gates to the **ALU**, were designed to react only to changes happening in the **current clock cycle**. These chips are called "**time-independent**" or "**combinational**" chips. The term "**combinational**" refers to the fact that these chips respond solely to different combinations of input values without considering the passage of time.

On the other hand, there are chips designed to respond not only to changes in the **current time** unit but also to changes that occurred in **previous time units**. These are known as "**sequential**" or "**clocked**" chips. The core component of sequential logic is the DFF (**data flip-flop**), and any chip that includes a DFF, either directly or indirectly, is considered sequential. Figure 3.4 illustrates a typical configuration of sequential logic.
![[Screenshot 2023-09-21 at 9.15.20 AM.png|700]]
In this configuration, you'll find one or more chips that include DFF components, either **directly** or **indirectly**. As depicted in the figure, these sequential chips may also interact with combinational chips. The feedback loop in sequential chips allows them to take into account inputs and outputs from the **previous time unit**. In combinational chips, where time isn't considered, introducing **feedback loops** can be problematic. It could create a situation where the chip's output depends on its input, which in turn depends on the output, creating a circular loop. However, this issue can be resolved by using a DFF gate in the feedback loop. The DFF introduces a natural time delay, ensuring that the output at time "**t**" doesn't depend on itself but rather on the output at time "**t-1.**"

Imagine you're using a computer, and you want it to add two numbers, let's call them **x** and **y**. But here's the catch: x is stored in one part of the computer, and y is stored in a different part, a bit far away. Now, because of how computer parts are placed and how signals travel, it might take a bit longer for the computer to get the information about x and y. So, when the computer starts adding them up, it might not get the **right answer immediately**. It's like trying to listen to two musicians play in sync when they're in different rooms. But here's the clever part: In a computer, we use these **time snapshots** called **cycles**. We make sure that one cycle is long enough for the information (x and y) to travel from one part of the computer to another and for the computer to finish its calculations.

So, when we tell the computer to add x and y, we know that by the end of this time snapshot (cycle), the computer will give us the correct answer. We don't worry about the small delays in between. It's like conducting an orchestra and making sure all the musicians play in harmony even if they start at slightly different times. This trick of using cycles and making sure they're long enough for everything to happen is what keeps all the computer parts working together like a well-orchestrated team. We'll dive into this more when we talk about building the whole computer system in chapter 5.

## Specification
Now, we're going to talk about the memory chips that computers use:
1. **Data Flip-Flops (DFFs):** These are like tiny switches in a computer. They can remember a 0 or a 1, like an "on" or "off" switch.
2. **Registers:** These are made from DFFs. They can remember a small amount of information, like a number. They are a bit like tiny storage spaces in a computer.
3. **RAM Devices:** These are built from registers. They can remember a lot more information, like a bunch of numbers or data.
4. **Counters:** These are also built from registers. They are like little counters that can keep track of numbers for us.

We'll keep it simple and just talk about what these chips can do and how they connect with other parts of the computer. The technical details of how they work will be discussed later in the Implementation section.

### Data Flip-Flop
Imagine a tiny device in a computer called a **Data Flip-Flop** (**DFF**). It's a bit like a switch with a memory. It has four things:
1. **Data Input:** This is where you put in a single piece of information, like a 0 or a 1.
2. **Data Output:** This is where the DFF gives you back what you put in, but not right away.
3. **Clock Input:** It has a clock, like a heartbeat, that beats in a regular rhythm.
4. **How it Works:** Here's what it does - If you put a 0 or a 1 into the DFF's input, it remembers that number. But it doesn't give it back to you right away. It waits for one beat of the clock and then gives you the number you put in.

This simple device might not seem like much, but it's really important because we can use it to build more complex things like registers (which are like tiny storage spaces in a computer). We'll learn more about registers next.

### Registers
1. **Bit:** Think of it as a tiny memory chip in a computer. It can remember just one piece of information, either a 0 or a 1.
2. **Register:** This is like a bigger version of the Bit. It can remember a whole bunch of 0s and 1s, up to 16 of them.
3. **How Bit Works:** Imagine Bit as a small box. You can put a 0 or a 1 inside it. You can also tell it to remember what you put in. And when you want to know what's inside, it can tell you if it's 0 or 1.
4. **How Register Works:** Register is like a bigger box with 16 slots. You can put 0s and 1s in these slots. You can also tell it to remember what you put in. And when you want to know what's in each slot, it can tell you.
So, Bit remembers just one number, while Register can remember up to 16 numbers. That's what makes them useful in a computer.

![[Screenshot 2023-09-21 at 9.32.08 AM.png|700]]
**Single-Bit Register (Bit):** Think of it as a small box that can remember one number (0 or 1). It can do two things:
    - You can put a number (0 or 1) inside it.
    - You can tell it to remember what's inside.
    As long as you don't tell it to change, it will keep remembering the same number.
   
**16-Bit Register (Register):** This is like a bigger box with 16 slots. Each slot can remember a number (0 or 1). It can also do the same two things:
    - You can put numbers (0s and 1s) in its slots.
    - You can tell it to remember what's in the slots.
    Just like the small box, it will remember the numbers in its slots as long as you don't tell it to change.
So, whether it's a small box (Bit) or a bigger box with 16 slots (Register), they both remember numbers, and they'll keep remembering them until you tell them to forget or change. That's how they work in a computer.

**Using Bit and Register:** Both the Bit and the 16-bit Register work in the same way. You can use them like this:
	**Reading:** If you want to know what's inside the register, just look at the "out" part.
	**Writing:** If you want to change what's inside the register, put the new number (0 or 1) into the "in" part, and also turn on the "load" switch (put a 1 there). This will make the register remember the new number, and from that moment on, it will always show the new number when you look at "out."

**What Register Does:** Think of the Register as your computer's memory. It does exactly what you'd expect from memory: it remembers the last number you told it to remember until you tell it to remember a new one.

So, these chips work like your memory in a computer. You can read what's stored, and you can write new information into them to remember.


**#Paper10:Singularity: Rethinking the Software Stack**

**##Authors: Galen C. Hunt and James R. Larus**

**##Keywords:Operating systems, safe programming languages, program ****verification, program specification, sealed process architecture, ****sealed kernel, software-isolated processes (SIPs), hardware**

**protection domains, manifest-based programs (MBPs), unsafe ****code tax**

**### 1. ****What's the problems the paper works out ?**

本文描述了奇点项目（Singularity Project）根据编程语言和验证工具的进展重新审视操作系统设计所做的工作。比如描述了奇点项目的系统内核实现，说明了探索过程中，发现的操作系统和系统设计领域的新机遇

**### 2. ****How the paper work out the problem? **

**#####2.1 奇点系统内核的基本架构特征**

（1）Software-Isolated Process（SIP）

- 功能

进程资源的持有者，并且提供用于进程执行的上下文。



- 奇点SIPs特点：与其他SIPs不分享可写内存，相互之间通过软件验证而非硬件保护隔离。只能通过exchange heap交换messages来交流。

- 优势：

与硬件保护的进程相比，SIP之间的通信引起低开销并且SIP创建和销毁成本低（参见表1）

低成本使得将SIP用作细粒度隔离和扩展机制来替代硬件保护进程和未保护的动态代码加载的常规机制是实用的。

（2）Contract-Based Channels

\- 功能：

SIPs之间的而所有通信都要通过contract-based channels。信道（channel）具有恰好两个端点的双向消息管道。通道提供无损的有序消息队列。 从语义上讲，每个端点都有一个接收队列。 在端点上发送会在另一个端点的接收队列上排队一个消息。 通道端点每次只属于一个线程。 只有端点的所属线程可以从其接收队列中出队消息或向其对等体发送消息。

\- 优势：

经验表明channel constracts在防止和检测错误方面很有价值。

（3）Manifest-Based Programs

\- 功能：

MBP是由静态manifest定义的程序。奇点系统中不允许运行没有manifest的代码。

manifest存储着一个MBP代码的代码资源，所需系统资源，期望容量，程序依赖等等，其主要作用是能用来帮助静态或动态的验证MBP属性。

**#####2.2 奇点系统内核**

支撑奇点系统架构的是奇点系统内核

（1）Singularity Application Binary Interface (ABI)

\- 功能

ABI为每个SIP确保最小，安全和隔离的计算环境。SIP通过ABI访问原始内核设施，例如发送和接收消息的能力。

注：SIP可以访问更高级别的系统服务，例如访问文件或发送和接收网络数据包的能力，但是是通过channel，而不是ABI功能。

![img](file:///var/folders/10/y0nrg3x96fx_zqc8hh__13wh0000gn/T/WizNote/a642db12-2fbf-4969-b3fc-ea19c9eb481d/index_files/77528623.png)**#####2.3 内存管理**

memory independence invariant: SIP内的指针只能指向自己的内存或者exchange heap中的内存 。这样就保证了每个SIP的垃圾回收和终止与其他SIP无关

（1）Exchange Heap

exchange heap内的指针只能指向exchange heap自己内部。

在系统执行期间的任何时间，交换堆中的每个存储器块由至多一个SIP拥有（可访问）。 注意，SIP可能将悬挂指针保持到交换堆（指向SIP不再拥有的块的指针）。 静态验证确保SIP永远不会通过悬挂指针访问内存。

![img](file:///var/folders/10/y0nrg3x96fx_zqc8hh__13wh0000gn/T/WizNote/a642db12-2fbf-4969-b3fc-ea19c9eb481d/index_files/82024575.png)

**#####2.4 线程******

（1）Linked Stacks

优势：奇点系统使用Linked Stacks来缓解线程内存溢出的情况

（2）Scheduler

功能：Scheduler保留两个运行线程的列表，一个是unblocked列表，存储最近运行过的线程。一个是preempted列表，存储运行中被抢占的线程。

**### 3. ****Summary of major innovations **

创立的奇点系统综合考虑了语言，验证工具以及系统结构三个方面进行优化。取得了一定成果，展示了新的机会。

**### 4. ****How about the important related works/papers?**
****

****

**### 5. ****How to test/compare/analyze the results?**

Figure5是文中唯一一幅有关性能测试的示意图，展示了逐步增加保证代码正确的内核功能，代码运行时间逐渐增加。

以此展示为运行把安全代码所付出的代价。

![img](file:///var/folders/10/y0nrg3x96fx_zqc8hh__13wh0000gn/T/WizNote/a642db12-2fbf-4969-b3fc-ea19c9eb481d/index_files/29563014.png)

**### 6. ****What are some intriguing aspects of the paper?**

本文给出大量有关设计说明的图片或列表，Figure5对比了内核各部分设计的时间开销。但是以上所有分析，包括新设计的优势，都是通过理论描述来说明的。尽管作者指出部分方面（如硬件隔离保护代码安全的代价种类多）难以量化表示，但整体而言本文严重缺乏数据支撑，仅有文字说明。

**### 7. ****How can the research be improved?**

应该增加性能测试等数据说明

**### 8. ****If you write this paper, then how would you do?**

我将从几个部分入手

**#####8.1 Abstract**

**#####8.2 Introduction**

同文章，介绍奇点项目意义，以及奇点系统的设计思路/特点和作用。

**#####8.3 Background**

本文提出了全新的系统，在架构，验证工具，语言方面都是全新的。因此应该增加一个Background介绍related word，并以综述形式进行点评分类，说明现有系统的优化方向，重难点，说明本项目的思路和意义。

**#####8.4 Overview（**Implementation**）**

具体化三个部分的设计思路/改进思路

（本文侧重在架构设计，三个改进部分的绝大部分代码为原创，并且对于算法要求不多，主要是实现思路，所以与一般重算法的文章不同，没必要详细写Implementation。）

**#####8.5 Evaluation**

评估具体改进成果，与现有系统作对比，这点与稳中相同。

**#####8.6 Conclution**

总结全文

**### 9. ****Did you test the results by yourself? If so,What’s your test Results?**

haven't yet

**### 10. ****Give the survey paper list in the same area of the paper your reading.**
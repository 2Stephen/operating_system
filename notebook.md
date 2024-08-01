[TOC]

## 一

## 二.进程

### 1_1进程的概念组成特征

- 程序：是静态的，就是个存放在磁盘里的可执行文件，就是一系列的指令集合。
- 进程（Process）：是动态的，是程序的一次执行过程（同一个程序多次执行会对应多个进程）

1. 操作系统是这些进程的管理者，它要怎么区分各个进程？

   当进程被创建时，操作系统会为该进程分配一个唯一的、不重复的“身份证号”——PID（ProcessID，进程ID）

2. 操作系统要记录PID、进程所属用户ID（UID）（ 基本的进程描述信息，可以让操作系统区分各个进程）
   
   分配了哪些资源（可用于实现os对资源的管理）
   
   运行情况（实现os对进程的控制调度）
   
   **这些信息被保存在一个数据结构PCB（process control block）中，即进程控制块**
   
   ![image-20240729091931807](./pics/PCB.jpg)
   
   ![进程](./pics/进程.jpg)
   
3. 程序段、数据段、PCB三部分组成了进程实体（进程映像）

   引入进程实体的概念后，可以把进程定义为：

   进程是进程实体的运行过程，是系统进行资源分配和调度的一个独立单位

4. 进程的特征

   ![](./pics/进程的特征.jpg)

### 1_2进程的状态与转换、进程的组织

1. 进程的状态

   + 创建态：进程正在被创建时，它的状态是“创建态”，在这个阶段操作系统会为进程分配资源、初始化PCB
   + 就绪态：当进程创建完成后，便进入“就绪态”， 处于就绪态的进程已经具备运行条件，但由于没有空闲CPU，就暂时不能运行
   + 运行态：进程在cpu上运行，cpu会执行该进程对应的程序（执行指令序列）
   + 阻塞态：进程运行的过程中，可能会请求等待某个事件的发生（如等待某种系统资源的分配，或者等待其他进程的响应），在这个事件发生之前，进程无法继续往下执行，此时os会让这个进程下cpu，并让他进入“阻塞态”
   + 终止态：一个进程可以执行exit系统调用，请求操作系统终止该进程。 此时该进程会进入“终止态”，操作系统会让该进程下CPU，并回收内存空间等资源，最后还要回收该进程的PCB，当终止进程的工作完成后，这个进程就彻底消失了

2. ![](./pics/进程五状态.jpg)

3. 进程的整个生命周期 中，大部分时间都处于三种基本状态

   + 三种基本状态

     + 运行态（Running）占有CPU，并在CPU上运行

       *单CPU情况下，同一时刻只会有一 个进程处于运行态，多核CPU情况下，可能有多个进程处于运行态*

     + 就绪态（Ready）已经具备运行条件，但由于没有空闲CPU，而暂时不能运行
     + 阻塞态（Waiting/Blocked，又称：等待态）因等待某一事件而暂时不能运行

   + 另外两种状态
     + 创建态（New，又称：新建态）进程正在被创建，操作系统为进程分配资源、初始化PCB
     + 终止态（Terminated，又称：结束态）进程正在从系统中撤销，操作系统会回收进程拥有的资源、撤销PCB

   **进程PCB中，会有一个变量state表示进程的当前状态，为了对同一状态下的各个进程进行统一管理，os会将各个进程的PCB组织起来**

4. 进程的组织

   + 链接方式

     执行指针-->PCB2

     就绪队列指针-->PCB5->PCB1

     等待打印机的阻塞队列-->PCB3

   + 索引方式

### 1_3进程控制

1. 原语的执行具有原子性，即执行过程只能一气呵成，期间不允许被中断，可以用“关中断指令”和“开中断指令”这两个特权指令实现原子性
2. CPU执行了关中断指令之后，就不再例行检查中断信号，直到执行开中断指令之后才会恢复检查。

3. 1、进程的创建：

   创建原语：申请空白PCB、为新进程分配所需资源、初始化PCB、将PCB插入就绪队列

   引起进程创建的事件：用户登录、作业调度、提供服务、应用请求

   2、进程的终止：

   撤销原语

   引起进程中止的事件：正常结束、异常结束、外界干预

   3、进程的阻塞：

   阻塞原语：运行态->阻塞态

   引起进程阻塞的事件：需要等待系统分配某种资源、需要等待相互合作的其他进程完成工作

   4、进程的唤醒：

   唤醒原语：阻塞态->就绪态

   引起进程唤醒的事件：等待的事件发生

   5、进程的切换

   切换原语

   引起进程切换的事件：当前进程事件片到、有更高优先级的进程到达、当前进程主动阻塞、当前进程终止

### 1_4 进程通信

1. 进程间通信（Inter-Process Communication,IPC）指两个进程之间产生数据交互。

2. 为什么进程通信需要os支持？

   进程是分配系统资源的单位（包括内存地址空间），因此各进程拥有的内存地址空间相互独立。

   为了保证安全，一个进程不能直接访问另一个进程的地址空间。

3. 三种进程通信：共享存储、消息传递、管道通信

   1. 共享存储：进程开辟共享存储区，为避免出错，各个进程对共享空间的访问应该是互斥的。 各个进程可使用操作系统内核提供的同步互斥工具（如P、V操作）

      > 基于数据结构的共享：比如共享空间里只能放 一个长度为10的数组。这种共享方式速度慢、限制多，是一种低级通信方式 
      >
      > 基于存储区的共享：操作系统在内存中划出一 块共享存储区，数据的形式、存放位置都由通 信进程控制，而不是操作系统。这种共享方式速度很快，是一种高级通信方式

   2. 消息传递：进程间的数据交换以格式化的消息（Message）单位。进程通过操作系统提供的“发送消息/接收消息”两个原语进行数据交换。

      1. 格式化的消息：

         > 消息头：发送进程ID，接受进程ID，信息长度等
         >
         > 信息题

      2. 消息传递：

         > 直接通信方式：消息发送进程要知名接收进程的ID
         >
         > 间接通信方式：通过“信箱”间接地通信。因此又称“信箱通信方式”

   3. 管道通信：进程P->pipe->进程Q（单向）

      “管道”是一个特殊的共享文件，又名pipe文件。其实就是在内存中开辟一个大小固定的内存缓冲区

      1. 管道只能采用半双工通信，某一时间段内只能实现单向的传输。如果要实现双向同时通信，则需要设置两个管道。

      2. 各进程要互斥地访问管道（由OS实现）

      3. 管道写满，写进程将阻塞，直到读进程取走数据

      4. 管道读空，读进程将阻塞，直到写进程写入数据

      5. 管道中数据一旦被读出，就会彻底消失，因此当多个进程读同一个管道时，可能会错乱

         > 解决方案：
         >
         > 1. 一个pipe允许多个写进程，一个读进程
         > 2. 允许多个读写进程，但系统会让各个读进程轮流从管道中读数据（linux方案）

### 1_5 线程的概念

1. 可以把线程理解“轻量级进程”。 线程是一个基本的CPU执行单元，也是程序执行流的最小单位。
2. 引入线程之后，不仅是进程之间可以并发，进程内的各线程之间也可以并发，从而进一步提升了系统的并发度，使得一个进程内也可以并发处理各种任务（如QQ视频、文字聊天、传文件）
3. 引入线程后，进程只作为除CPU之外的系统资源的分配单元（如打印机、内存地址空间等都是分配给进程的）

![](./pics/线程.png)

![](./pics/线程的属性.png)

### 1_6线程实现方式

1. 用户级线程（ULT）：由线程库实现，很多编程语言提供了强大的线程库，可以实现线程的创建、销毁、调度等

2. 内核级线程（KLT）：由OS支持的线程

3. 多线程模型：

   > 一对一模型：一个用户级线程映射到一个内核级线程，每个用户进程有与用户级线程同数量的内核级线程
   >
   > 多对一模型：多个用户级线程映射到一个内核级线程，一个进程只分配给一个内核级线程
   >
   > 多对多模型：n个用户级线程映射到m个内核级线程 n≥m， 每个用户进程对应m个内核级线程
   >
   > *内核级线程才是处理机分配的单位*

### 1_7 线程的状态与转换

1. 和进程类似：就绪、运行、阻塞

2. 组织与控制：

   > TCB:线程控制块
   >
   > + 线程标识符TID
   > + 程序计数器PC：线程目前执行到哪里
   > + 其他寄存器：线程运行的中间结果
   > + 堆栈指针：堆栈保存函数调用信息、局部变量等
   > + 线程运行状态：运行、就绪、阻塞
   > + 优先级：线程调度、资源分配的参考
   >
   > *线程切换时要保存/恢复：PC、register、堆栈指针*
   >
   > 线程表：多个TCB组织成线程表

### 2_1调度的概念、层次

1. 调度：资源有限，没法同时处理事情，需要确定某种规则来决定处理任务的顺序

2. 调度的三个层次：

   + 高级调度（作业调度）：按一定的原则从外存的作业后备队列中挑选一个作业调入内存，并创建进程。每个作业只调入一次，调出一次，调入创建PCB调出撤销PCB

     > 作业：一个具体的任务

   + 低级调度（进程调度/处理机调度）：按照某种策略从就绪队列中选取一个进程

   + 中级调度（内存调度）：按照某种策略决定将哪种处于挂起状态的进程重新调入内存

     内存不足时，可以将某些进程的数据调出外存，等内存空闲或者进程需要时再重新调入内存。

     暂时调到外存等待的进程状态为挂起状态，被挂起的进程PCB会被组织成挂起队列

### 2_2调度的目标

1. cpu利用率=忙碌的时间/总时间

2. 系统吞吐量 = 总共完成了多少道作业/总共花了多少时间

3. 周转时间：作业被提交给系统开始，道作业完成为止

   > 四个部分：
   >
   > + 作业在外存后备队列上的等待作业调度（高级调度）的时间
   > + 进程在就绪队列上等待进程的调度（低级调度）的时间
   > + 进程在CPU上执行的时间
   > + 进程等待IO操作完成的时间
   >
   > *后三项在一个作业的整个处理过程中，可能发生多次*
   >
   > （作业）周转时间=作业完成时间-作业提交时间
   >
   > 平均周转时间：各作业周转时间之和/作业数
   >
   > 带权周转时间 = 作业周期时间/作业实际运行的时间 = （作业完成时间 - 作业提交时间）/作业实际运行的时间≥1
   >
   > 平均带权周转时间：各作业的带权周转时间之和/作业数

4. 等待时间：作业/进程等待处理机状态时间之和

5. 响应时间：用户提交请求到首次产生响应所用的时间

### 2_3 进程调度的时机、切换过程和方式

1. 进程调度

   > 需要进程调度：进程主动、被动放弃处理机
   >
   > 不能进程调度：处理中断、进程在OS内核程序临界区中、在原子操作过程中

   *临界资源：一个时间段内只允许一个进程使用的资源，各进程需要互斥地访问*

   *临界区：访问临界资源的那段代码*

2. 进程调度的方式

   + 非剥夺调度方式：非抢占式，只允许进程主动放弃处理机
   + 剥夺调度方式：抢占方式

3. 进程的切换与过程

   进程切换主要完成了：对原来运行进程各种数据保存、对新进程各种数据的恢复

   进程切换有代价

### 2_4 调度器



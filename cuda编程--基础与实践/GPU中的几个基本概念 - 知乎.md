**物理概念：** 
----------

streaming processor(sp): 最基本的处理单元。GPU进行并行计算，也就是很多个sp同时做处理。现在SP的术语已经有点弱化了，而是直接使用thread来代替。一个SP对应一个thread

**Warp：** warp是SM调度和执行的基础概念，通常一个SM中的SP(thread)会分成几个warp(也就是SP在SM中是进行分组的，物理上进行的分组)，一般每一个WARP中有32个thread.这个WARP中的32个thread(sp)是一起工作的，执行相同的指令，如果没有这么多thread需要工作，那么这个WARP中的一些thread(sp)是不工作的

（每一个线程都有自己的寄存器内存和local memory，一个warp中的线程是同时执行的，也就是当进行并行计算时，线程数尽量为32的倍数，如果线程数不上32的倍数的话；假如是1，则warp会生成一个掩码，当一个指令控制器对一个warp单位的线程发送指令时，32个线程中只有一个线程在真正执行，其他31个 进程会进入静默状态。）

**streaming multiprocessor(sm)：** 多个sp加上其他的一些资源组成一个sm, 其他资源也就是存储资源，共享内存，寄储器等。可见，一个SM中的所有SP是先分成warp的，是共享同一个memory和instruction unit（指令单元）。从硬件角度讲，一个GPU由多个SM组成（当然还有其他部分），一个SM包含有多个SP（以及还有寄存器资源，shared memory资源，L1cache，scheduler，SPU，LD/ST单元等等）

**软件概念：** 
----------

**thread–>block–>grid：** 

CUDA在执行的时候是让host里面的一个一个的kernel按照线程网格（Grid）的概念在显卡硬件（GPU）上执行。当要执行这些任务的时候，每一个Grid又把任务分成一部分一部分的block，block再分线程来完成。每个Grid中的任务是一定的。二维线程块的索引关系为如下：

unsigned int xIndex = blockDim.x \* blockIdx.x + threadIdx.x;

unsigned int yIndex = blockDim.y \* blockIdx.y + threadIdx.y;

![](https://pic2.zhimg.com/v2-b6e662a74b46f4501b60f98ff191fe5d_b.jpg)

**cuda内存模型**
------------

![](https://pic1.zhimg.com/v2-5be010dbf9d3253f263f91952890fab8_b.jpg)

*   每个 thread 都有自己的一份 register 和 local memory 的空间。
*   一组thread构成一个 block，这些thread 则共享有一份shared memory。
*   所有的 thread(包括不同 block 的 thread)都共享一份global memory、constant memory、和 texture memory。
*   不同的 grid 则有各自的 global memory、constant memory 和 texture memory。
*   每一个时钟周期内，warp（一个block里面一起运行的thread，其中各
*   个线程对应的数据资源不同(指令相同但是数据不同）包含的thread数量是有限的，现在的规定是32个。一个block中含有16个warp。所以一个block中最多含有512个线程.每次Device（就是GPU）只

**小结：** 
--------

**物理层：** 在GPU中最小的硬件单元是SP(这个术语通常使用thread来代替),而硬件上一个SM中的所有SP在物理上是分成了几个WARP(每一个warp包含一些thread),warp中的SP是可以同时工作的，但是执行相同的指令，也就是说取指令单元取一条指令同时发射给WARP中的所有的SP(假设SP都需要工作，否则有些是idle的).可见，在硬件上一个SM->WARPS->threads(sp).

**逻辑层：** 一个kernel对应一个GRID，该GRID又包含若干个block，block内包含若干个thread。GRID跑在GPU上的时候，可能是独占一个GPU的，也可能是多个kernel并发占用一个GPU的（需要fermi及更新的GPU架构支持）。

**补充：** 
--------

**SIMT架构：** SIMT中文译为单指令多线程，英文全称为Single Instruction Multiple Threads。如同CPU中的SIMD。GPU中的SIMT体系结构相对于CPU的SIMD中的概念。为了有效地管理和执行多个单线程，多处理器采用了SIMT架构。此架构在第一个unified computing GPU中由NVIDIA公司生产的GPU引入。不同于CPU中通过SIMD（单指令多数据）来处理矢量数据；GPU则使用SIMT，SIMT的好处是无需开发者费力把数据凑成合适的矢量长度，并且SIMT允许每个线程有不同的分支。 纯粹使用SIMD不能并行的执行有条件跳转的函数，很显然条件跳转会根据输入数据不同在不同的线程中有不同表现，这个只有利用SIMT才能做到
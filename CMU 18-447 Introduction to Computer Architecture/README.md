# [Spring 2015 -- Computer Architecture Lectures -- Carnegie Mellon](https://www.youtube.com/playlist?list=PL5PHm2jkkXmi5CxxI7b3JCL1TWybTDtKq) 笔记

> 我看的是这个版本：[Computer Architecture - ETH Zürich - Fall 2017](https://www.youtube.com/playlist?list=PL5Q2soXY2Zi9OhoVQBXYFIZywZXCPl4M_)   
> 课程主页：[Computer Architecture – Fall 2017 (263-2210-00L)](https://safari.ethz.ch/architecture/fall2017/doku.php)   
> 课件：[Schedule - Computer Architecture - Fall 2017](https://safari.ethz.ch/architecture/fall2017/doku.php?id=schedule)   
> 论文阅读：[Readings - Computer Architecture - Fall 2017](https://safari.ethz.ch/architecture/fall2017/doku.php?id=readings)   
> 15年的lab：[Labs](http://www.archive.ece.cmu.edu/~ece447/s15/doku.php?id=labs)   
> 15年的 schedule：[Schedule](http://www.archive.ece.cmu.edu/~ece447/s15/doku.php?id=schedule)


- [Lecture 1: Introduction and Basics](#01)
- [Lecture 2: Fundamentals, Memory Hierarchy, Caches](#02)
- [Lecture 3: Cache Management and Memory Parallelism](#03)
- []()
- []()


&nbsp;   
<a id="01"></a>
## Lecture 1: Introduction and Basics

<img src="./assets/01_DRAM_cell.png" width="360"/>

周期性地 refresh，refresh 时 DRAM 不可用

Memory Controller 向 DRAM 发送 refresh command

**DRAM Row Hammer**：在一个 refresh interval 中频繁 open（高电压）, close（低电压） 一个 row，会造成其他临近 row 的错误


&nbsp;   
<a id="02"></a>
## Lecture 2: Fundamentals, Memory Hierarchy, Caches

- Sequential Execution Model
  - control driven (instruction pointer)
- Data Flow Model （类似 DAG scheduler）
  - data driven
  - parallel execution：当输入操作数就绪时，指令就可以执行 (hard for hardware)
  - hard for debugging

<a></a>

- **ISA**: Specifies how the programmer sees instructions to be executed
  - software/compiler assumes, hardware promises
  - 比如 IA32 手册
- **Microarchitecture**: How the underlying implementation actually executes instructions
  - 只要满足 ISA 的语义，指令可以以任意顺序执行
  - 流水线，乱序执行，超标量，指令预取

### DRAM

- storage: capacitor
- access (transistor): wordline (row enable) + bitline

### SRAM

<img src="./assets/02_SRAM.png" width="360"/>

<a></a>
<img src="./assets/02_memory_bank.png" width="360"/>

<a></a>
<img src="./assets/02_SRAM_access.png" width="360"/>

<a></a>
<img src="./assets/02_DRAM_access.png" width="360"/>

- 局部性原理
  - Temporal Locality
  - Spatial Locality

<a></a>

- Cache design decisions
  - Placement
  - Replacement
  - Granularity of management
  - Write policy
  - Instructions/data


&nbsp;   
<a id="03"></a>
## Lecture 3: Cache Management and Memory Parallelism

2^k 路组相连：cache 有 2^n line，分为 2^(n-k) 组，每组 2^k line。内存地址其中 (n-k) 位作为组号，组内 2^k line 使用 cache replacement policy   
k=0 时就是 directed map（直接映射）

### replacement policy

- LRU 的实现手法：对于 k路组相连，有 k! 种排序，于是需要 log2(k!) bits
  - 难以实现
- Next Victim：存储固定数量的 victim，作为循环队列，相当于判了缓刑，如果在 evict 前被 hit，那么清除出队列，后面的向前进，最后一个 victim 随机选一个
  - 硬件易于实现，通常有两个 victim 位置

**cache miss rate** 和 **execution time** 不同，因为有些 block is costly，以及 miss overlap

### Write policy

- **write through**：每次都回写内存（**consistency**）
- **write back**：evict 时回写内存

write miss：1. 直接写内存；2. 先 load 到 cache

### Instructions vs. Data

- Unified
  - + 共享 cache 空间
  - - I 和 D 在 pipeline 不同阶段（**因此 L1 cache 把指令和数据分开，因为 L1 cache 的设计核心就是 pipeline**）
  - - I 和 D 互相引起 thrash

### Multi-level Cache

- 一级 cache 内部
  - tag store & data store access：顺序 / 并行
- 不同 cache之间
  - Serial
  - Parrallel

### Cache 参数

- cache size
  - 超过一定值后性能不再显著提升
- block size
  - 太小无法利用 spatial locality，太大花费大量时间 fill 整个 block
- associativity（cache 一组有多少 line）
  - 大：命中率高，高 latency
  - 小：低 latency
- replacement policy

### Cache Miss 分类

- compulsory：首次访问某个 block 导致的 miss
  - prefetch
- capacity：被换出
  - associativity
  - software hints
- conflict：其他
  - 程序设计层面，data fits into cache

### 性能提升

> 2个术语：latency 是提供服务所花的时间；cost 是 stall processor 的时间

- reduce miss rate
  - victim cache：缓存最近被换出的 block。也就是额外维护一个缓存（所有组共享），防止 thrashing
  - hash
  - pseudo associativity：serial lookup
- reduce miss latency/cost
  - **low miss rate 不代表 low stall time**
  - Memory Level Parallelism：cache isolated miss 提高性能，见下图
- reduce hit latency/cost
- reconstruct data structure and access pattern
  - 数据访问对 cache 友好
  - 数据分片 fits into cache
  - AOS & SOA

<img src="./assets/03_MLP.png" width="360"/>

> P1-4 并行访问，S1-3 串行访问   
> cache S1-3 的 stall 较小

### 混合缓存替换算法

- 运行时切换 replacement policy
- 选择 policy：使用饱和计数器

> 有个问题：切换 policy 时的 reconstruct 怎么做？overhead 几何？

### cache 并发访问

Miss Status Handling Registers  记录 miss 的状态与数据


&nbsp;   
<a id="04"></a>
## 

&nbsp;   
<a id="05"></a>
## 

&nbsp;   
<a id=""></a>
## 

&nbsp;   
<a id=""></a>
## 

&nbsp;   
<a id=""></a>
## 

&nbsp;   
<a id=""></a>
## 

&nbsp;   
<a id=""></a>
## 

&nbsp;   
<a id=""></a>
## 

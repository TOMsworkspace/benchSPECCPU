---
title: gem5获取bench的访问内存信息
top_img: 'https://cdn.jsdelivr.net/gh/TOMsworkspace/TOMsworkspace.github.io/2020/09/23/gem5获取bench的访问内存信息/figure1.png'
comments: true
cover: 'https://cdn.jsdelivr.net/gh/TOMsworkspace/TOMsworkspace.github.io/2020/09/23/gem5获取bench的访问内存信息/figure1.png'
toc: true
toc_number: true
copyright: true
date: 2020-09-23 19:54:28
updated:
tags: 
        - gem5
        - benchmark
        - trace
        - MemoryAccess
categories: 
        - [GEM5]
keywords: 
        - gem5      
        - trace 
description: 获取benchmark的内存访问信息
---

## 简介

&emsp;获取gem5的bench的内存访问信息的主要原理来自于gem5提供的[调试(debugging)支持](http://www.gem5.org/documentation/learning_gem5/part2/debugging/).

&emsp;gem5提供了的调试标志(debug flags)来输出运行仿真的运行时信息。gem5提供了从内存系统到CPU的全系统仿真能力，提供了许多相关的gebug flags 以使我们可以更好的跟踪我们的仿真。

## gem5 default debug flags

&emsp;gem5提供了许多以实现的debug flags.可以使用如下命令查看,我的环境（gem5 20.0.0.0 ubuntu 18.04 LTS）

```bash
build/X86/gem5.opt --debug-help
```

&emsp;输出如下：

```bash
build/X86/gem5.opt --debug-help

Base Flags:
    Activity: None
    AddrRanges: None
    Annotate: State machine annotation debugging
    AnnotateQ: State machine annotation queue debugging
    AnnotateVerbose: Dump all state machine annotation details
    BaseXBar: None
    Branch: None
    Bridge: None
    CCRegs: None
    CMOS: Accesses to CMOS devices
    Cache: None
    CacheComp: None
    CachePort: None
    CacheRepl: None
    CacheTags: None
    CacheVerbose: None
    Checker: None
    Checkpoint: None
    ClockDomain: None
    CoherentXBar: None
    CommMonitor: None
    Commit: None
    CommitRate: None
    Config: None
    Context: None
    CxxConfig: None
    DMA: None
    DMACopyEngine: None
    DRAM: None
    DRAMPower: None
    DRAMSim2: None
    DRAMState: None
    DVFS: None
    DebugPrintf: None
    Decode: None
    Decoder: Decoder debug output
    DirectedTest: None
    DiskImageRead: None
    DiskImageWrite: None
    DistEthernet: None
    DistEthernetCmd: None
    DistEthernetPkt: None
    Drain: None
    DynInst: None
    ElasticTrace: None
    Ethernet: None
    EthernetCksum: None
    EthernetDMA: None
    EthernetData: None
    EthernetDesc: None
    EthernetEEPROM: None
    EthernetIntr: None
    EthernetPIO: None
    EthernetSM: None
    Event: None
    ......
```

## 在仿真中使用debug flags

&emsp;再命令中加上对应的option --debug-flags 并指定要使用的flag就好了，比如：

```bash
build/X86/gem5.opt --debug-flags=Exec configs/learning_gem5/part1/simple.py | head -n 50
```

## 添加一个debug flags

&emsp; gem5支持自己定义一个flag。具体方法如下：

（1）将要定义的flag包含到Sconscript文件中  

```bash
DebugFlag('Your flags neme')
```  

&emsp;通过在SConscript文件中声明该标志，将自动生成一个C++头文件，使我们可以使用调试标志。头文件在debug目录中，并且具有与我们在SConscript文件中声明的名称相同的名称（和大小写）。因此，我们需要在计划使用debug  flag的任何文件中包含自动生成的头文件。

（2）在要使用的地方包含头文件

```c++
#include "debug/Your flag name.hh"

```

然后在代码中使用，运行时需要重新编译。

```c++
DPRINTF(Your debug name, "Created the hello object\n");
```

 &emsp;这里的DPRINTF是一个输出宏，类似于std::out , 不过带了一个debug flag参数。这样可以将输出与不同的对象绑定，使用不同的flag可以调试不同的对象。

 &emsp;类似于DPRINTF的输出宏还有很多，他们都定义在src/base/trace.hh 中。

 ```c++
src/base/trace.hh:223

#define DTRACE(x) ;
#define DDUMP(x, data, count) ;
#define DPRINTF(x, ...) ;
#define DPRINTFS(x, ...) ;
#define DPRINTFR(...);
#define DDUMPN(data, count) ;
#define DPRINTFN(...) ;
#define DPRINTFNR(...) ;
#define DPRINTF_UNCONDITIONAL(x, ...);
 ```

（3）运行时查看

 &emsp;在运行时在option 中加入--debug-flags = Your debug flag 输出与该flag关联的调试信息。

## 内存访问MemoryAccess flag

&emsp;MemoryAccess是一个用来输出仿真时bench访问内存信息的debug flag，可以在运行仿真时加入--debug-flags=MemoryAccess来获取bench的内存访问信息。

&emsp;这个flags使用于如下的c++文件中

```c++
src/mem/abstract_mem.cc
```

```c++
src/mem/abstract_mem.cc:357

DPRINTF(MemoryAccess, "%s from %s of size %i on address %#x %c\n",
            label, sys->getMasterName(pkt->req->masterId()),
            size, pkt->getAddr(), pkt->req->isUncacheable() ? 'U' : 'C');
```

&emsp;可以修改这里的代码，获得需要的信息。比如只输出

```c++
DPRINTF(MemoryAccess,"%i %s %#x\n",curTick(), label, pkt->getAddr());
```

&emsp;获取对应的内存访问信息。记住每次修改后必须重新编译才能生效。

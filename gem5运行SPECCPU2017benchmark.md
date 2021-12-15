---
title: gem5运行SPECCPU2017benchmark
top_img: 'https://cdn.jsdelivr.net/gh/TOMsworkspace/TOMsworkspace.github.io/2020/10/11/gem5运行SPECCPU2017benchmark/1.png'
comments: true
cover: 'https://cdn.jsdelivr.net/gh/TOMsworkspace/TOMsworkspace.github.io/2020/10/11/gem5运行SPECCPU2017benchmark/figure2.png'
toc: true
toc_number: true
copyright: true
date: 2020-10-11 15:39:06
updated:
tags: 
        - GEM5
        - CPU2017
categories: 
        - "GEM5"
keywords: 
        - gem5
        - GEM5
        - SPECCPU2017
        - spec
        - benchmark
description: '如何在GEM5中运行SPECCPU 2017benchmark,实现两个平台的协同仿真'
---

## **系统环境配置**

&emsp;**硬件：**

- CPU: Intel(R) Core(TM) i7-6700K CPU @ 4.00GHz 4核 8 线程
- 内存16G
- 硬盘 2T HDD+512G SSD

&emsp;**软件：**

- Ubuntu 18.04 LTS
- GCC/G++/FORTRAN 7.5.0
- python 3.7.6
- gem5 20.1.0.0

## **SPEC CPU2017 benchmark**

![figure1.png](https://cdn.jsdelivr.net/gh/TOMsworkspace/TOMsworkspace.github.io/2020/10/11/gem5运行SPECCPU2017benchmark/figure1.png)

&emsp; spec cpu2017 由43个benchmark组成，由20个整型和23个浮点benchmark组成。整型和浮点又分别有 吞吐量（Rate）和速度（Speed）两种类别的bench,分别用于测试 CPU的速度和吞吐量。将43个bench分为整型吞吐量（int_rate）、整型速度（int_speed）、浮点吞吐量（fp_rate）、浮点速度（fp_speed）四个类别。下面是对43个bench的具体介绍:

|SPECrate 2017 Integer|SPECspeed 2017 Integer|Language|KLOC[1]| 应用领域                   |
|:--|:--|:--:|--:|:--                                      |
|500.perlbench_r|600.perlbench_s|C|362|Perl编程语言解释器|
|502.gcc_r|602.gcc_s|C|1304|GUN C编译器|
|505.mcf_r |605.mcf_s|C|3|路径规划|
|520.omnetpp_r|620.omnetpp_s|C++|134|离散事件模拟-计算机网络|
|523.xalancbmk_r|623.xalancbmk_s|C++|520|XSLT将XML转化为HTML|
|525.x264_r|625.x264_s|C|96|视频压缩|
|531.deepsjeng_r|631.deepsjeng_s|C++|10|人工智能：alpha-beta数搜索（国际象棋）|
|541.leela_r|641.leela_s|C++|21|人工智能：蒙特卡洛述搜索（围棋）|
|548.exchange2_r|648.exchange2_s|Fortran|1|人工智能：递归解决方案生成器（数独）|
|557.xz_r|657.xz_s|C|33|通用数据压缩|

|SPECrate 2017 Integer|SPECspeed 2017 Integer|Language|KLOC[1]| 应用领域                   |
|:--|:--|:--:|--:|:--|
|503.bwaves_r|603.bwaves_s|Fortran|1|爆炸模型|
|507.cactuBSSN_r|607.cactuBSSN_s|C++ C Fortran|257|物理：相对论|
|508.namd_r ||C++|8|分子动力学|
|510.parest_r||C++|427|生物医学成像|
|511.povray_r||C++ C|170|光学追踪|
|519.lbm_r|619.lbm_s|C|1|流体动力学|
|521.wrf_r|621.wrf_s|Fortran C|991|天气预报|
|526.blender_r||C++ C|1577|3D动画渲染|
|527.cam4_r|627.cam4_s|Fortran C|407|大气建模|
||628.pop2_s|Fortran C|338|大规模海洋模拟（气候水平）|
|538.imagick_r|638.imgick_s|C|259|图像处理|
|544.nab_r|644.nab_s|C|24|分子动力学|
|549.fotonik3d_r|649.fotonik3d_s|Fortran|14|计算电磁学|
|554.roms_r|654.roms_s|Fortran|210|区域海洋模拟|

[1]KLOC=编译时使用的源码长度（包括空格和注释）/1000

## **编译运行**

&emsp;**（1）spec cpu 2017编译**

&emsp;主要按照[官方文档](https://www.spec.org/cpu2017/Docs/quick-start.html)的说明。

&emsp;**（2）gem5的安装运行**

&emsp;[官方文档](http://www.gem5.org/documentation/learning_gem5/part1/building/)

&emsp;**(3)自动化脚本**

&emsp;关于43个benmark的输入数据及命令格式：[SPEC CPU2017 commands](https://cdn.jsdelivr.net/gh/TOMsworkspace/TOMsworkspace.github.io/2020/10/11/gem5运行SPECCPU2017benchmark/SPEC2017_commands.pdf)  
&emsp;其他更多细节也可参照我之前的文章，[在GME5运行SPEC CPU 2006 benchmark](https://tomsworkspace.github.io/2020/09/22/gem5%E8%BF%90%E8%A1%8CSPECCPU2006benchmark/) [CSDN链接](https://blog.csdn.net/qq_38877888/article/details/108759538)

## **协同仿真**

&emsp;目前已经成功编译并运行大部分bench，但是还有3个bench编译成功但是无法正常运行。具体如下：

|BENCHMARK|错误原因|
|:--:|:----|
|510.parest_r|An error occurred in line <377> of file <source/base/utilities.cc> in    function                                                                           The violated condition was: cpuinfo                                                                            The name and call sequence of the exception was: ExcIO()|
|527.cam4_r|At line 869 of file shr_file_mod.fppized.f90 (unit = 98) Fortran runtime error: Cannot open file 'cpl_modelio.nml': No such file or directory|
|627.cam4_s|At line 869 of file shr_file_mod.fppized.f90 (unit = 98) Fortran runtime error: Cannot open file 'cpl_modelio.nml': No such file or directory|

## **主要问题**

### （1）spec cpu 2017 编译错误

&emsp;主要可能导致的原因如下：

- gcc/g++/gfortran 路径错误，从提供的配置文件直接拷贝时需要修改一下编译工具链的路径。
- 优化选项导致的错误，比如-fno-tree-loop-vectorize优化选项，将其去掉。

### （2）protobuf导致的gem5编译错误

&emsp;主要可能导致的原因如下：

- protocbuf版本太低或者太高
- 系统中存在多个版本的protobuf

&emsp;由于protobuf会被在很多地方采用(如python），所以系统中可能存在多个版本的protocbuf。导致编译时使用了不同版本的库或工具导致编译出错。编译时可以从这个思路排查。可以卸载其他版本的protobuf或者使用源码编译安装。如果实在无法解决问题，可以修改gem5安装目录下的SConstruct文件禁用protobuf组件或卸载protobuf可以使编译通过，这不会影响gem5的主要功能。

### （3）panic Unrecognized instruction executed

```bash
panic: Unrecognized/invalid instruction executed:
{
    leg = 0x10,
    rex = 0,
    vex/xop = 0x5,
    op = {
        type = three byte 0f3a,
        op = 0x18,
    },
    modRM = 0,
    sib = 0,
    immediate = 0,
    displacement = 0
    dispSize = 0
}
```

&emsp;这个问题出现在使用Gem5运行spec cpu 2017 的 benchmark时，出现错误的原因主要是cpu2017的benchmark编译时生成的代码指令中包含gem5目前不支持的无效指令，很可能是SSE或AVX指令。这个问题可以通过在cpu 2017的配置文件中加上[-march选项](https://gcc.gnu.org/onlinedocs/gcc/x86-Options.html)来生成支持的代码指令。

```bash
#vim your-cpu2017-config.cfg

default=base:         # flags for all base
   OPTIMIZE       = -g -O3 -march=x86-64 -fno-unsafe-math-optimizations

```

&emsp;参考  
[1]<https://gem5-users.gem5.narkive.com/jO22f7kV/spec2017-on-gem5-se-mode#post5>  
[2]<https://www.mail-archive.com/gem5-dev@gem5.org/msg28987.html>  
[3]<https://www.mail-archive.com/gem5-users@gem5.org/msg14911.html>
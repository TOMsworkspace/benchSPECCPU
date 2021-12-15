---
title: gem5运行SPECCPU2006benchmark
top_img: 'https://cdn.jsdelivr.net/gh/TOMsworkspace/TOMsworkspace.github.io/2020/09/22/gem5运行SPECCPU2006benchmark/figure2.png'
comments: true
cover: 'https://cdn.jsdelivr.net/gh/TOMsworkspace/TOMsworkspace.github.io/2020/09/22/gem5运行SPECCPU2006benchmark/figure2.png'
toc: true
toc_number: true
copyright: true
date: 2020-09-22 21:55:33
updated:
tags: 
    - GEM5
    - CCPU2006
categories: 
    - "GEM5"
keywords:
    - gem5
    - GEM5
    - SPECCPU2006
    - spec
    - benchmark
description: '如何在GEM5中运行SPECCPU 2006benchmark,实现两个平台的协同仿真'
---


# **gem5运行SPECCPU 2006benchmark**

## **前言**

&emsp;最近在做实验，准备在GEM5中运行SPECCPU 2006的benchmark。

![figure1.png](https://cdn.jsdelivr.net/gh/TOMsworkspace/TOMsworkspace.github.io/2020/09/22/gem5运行SPECCPU2006benchmark/figure1.png)

## **系统环境配置**

&emsp;**硬件：** CPU I5-4500 4核 内存8G 硬盘 1T
&emsp;**软件：** Ubuntu 18.04 LTS GCC/G++5.5 python2.7 gem5 20.0.0.3

## **编译安装**

&emsp;[GEM5基本安装运行](http://www.gem5.org/documentation/learning_gem5/part1/building/)

&emsp;[SPEC CPU 2006 benchmark基本安装运行](https://www.spec.org/cpu2006/Docs/)

&emsp;[SPEC runspec命令](https://www.spec.org/cpu2006/Docs/runspec.html)  
该命令是用来编译和运行spec cpu 2006的。

## **协同仿真**

目前已编译成功并运行的benchmark

|int benchmark|language|success(true or false)|
|:---:|:--:|:-:|
|400.perlbench|C|true|
|401.bzip2|C|true|
|403.gcc|C|true|
|429.mcf|C|true|
|445.gobmk|C|true|
|456.hmmer|C|true|
|458.sjeng|C|true|
|462.libquantum|C|true|
|464.h264ref|C|true|
|471.omnetpp|C++|true|
|473.astar|C++|true|
|483.xalancbmk|C++|true|

|fp benchmark|language|success(true or false)|
|:-:|:---:|:--:|
|410.bwaves|Fortran|true|
|416.gamess|Fortran|true|
|433.milc|C|true|
|434.zeusmp|Fortran|true|
|435.gromacs|C/Fortran|true|
|436.cactusADM|C/Fortran|true|
|437.leslie3d|Fortran|true|
|444.namd|C++|true|
|447.dealII|C++|false|
|450.soploex|C++|true|
|453.povray|C++|true|
|454.calculix|C/Fortran|true|
|459.GemsFDTD|Fortran|true|
|465.tonto|Fortran|true|
|470.lbm|C|true|
|481.wrf|C/Fortran|true|
|482.sphinx3|C|true|

### **gem5 SE/FS 仿真模式**  

gem5有系统调用仿真模式（System Call Emulation (SE) Mode）和全系统仿真模式（Full System (FS) Mode）。可以支持从CPU到内外存的全系统体系结构仿真。你可以根据自己的需要对GEM5源码做相应的扩展，然后运行SE/FS仿真。

#### **运行仿真的格式**

```bash
# <gem5 binary> [gem5 options] <simulation script> [script options]
```

每个仿真包括一个基于特定体系结构编译的gem5二进制和一个仿真python script，以及相应的options。

#### **gem5 Options**

使用"gem5 binary -h"查看options,例如：

```bash
 # build/X86/gem5.opt -h
Usage
=====
  gem5.opt [gem5 options] script.py [script options]

gem5 is copyrighted software; use the --copyright option for details.
Options
=======
--version               show programm's version number and exit
--help, -h              show this help message and exit
--build-info, -B        Show build information
--copyright, -C         Show full copyright information
--readme, -R            Show the readme
--outdir=DIR, -d DIR    Set the output directory to DIR [Default: m5out]
--redirect-stdout, -r   Redirect stdout (& stderr, without -e) to file
--redirect-stderr, -e   Redirect stderr to file
--stdout-file=FILE      Filename for -r redirection [Default: simout]
--stderr-file=FILE      Filename for -e redirection [Default: simerr]
--listener-mode={on,off,auto}
                        Port (e.g., gdb) listener mode (auto: Enable if
                        running interactively) [Default: auto]
--listener-loopback-only
                        Port listeners will only accept connections over the
                        loopback device
--interactive, -i       Invoke the interactive interpreter after running the
                        script
--pdb                   Invoke the python debugger before running the script
--path=PATH[:PATH], -p PATH[:PATH]
                        Prepend PATH to the system path when invoking the
                        script
--quiet, -q             Reduce verbosity
--verbose, -v           Increase verbosity
...
```

#### **Script Options**

gem5已经提供了很多实例的运行脚本，存在$gem5path/config/example/路径下，其中包括运行SE和FS模式的脚本se.py和fs.py。下面是se.py的options。

```bash
Usage: se.py [options]

Options:
  -h, --help            show this help message and exit
  -n NUM_CPUS, --num-cpus=NUM_CPUS
  --sys-voltage=SYS_VOLTAGE
                        Top-level voltage for blocks running at system
                        power supply
  --sys-clock=SYS_CLOCK
                        Top-level clock for blocks running at system
                        speed
  --list-mem-types      List available memory types
  --mem-type=MEM_TYPE   type of memory to use
  --mem-channels=MEM_CHANNELS
                        number of memory channels
  --mem-ranks=MEM_RANKS
                        number of memory ranks per channel
  --mem-size=MEM_SIZE   Specify the physical memory size (single memory)
  --enable-dram-powerdown
                        Enable low-power states in DRAMCtrl
  --mem-channels-intlv=MEM_CHANNELS_INTLV
                        Memory channels interleave
  --memchecker
  --external-memory-system=EXTERNAL_MEMORY_SYSTEM
                        use external ports of this port_type for caches
  --tlm-memory=TLM_MEMORY
                        use external port for SystemC TLM cosimulation
  --caches
  --l2cache
  --num-dirs=NUM_DIRS
  --num-l2caches=NUM_L2CACHES
  --num-l3caches=NUM_L3CACHES
  --l1d_size=L1D_SIZE
  --l1i_size=L1I_SIZE
  --l2_size=L2_SIZE
  --l3_size=L3_SIZE
  --l1d_assoc=L1D_ASSOC
  --l1i_assoc=L1I_ASSOC
  --l2_assoc=L2_ASSOC
  --l3_assoc=L3_ASSOC
  --cacheline_size=CACHELINE_SIZE
  --ruby
  ......

  -c CMD, --cmd=CMD     The binary to run in syscall emulation mode.
  -o OPTIONS, --options=OPTIONS
                        The options to pass to the binary, use " "
                        around the entire string
  -e ENV, --env=ENV     Initialize workload environment from text file.
  -i INPUT, --input=INPUT
                        Read stdin from a file.
  --output=OUTPUT       Redirect stdout to a file.
  --errout=ERROUT       Redirect stderr to a file.
  ......
```

### **spec cpu2006静态编译**

spec cpu 2006benchmark 使用命令[runspec](https://www.spec.org/cpu2006/Docs/runspec.html)来进行编译和运行。

runspec命令使用一个以.cfg结尾的配置文件来对benchmark进行编译。默认在$spec安装目录/config/ 下提供了一些配置文件的实例，可以根据需要对其进行修改。

要使spec cpu 2006的bench能在gem5里运行，需要对每个bench进行静态编译。对于配置文件主要做如下的更改：

```bash
## Base is low opt
default=base=default=default:
COPTIMIZE    = -O2 -fno-strict-aliasing
CXXOPTIMIZE  = -O2 -fno-strict-aliasing
FOPTIMIZE    = -O2 -fno-strict-aliasing

改为
COPTIMIZE    = -O2 -fno-strict-aliasing -static
CXXOPTIMIZE  = -O2 -fno-strict-aliasing -static
FOPTIMIZE    = -O2 -fno-strict-aliasing -static
```

### **运行一个例子**

#### **runspec运行**

 编译并运行一个bench,以401.bzip2为例

```bash
# 编译perlbench
runspec --config=xxx.cfg --action=build --tune=base bzip2
#运行perlbench
runspec --config=xxx.cfg --size=ref -I --tune=base bzip2
```

这里的xxx.cfg是对应的配置文件，指定了包括编译和运行SPEC CPU的环境参数。还可以使用int,fp,all来替代bzip2编译或运行整型bench，浮点或所有bench。

#### **二进制运行**

当编译一个bench会在对应目录下生成一个run子文件夹，里面包含编译生成的可执行文件和对应的输入。想了解每个bench 到底做了什么可以直接运行这个可执行文件。以bzip2为例

```bash
cd $PATH_TO_YOUR_SPEC_CPU2006_INSTALL/benchspec/CPU2006/401. bzip2/run/run__base.amd64-m64-gcc43-nn.0000/

./bzip2_base.amd64-m64-gcc43-nn control
```

对于不同的bench，有不同的运行输入，具体可见[官方说明](https://www.spec.org/cpu2006/Docs/)或安装目录下每个bench下的Docs子文件夹。要使这些bench在gem5中被正确地执行，指定正确的输入是很重要的。否则即使能跑起来也没有正确的输入。使用runspec运行bench 只需要使用--size选项来指定输入就好了。

#### **GEM5运行**

接下来在gem5中运行一个bench，还是以bzip2为例。使用config/example/se.py作为配置文件。命令很长，将其写成shell 脚本。

```bash
#!/bin/bash
#run_bzip2.sh
gem5path=/home/tom/gem5
spec2006path=$gem5path/cpu2006/benchspec/CPU2006
outdir=/home/tom/Desktop/spec2006Trace
bench=401.bzip2
ben_suffix=run/run_base.amd64-m64-gcc43-nn.0000/
exe=bzip_base.amd64-m64-gcc43-nn

$gem5path/build/X86/gem5.opt \
--debug-flags=MemoryAccess \
--outdir=$outdir --debug-file=401.bzip2.out \
$gem5path/configs/example/se.py \
-c "$spec2006path/$bench/$ben_suffix/$exe" \
-o "" \
-i "$spec2006path/$bench/$ben_suffix/control" \
--cpu-nums=1 \
--cpu-type=TimingSimpleCPU \
--caches \
--cacheline_size=64 \
--l1d_size=64kB --l1i_size=32kB --l1i_assoc=8 --l1d_assoc=8 \
--l2cache --l2_size=2MB --l2_assoc=8 \
--l3_size=32MB --l3_assoc=4 \
--mem-size=4096MB
```

 其中的几个参数解释如下：

- -c 指定gem5运行的二进制文件
- -o 运行二进制文件的选项
- -i 二进制文件的输入文件，把文件当成输入
  
  要想运行其余的bench,主要改动这三个选项。

### **自动化脚本**

显然，对于每个bench都写一个shell太麻烦了，每个bench还需要指定不同的输入，而且没办法同时运行个bench进行多线程仿真。考虑用python脚本来解决这个问题。参考了网上的做法，但是都是将bench当成单线程来模拟，并没有提供多线程的解决方案。参考[现有方案](https://markgottscho.wordpress.com/2014/09/20/tutorial-easily-running-spec-cpu2006-benchmarks-in-the-gem5-simulator/)，在此基础上写了一个同时支持单线程和多线程的版本。

#### **配置文件spec06_config.py**  

首先，从config/example/se.py配置文件中复制一个配置文件spec_config.py。并对其进行更改。

```python
# Copyright (c) 2012-2013 ARM Limited
# All rights reserved.
#
# The license below extends only to copyright in the software and shall
# not be construed as granting a license to any other intellectual
# property including but not limited to intellectual property relating
# to a hardware implementation of the functionality of the software
# licensed hereunder.  You may use the software subject to the license
# terms below provided that you ensure that this notice is replicated
# unmodified and in its entirety in all distributions of the software,
# modified or unmodified, in source code or in binary form.
#
# Copyright (c) 2006-2008 The Regents of The University of Michigan
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are
# met: redistributions of source code must retain the above copyright
# notice, this list of conditions and the following disclaimer;
# redistributions in binary form must reproduce the above copyright
# notice, this list of conditions and the following disclaimer in the
# documentation and/or other materials provided with the distribution;
# neither the name of the copyright holders nor the names of its
# contributors may be used to endorse or promote products derived from
# this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
# "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
# LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
# A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
# OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
# SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
# LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
# DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
# THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

# Simple test script
#
# "m5 test.py"

from __future__ import print_function
from __future__ import absolute_import

import optparse
import sys
import os

import m5
from m5.defines import buildEnv
from m5.objects import *
from m5.params import NULL
from m5.util import addToPath, fatal, warn

addToPath('../')

from ruby import Ruby

from common import Options
from common import Simulation
from common import CacheConfig
from common import CpuConfig
from common import ObjectList
from common import MemConfig
from common.FileSystemConfig import config_filesystem
from common.Caches import *
from common.cpu2000 import *
import spec06_benchmarks

# ...snip...
parser = optparse.OptionParser()
Options.addCommonOptions(parser)
Options.addSEOptions(parser)


# NAVIGATE TO THIS POINT

# ...snip...

parser.add_option("-b", "--benchmark", type="string", default="", help="The SPEC benchmark to be loaded.")
parser.add_option("--benchmark_stdout", type="string", default="", help="Absolute path for stdout redirection for the benchmark.")
parser.add_option("--benchmark_stderr", type="string", default="", help="Absolute path for stderr redirection for the benchmark.")

def get_processes(options):
    """Interprets provided options and returns a list of processes"""

    multiprocesses = []
    outputs = []
    errouts = []

    workloads = options.benchmark.split(';')

    if options.benchmark_stdout != "":
        outputs = options.benchmark_stdout.split(';')
    elif options.output != "":
        outputs = options.output.split(';')

    if options.benchmark_stderr != "":
        errouts = options.benchmark_stderr.split(';')
    elif options.errout != "":
        errouts = options.errout.split(';')

    idx = 0
    for wrkld in workloads:

        if wrkld:
            print('Selected SPEC_CPU2006 benchmark')
            if wrkld == 'perlbench':
                print('--> perlbench')
                process = spec06_benchmarks.perlbench
            elif wrkld == 'bzip2':
                print('--> bzip2')
                process = spec06_benchmarks.bzip2
            elif wrkld == 'gcc':
                print
                '--> gcc'
                process = spec06_benchmarks.gcc
            elif wrkld == 'bwaves':
                print
                '--> bwaves'
                process = spec06_benchmarks.bwaves
            elif wrkld == 'gamess':
                print
                '--> gamess'
                process = spec06_benchmarks.gamess
            elif wrkld == 'mcf':
                print
                '--> mcf'
                process = spec06_benchmarks.mcf
            elif wrkld == 'milc':
                print
                '--> milc'
                process = spec06_benchmarks.milc
            elif wrkld == 'zeusmp':
                print
                '--> zeusmp'
                process = spec06_benchmarks.zeusmp
            elif wrkld == 'gromacs':
                print
                '--> gromacs'
                process = spec06_benchmarks.gromacs
            elif wrkld == 'cactusADM':
                print
                '--> cactusADM'
                process = spec06_benchmarks.cactusADM
            elif wrkld == 'leslie3d':
                print
                '--> leslie3d'
                process = spec06_benchmarks.leslie3d
            elif wrkld == 'namd':
                print
                '--> namd'
                process = spec06_benchmarks.namd
            elif wrkld == 'gobmk':
                print
                '--> gobmk'
                process = spec06_benchmarks.gobmk
            elif wrkld == 'dealII':
                print
                '--> dealII'
                process = spec06_benchmarks.dealII
            elif wrkld == 'soplex':
                print
                '--> soplex'
                process = spec06_benchmarks.soplex
            elif wrkld == 'povray':
                print
                '--> povray'
                process = spec06_benchmarks.povray
            elif wrkld == 'calculix':
                print
                '--> calculix'
                process = spec06_benchmarks.calculix
            elif wrkld == 'hmmer':
                print
                '--> hmmer'
                process = spec06_benchmarks.hmmer
            elif wrkld == 'sjeng':
                print
                '--> sjeng'
                process = spec06_benchmarks.sjeng
            elif wrkld == 'GemsFDTD':
                print
                '--> GemsFDTD'
                process = spec06_benchmarks.GemsFDTD
            elif wrkld == 'libquantum':
                print
                '--> libquantum'
                process = spec06_benchmarks.libquantum
            elif wrkld == 'h264ref':
                print
                '--> h264ref'
                process = spec06_benchmarks.h264ref
            elif wrkld == 'tonto':
                print
                '--> tonto'
                process = spec06_benchmarks.tonto
            elif wrkld== 'lbm':
                print
                '--> lbm'
                process = spec06_benchmarks.lbm
            elif wrkld == 'omnetpp':
                print
                '--> omnetpp'
                process = spec06_benchmarks.omnetpp
            elif wrkld == 'astar':
                print
                '--> astar'
                process = spec06_benchmarks.astar
            elif wrkld == 'wrf':
                print
                '--> wrf'
                process = spec06_benchmarks.wrf
            elif wrkld == 'sphinx3':
                print
                '--> sphinx3'
                process = spec06_benchmarks.sphinx3
            elif wrkld== 'xalancbmk':
                print
                '--> xalancbmk'
                process = spec06_benchmarks.xalancbmk
            elif wrkld == 'specrand_i':
                print
                '--> specrand_i'
                process = spec06_benchmarks.specrand_i
            elif wrkld == 'specrand_f':
                print
                '--> specrand_f'
                process = spec06_benchmarks.specrand_f
            else:
                print
                "No recognized SPEC2006 benchmark selected! Exiting."
                sys.exit(1)
            process.cwd = os.getcwd()

            if len(outputs) > idx:
                process.output = outputs[idx]
            if len(errouts) > idx:
                process.errout = errouts[idx]

            multiprocesses.append(process)
            idx += 1

        else:
            print >> sys.stderr, "Need --benchmark switch to specify SPEC CPU2006 workload. Exiting!\n"
            sys.exit(1)

    if options.smt:
        assert(options.cpu_type == "DerivO3CPU")
        return multiprocesses, idx
    else:
        return multiprocesses, 1


#parser = optparse.OptionParser()
#Options.addCommonOptions(parser)
#Options.addSEOptions(parser)

if '--ruby' in sys.argv:
    Ruby.define_options(parser)

(options, args) = parser.parse_args()

if args:
    print("Error: script doesn't take any positional arguments")
    sys.exit(1)


multiprocesses, numThreads = get_processes(options)


(CPUClass, test_mem_mode, FutureClass) = Simulation.setCPUClass(options)
CPUClass.numThreads = numThreads

# Check -- do not allow SMT with multiple CPUs
if options.smt and options.num_cpus > 1:
    fatal("You cannot use SMT with multiple CPUs!")

np = options.num_cpus
system = System(cpu = [CPUClass(cpu_id=i) for i in range(np)],
                mem_mode = test_mem_mode,
                mem_ranges = [AddrRange(options.mem_size)],
                cache_line_size = options.cacheline_size,
                workload = NULL)

if numThreads > 1:
    system.multi_thread = True

# Create a top-level voltage domain
system.voltage_domain = VoltageDomain(voltage = options.sys_voltage)

# Create a source clock for the system and set the clock period
system.clk_domain = SrcClockDomain(clock =  options.sys_clock,
                                   voltage_domain = system.voltage_domain)

# Create a CPU voltage domain
system.cpu_voltage_domain = VoltageDomain()

# Create a separate clock domain for the CPUs
system.cpu_clk_domain = SrcClockDomain(clock = options.cpu_clock,
                                       voltage_domain =
                                       system.cpu_voltage_domain)

# If elastic tracing is enabled, then configure the cpu and attach the elastic
# trace probe
#if options.elastic_trace_en:
#    CpuConfig.config_etrace(CPUClass, system.cpu, options)

# All cpus belong to a common cpu_clk_domain, therefore running at a common
# frequency.
for cpu in system.cpu:
    cpu.clk_domain = system.cpu_clk_domain

if ObjectList.is_kvm_cpu(CPUClass) or ObjectList.is_kvm_cpu(FutureClass):
    if buildEnv['TARGET_ISA'] == 'x86':
        system.kvm_vm = KvmVM()
        for process in multiprocesses:
            process.useArchPT = True
            process.kvmInSE = True
    else:
        fatal("KvmCPU can only be used in SE mode with x86")

# Sanity check
if options.simpoint_profile:
    if not ObjectList.is_noncaching_cpu(CPUClass):
        fatal("SimPoint/BPProbe should be done with an atomic cpu")
    if np > 1:
        fatal("SimPoint generation not supported with more than one CPUs")

for i in range(np):
    if options.smt:
        system.cpu[i].workload = multiprocesses
    elif len(multiprocesses) == 1:
        system.cpu[i].workload = multiprocesses[0]
    else:
        system.cpu[i].workload = multiprocesses[i]

    if options.simpoint_profile:
        system.cpu[i].addSimPointProbe(options.simpoint_interval)

    if options.checker:
        system.cpu[i].addCheckerCpu()

    if options.bp_type:
        bpClass = ObjectList.bp_list.get(options.bp_type)
        system.cpu[i].branchPred = bpClass()

    if options.indirect_bp_type:
        indirectBPClass = \
            ObjectList.indirect_bp_list.get(options.indirect_bp_type)
        system.cpu[i].branchPred.indirectBranchPred = indirectBPClass()

    system.cpu[i].createThreads()

if options.ruby:
    Ruby.create_system(options, False, system)
    assert(options.num_cpus == len(system.ruby._cpu_ports))

    system.ruby.clk_domain = SrcClockDomain(clock = options.ruby_clock,
                                        voltage_domain = system.voltage_domain)
    for i in range(np):
        ruby_port = system.ruby._cpu_ports[i]

        # Create the interrupt controller and connect its ports to Ruby
        # Note that the interrupt controller is always present but only
        # in x86 does it have message ports that need to be connected
        system.cpu[i].createInterruptController()

        # Connect the cpu's cache ports to Ruby
        system.cpu[i].icache_port = ruby_port.slave
        system.cpu[i].dcache_port = ruby_port.slave
        if buildEnv['TARGET_ISA'] == 'x86':
            system.cpu[i].interrupts[0].pio = ruby_port.master
            system.cpu[i].interrupts[0].int_master = ruby_port.slave
            system.cpu[i].interrupts[0].int_slave = ruby_port.master
            system.cpu[i].itb.walker.port = ruby_port.slave
            system.cpu[i].dtb.walker.port = ruby_port.slave
else:
    MemClass = Simulation.setMemClass(options)
    system.membus = SystemXBar()
    system.system_port = system.membus.slave
    CacheConfig.config_cache(options, system)
    MemConfig.config_mem(options, system)
    config_filesystem(system, options)

if options.wait_gdb:
    for cpu in system.cpu:
        cpu.wait_for_remote_gdb = True

root = Root(full_system = False, system = system)
Simulation.run(options, root, system, FutureClass)
```

#### **bench输入**

然后写一个脚本解决bench的输入问题，并在spec06_config.py中使用。

```python
##spec06_benchmarks.py
import m5
from m5.objects import *

# These three directory paths are not currently used.
# gem5_dir = '<FULL_PATH_TO_YOUR_GEM5_INSTALL>'
# spec_dir = '<FULL_PATH_TO_YOUR_SPEC_CPU2006_INSTALL>'
# out_dir = '<FULL_PATH_TO_DESIRED_OUTPUT_DIRECTORY>'

GEM5_DIR='/home/tom/gem5'
#<PATH_TO_YOUR_GEM5_INSTALL>                          # Install location of gem5
SPEC_DIR='/home/tom/cpu2006/benchspec/CPU2006/'

dir_suffix = '/run/run_base_ref_amd64-m64-gcc43-nn.0000/'
exe_suffix = '_base.amd64-m64-gcc43-nn'

# temp
# binary_dir = spec_dir
# data_dir = spec_dir

# 400.perlbench
perlbench = Process(pid=400)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'400.perlbench'+dir_suffix
perlbench.executable = fullpath+'perlbench' + exe_suffix
# TEST CMDS
# perlbench.cmd = [perlbench.executable] + ['-I.', fullpath+'/lib',fullpath+ 'attrs.pl']
# REF CMDS
perlbench.cmd = [perlbench.executable] + ['-I'+fullpath+'/lib', fullpath+'/checkspam.pl', '2500', '5', '25', '11', '150', '1', '1', '1', '1']
# perlbench.cmd = [perlbench.executable] + ['-I'+fullpath+'/lib',fullpath+ 'diffmail.pl', '4', '800', '10', '17', '19', '300']
# perlbench.cmd = [perlbench.executable] + ['-I'+fullpath+'/lib',fullpath+ 'splitmail.pl', '1600', '12', '26', '16', '4500']
# perlbench.output = out_dir+'perlbench.out'

# 401.bzip2
bzip2 = Process(pid=401)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'401.bzip2'+dir_suffix
bzip2.executable = fullpath+'bzip2' + exe_suffix
# TEST CMDS
# bzip2.cmd = [bzip2.executable] + [fullpath+'input.program', '5']
# REF CMDS
bzip2.cmd = [bzip2.executable] + [fullpath+'input.source', '280']
# bzip2.cmd = [bzip2.executable] + [fullpath+'chicken.jpg', '30']
# bzip2.cmd = [bzip2.executable] + [fullpath+'liberty.jpg', '30']
# bzip2.cmd = [bzip2.executable] + [fullpath+'input.program', '280']
# bzip2.cmd = [bzip2.executable] + [fullpath+'text.html', '280']
# bzip2.cmd = [bzip2.executable] + [fullpath+'input.combined', '200']
# bzip2.output = out_dir + 'bzip2.out'

# 403.gcc
gcc = Process(pid=403)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'403.gcc'+dir_suffix
gcc.executable = fullpath+'gcc' + exe_suffix
# TEST CMDS
# gcc.cmd = [gcc.executable] + [fullpath+'cccp.i', '-o',fullpath+ 'cccp.s']
# REF CMDS
gcc.cmd = [gcc.executable] + [fullpath+'166.i', '-o', fullpath+'166.s']
# gcc.cmd = [gcc.executable] + [fullpath+'200.i', '-o',fullpath+ '200.s']
# gcc.cmd = [gcc.executable] + [fullpath+'c-typeck.i', '-o',fullpath+ 'c-typeck.s']
# gcc.cmd = [gcc.executable] + [fullpath+'cp-decl.i', '-o',fullpath+ 'cp-decl.s']
# gcc.cmd = [gcc.executable] + [fullpath+'expr.i', '-o',fullpath+ 'expr.s']
# gcc.cmd = [gcc.executable] + [fullpath+'expr2.i', '-o',fullpath+ 'expr2.s']
# gcc.cmd = [gcc.executable] + [fullpath+'g23.i', '-o',fullpath+ 'g23.s']
# gcc.cmd = [gcc.executable] + [fullpath+'s04.i', '-o',fullpath+ 's04.s']
# gcc.cmd = [gcc.executable] + [fullpath+'scilab.i', '-o',fullpath+ 'scilab.s']
# gcc.output = out_dir + 'gcc.out'

# 410.bwaves
bwaves = Process(pid=410)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'410.bwaves'+dir_suffix
bwaves.executable = fullpath+'bwaves' + exe_suffix
# TEST CMDS
# bwaves.cmd = [bwaves.executable]
# REF CMDS
bwaves.cmd = [bwaves.executable]
# bwaves.output = out_dir + 'bwaves.out'

# 416.gamess
gamess = Process(pid=416)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'416.gamess'+dir_suffix
gamess.executable = fullpath+'gamess' + exe_suffix
# TEST CMDS
# gamess.cmd = [gamess.executable]
# gamess.input = fullpath+'exam29.config'
# REF CMDS
gamess.cmd = [gamess.executable]
gamess.input = fullpath+'cytosine.2.config'
# gamess.cmd = [gamess.executable]
# gamess.input = fullpath+'h2ocu2+.gradient.config'
# gamess.cmd = [gamess.executable]
# gamess.input = fullpath+'triazolium.config'
# gamess.output = out_dir + 'gamess.out'

# 429.mcf
mcf = Process(pid=429)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'429.mcf'+dir_suffix
mcf.executable = fullpath+'mcf' + exe_suffix
# TEST CMDS
# mcf.cmd = [mcf.executable] + [fullpath+'inp.in']
# REF CMDS
mcf.cmd = [mcf.executable] + [fullpath+'inp.in']
# mcf.output = out_dir + 'mcf.out'

# 433.milc
milc = Process(pid=433)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'433.milc'+dir_suffix
milc.executable = fullpath+'milc' + exe_suffix
# TEST CMDS
# milc.cmd = [milc.executable]
# milc.input = fullpath+'su3imp.in'
# REF CMDS
milc.cmd = [milc.executable]
milc.input = fullpath+'su3imp.in'
# milc.output = out_dir + 'milc.out'

# 434.zeusmp
zeusmp = Process(pid=434)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'434.zeusmp'+dir_suffix
zeusmp.executable = fullpath+'zeusmp' + exe_suffix
# TEST CMDS
# zeusmp.cmd = [zeusmp.executable]
# REF CMDS
zeusmp.cmd = [zeusmp.executable]
# zeusmp.output = out_dir + 'zeusmp.out'

# 435.gromacs
gromacs = Process(pid=435)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'435.gromacs'+dir_suffix
gromacs.executable = fullpath+'gromacs' + exe_suffix
# TEST CMDS
# gromacs.cmd = [gromacs.executable] + ['-silent','-deffnm',fullpath+ 'gromacs', '-nice','0']
# REF CMDS
gromacs.cmd = [gromacs.executable] + ['-silent', '-deffnm', fullpath+'gromacs', '-nice', '0']
# gromacs.output = out_dir + 'gromacs.out'

# 436.cactusADM
cactusADM = Process(pid=436)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'436.cactusADM'+dir_suffix
cactusADM.executable = fullpath+'cactusADM' + exe_suffix
# TEST CMDS
# cactusADM.cmd = [cactusADM.executable] + [fullpath+'benchADM.par']
# REF CMDS
cactusADM.cmd = [cactusADM.executable] + [fullpath+'benchADM.par']
# cactusADM.output = out_dir + 'cactusADM.out'

# 437.leslie3d
leslie3d = Process(pid=437)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'437.leslie3d'+dir_suffix
leslie3d.executable = fullpath+'leslie3d' + exe_suffix
# TEST CMDS
# leslie3d.cmd = [leslie3d.executable]
# leslie3d.input = fullpath+'leslie3d.in'
# REF CMDS
leslie3d.cmd = [leslie3d.executable]
leslie3d.input = fullpath+'leslie3d.in'
# leslie3d.output = out_dir + 'leslie3d.out'

# 444.namd
namd = Process(pid=444)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'444.namd'+dir_suffix
namd.executable = fullpath+'namd' + exe_suffix
# TEST CMDS
# namd.cmd = [namd.executable] + ['--input', fullpath+'namd.input', '--output', fullpath+'namd.out', '--iterations', '1']
# REF CMDS
namd.cmd = [namd.executable] + ['--input', fullpath+'namd.input', '--output', fullpath+'namd.out', '--iterations', '38']
# namd.output = out_dir + 'namd.out'

# 445.gobmk
gobmk = Process(pid=445)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'445.gobmk'+dir_suffix
gobmk.executable = fullpath+'gobmk' + exe_suffix
# TEST CMDS
# gobmk.cmd = [gobmk.executable] + ['--quiet','--mode', 'gtp']
# gobmk.input = fullpath+'dniwog.tst'
# REF CMDS
gobmk.cmd = [gobmk.executable] + ['--quiet', '--mode', 'gtp']
gobmk.input = fullpath+'13x13.tst'
# gobmk.cmd = [gobmk.executable] + ['--quiet','--mode', 'gtp']
# gobmk.input = fullpath+'nngs.tst'
# gobmk.cmd = [gobmk.executable] + ['--quiet','--mode', 'gtp']
# gobmk.input = fullpath+'score2.tst'
# gobmk.cmd = [gobmk.executable] + ['--quiet','--mode', 'gtp']
# gobmk.input = fullpath+'trevorc.tst'
# gobmk.cmd = [gobmk.executable] + ['--quiet','--mode', 'gtp']
# gobmk.input = fullpath+'trevord.tst'
# gobmk.output = out_dir + 'gobmk.out'

# 447.dealII
####### NOT WORKING #########
dealII = Process(pid=447)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'447.dealII'+dir_suffix
dealII.executable = fullpath+'dealII' + exe_suffix
# TEST CMDS
####### NOT WORKING #########
dealII.cmd = [gobmk.executable]+['8']
# REF CMDS
####### NOT WORKING #########
# dealII.output = out_dir + 'dealII.out'

# 450.soplex
soplex = Process(pid=450)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'450.soplex'+dir_suffix
soplex.executable = fullpath+'soplex' + exe_suffix
# TEST CMDS
# soplex.cmd = [soplex.executable] + ['-m10000',fullpath+ 'test.mps']
# REF CMDS
soplex.cmd = [soplex.executable] + ['-m45000', fullpath+'pds-50.mps']
# soplex.cmd = [soplex.executable] + ['-m3500', fullpath+'ref.mps']
# soplex.output = out_dir + 'soplex.out'

# 453.povray
povray = Process(pid=453)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'453.povray'+dir_suffix
povray.executable = fullpath+'povray' + exe_suffix
# TEST CMDS
# povray.cmd = [povray.executable] + [fullpath+'SPEC-benchmark-test.ini']
# REF CMDS
povray.cmd = [povray.executable] + [fullpath+'SPEC-benchmark-ref.ini']
# povray.output = out_dir + 'povray.out'

# 454.calculix
calculix = Process(pid=454)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'454.calculix'+dir_suffix
calculix.executable = fullpath+'calculix' + exe_suffix
# TEST CMDS
# calculix.cmd = [calculix.executable] + ['-i', 'beampic']
# REF CMDS
calculix.cmd = [calculix.executable] + ['-i', 'hyperviscoplastic']
# calculix.output = out_dir + 'calculix.out'

# 456.hmmer
hmmer = Process(pid=456)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'456.hmmer'+dir_suffix
hmmer.executable = fullpath+'hmmer' + exe_suffix
# TEST CMDS
# hmmer.cmd = [hmmer.executable] + ['--fixed', '0', '--mean', '325', '--num', '45000', '--sd', '200', '--seed', '0', fullapth+'bombesin.hmm']
# REF CMDS
hmmer.cmd = [hmmer.executable] + [fullpath+'nph3.hmm', fullpath+'swiss41']
# hmmer.cmd = [hmmer.executable] + ['--fixed', '0', '--mean', '500', '--num', '500000', '--sd', '350', '--seed', '0', fullpath+'retro.hmm']
# hmmer.output = out_dir + 'hmmer.out'

# 458.sjeng
sjeng = Process(pid=458)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'458.sjeng'+dir_suffix
sjeng.executable = fullpath+'sjeng' + exe_suffix
# TEST CMDS
# sjeng.cmd = [sjeng.executable] + [fullpath+'test.txt']
# REF CMDS
sjeng.cmd = [sjeng.executable] + [fullpath+'ref.txt']
# sjeng.output = out_dir + 'sjeng.out'

# 459.GemsFDTD
GemsFDTD = Process(pid=459)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'459.GemsFDTD'+dir_suffix
GemsFDTD.executable = fullpath+'GemsFDTD' + exe_suffix
# TEST CMDS
# GemsFDTD.cmd = [GemsFDTD.executable]
# REF CMDS
GemsFDTD.cmd = [GemsFDTD.executable]
# GemsFDTD.output = out_dir + 'GemsFDTD.out'

# 462.libquantum
libquantum = Process(pid=462)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'462.libquantum'+dir_suffix
libquantum.executable = fullpath+'libquantum' + exe_suffix
# TEST CMDS
# libquantum.cmd = [libquantum.executable] + ['33','5']
# REF CMDS [UPDATE 10/2/2015]: Sparsh Mittal has pointed out the correct input for libquantum should be 1397 and 8, not 1297 and 8. Thanks!
libquantum.cmd = [libquantum.executable] + ['1397', '8']
# libquantum.output = out_dir + 'libquantum.out'

# 464.h264ref
h264ref = Process(pid=464)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'464.h264ref'+dir_suffix
h264ref.executable = fullpath+'h264ref' + exe_suffix
# TEST CMDS
# h264ref.cmd = [h264ref.executable] + ['-d', fullpath+'foreman_test_encoder_baseline.cfg']
# REF CMDS
h264ref.cmd = [h264ref.executable] + ['-d', fullpath+'foreman_ref_encoder_baseline.cfg']
# h264ref.cmd = [h264ref.executable] + ['-d', fullpath+'foreman_ref_encoder_main.cfg']
# h264ref.cmd = [h264ref.executable] + ['-d', fullpath+'sss_encoder_main.cfg']
# h264ref.output = out_dir + 'h264ref.out'

# 465.tonto
tonto = Process(pid=465)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'465.tonto'+dir_suffix
tonto.executable = fullpath+'tonto' + exe_suffix
# TEST CMDS
# tonto.cmd = [tonto.executable]
# REF CMDS
tonto.cmd = [tonto.executable]
# tonto.output = out_dir + 'tonto.out'

# 470.lbm
lbm = Process(pid=470)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'470.lbm'+dir_suffix
lbm.executable = fullpath+'lbm' + exe_suffix
# TEST CMDS
# lbm.cmd = [lbm.executable] + ['20', fullpath+'reference.dat', '0', '1', fullpath+'100_100_130_cf_a.of']
# REF CMDS
lbm.cmd = [lbm.executable] + ['300', fullpath+'reference.dat', '0', '0', fullpath+'100_100_130_ldc.of']
# lbm.output = out_dir + 'lbm.out'

# 471.omnetpp
omnetpp = Process(pid=471)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'471.omnetpp'+dir_suffix
omnetpp.executable = fullpath+'omnetpp' + exe_suffix
# TEST CMDS
# omnetpp.cmd = [omnetpp.executable] + [fullpath+'omnetpp.ini']
# REF CMDS
omnetpp.cmd = [omnetpp.executable] + [fullpath+'omnetpp.ini']
# omnetpp.output = out_dir + 'omnetpp.out'

# 473.astar
astar = Process(pid=473)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'473.astar'+dir_suffix
astar.executable = fullpath+'astar' + exe_suffix
# TEST CMDS
# astar.cmd = [astar.executable] + [fullpath+'lake.cfg']
# REF CMDS
astar.cmd = [astar.executable] + [fullpath+'rivers.cfg']
# astar.output = out_dir + 'astar.out'

# 481.wrf
wrf = Process(pid=481)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'481.wrf'+dir_suffix
wrf.executable = fullpath+'wrf' + exe_suffix
# TEST CMDS
# wrf.cmd = [wrf.executable]
# REF CMDS
wrf.cmd = [wrf.executable]
# wrf.output = out_dir + 'wrf.out'

# 482.sphinx3
sphinx3 = Process(pid=482)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'482.sphinx3'+dir_suffix
sphinx3.executable = fullpath+'sphinx_livepretend' + exe_suffix
# TEST CMDS
# sphinx3.cmd = [sphinx3.executable] + [fullpath+'ctlfile', fullpath, fullpath+'args.an4']
# REF CMDS
sphinx3.cmd = [sphinx3.executable] + [fullpath+'ctlfile', fullpath, fullpath+'args.an4']
# sphinx3.output = out_dir + 'sphinx3.out'

# 483.xalancbmk
######## NOT WORKING ###########
xalancbmk = Process(pid=483)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'483.xalancbmk'+dir_suffix
xalancbmk.executable = fullpath+'Xalan' + exe_suffix
# TEST CMDS
######## NOT WORKING ###########
xalancbmk.cmd = [xalancbmk.executable] + ['-v',fullpath+'test.xml',fullpath+'xalanc.xsl']
# REF CMDS
######## NOT WORKING ###########
# xalancbmk.output = out_dir + 'xalancbmk.out'

# 998.specrand
specrand_i = Process(pid=998)  # Update June 7, 2017: This used to be LiveProcess()
fullpath=SPEC_DIR+'998.specrand'+dir_suffix
specrand_i.executable = fullpath+'specrand' + exe_suffix
# TEST CMDS
# specrand_i.cmd = [specrand_i.executable] + ['324342', '24239']
# REF CMDS
specrand_i.cmd = [specrand_i.executable] + ['1255432124', '234923']
# specrand_i.output = out_dir + 'specrand_i.out'
# 999.specrand
specrand_f = Process(pid=999)  # Update June 7, 2017: This used to be LiveProces using std::in;
fullpath=SPEC_DIR+'999.specrand'+dir_suffix
specrand_f.executable = fullpath+'specrand' + exe_suffix
# TEST CMDS
# specrand_f.cmd = [specrand_f.executable] + ['324342', '24239']
# REF CMDS
specrand_f.cmd = [specrand_f.executable] + ['1255432124', '234923']
# specrand_f.output = out_dir + 'specrand_f.out'
```

修改之后，可以通过

```bash
cd $PATH_TO_YOUR_GEM5_INSTALL  
build/ARCH/gem5.opt [gem5 options] config/example/spe06_config.py  [script options] --benchmark=YOUR_BENCH
```

指定对应的bench来运行。为了使运行更简单。分别为运行写了单线程和多线程版本。

#### **单线程运行**

``` bash
#!/bin/bash
#
# run_spec2006.sh
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.


############ DIRECTORY VARIABLES: MODIFY ACCORDINGLY #############
GEM5_DIR=/home/tom/gem5
#<PATH_TO_YOUR_GEM5_INSTALL>                          # Install location of gem5
SPEC_DIR=/home/tom/cpu2006
#<PATH_TO_YOUR_SPEC_CPU2006_INSTALL>                  # Install location of your SPEC2006 benchmarks

ARCH=X86
#YOUR gem5 arch
##################################################################

ARGC=$# # Get number of arguments excluding arg0 (the script itself). Check for help message condition.
if [[ "$ARGC" != 2 ]]; then # Bad number of arguments.
    echo "run_gem5_alpha_spec06_benchmark.sh  Copyright (C) 2014 Mark Gottscho"
    echo "This program comes with ABSOLUTELY NO WARRANTY; for details see <http://www.gnu.org/licenses/>."
    echo "This is free software, and you are welcome to redistribute it under certain conditions; see <http://www.gnu.org/licenses/> for details."
    echo ""
    echo "This script runs a single gem5 simulation of a single SPEC CPU2006 benchmark for Alpha ISA."
    echo ""
    echo "USAGE: run_gem5_alpha_spec06_benchmark.sh <BENCHMARK> <OUTPUT_DIR>"
    echo "EXAMPLE: ./run_gem5_alpha_spec06_benchmark.sh bzip2 /FULL/PATH/TO/output_dir"
    echo ""
    echo "A single --help help or -h argument will bring this message back."
    exit
fi

# Get command line input. We will need to check these.
BENCHMARK=$1                    # Benchmark name, e.g. bzip2
OUTPUT_DIR=$2                   # Directory to place run output. Make sure this exists!
RUN_SUFFIX=run/run_base_ref_amd64-m64-gcc43-nn.0000
######################### BENCHMARK CODENAMES ####################
PERLBENCH_CODE=400.perlbench
BZIP2_CODE=401.bzip2
GCC_CODE=403.gcc
BWAVES_CODE=410.bwaves
GAMESS_CODE=416.gamess
MCF_CODE=429.mcf
MILC_CODE=433.milc
ZEUSMP_CODE=434.zeusmp
GROMACS_CODE=435.gromacs
CACTUSADM_CODE=436.cactusADM
LESLIE3D_CODE=437.leslie3d
NAMD_CODE=444.namd
GOBMK_CODE=445.gobmk
DEALII_CODE=447.dealII
SOPLEX_CODE=450.soplex
POVRAY_CODE=453.povray
CALCULIX_CODE=454.calculix
HMMER_CODE=456.hmmer
SJENG_CODE=458.sjeng
GEMSFDTD_CODE=459.GemsFDTD
LIBQUANTUM_CODE=462.libquantum
H264REF_CODE=464.h264ref
TONTO_CODE=465.tonto
LBM_CODE=470.lbm
OMNETPP_CODE=471.omnetpp
ASTAR_CODE=473.astar
WRF_CODE=481.wrf
SPHINX3_CODE=482.sphinx3
XALANCBMK_CODE=483.xalancbmk
SPECRAND_INT_CODE=998.specrand
SPECRAND_FLOAT_CODE=999.specrand
##################################################################

# Check BENCHMARK input
#################### BENCHMARK CODE MAPPING ######################
BENCHMARK_CODE="none"
if [[ "$BENCHMARK" == "perlbench" ]]; then
    BENCHMARK_CODE=$PERLBENCH_CODE
fi
if [[ "$BENCHMARK" == "bzip2" ]]; then
    BENCHMARK_CODE=$BZIP2_CODE
fi
if [[ "$BENCHMARK" == "gcc" ]]; then
    BENCHMARK_CODE=$GCC_CODE
fi
if [[ "$BENCHMARK" == "bwaves" ]]; then
    BENCHMARK_CODE=$BWAVES_CODE
fi
if [[ "$BENCHMARK" == "gamess" ]]; then
    BENCHMARK_CODE=$GAMESS_CODE
fi
if [[ "$BENCHMARK" == "mcf" ]]; then
    BENCHMARK_CODE=$MCF_CODE
fi
if [[ "$BENCHMARK" == "milc" ]]; then
    BENCHMARK_CODE=$MILC_CODE
fi
if [[ "$BENCHMARK" == "zeusmp" ]]; then
    BENCHMARK_CODE=$ZEUSMP_CODE
fi
if [[ "$BENCHMARK" == "gromacs" ]]; then
    BENCHMARK_CODE=$GROMACS_CODE
fi
if [[ "$BENCHMARK" == "cactusADM" ]]; then
    BENCHMARK_CODE=$CACTUSADM_CODE
fi
if [[ "$BENCHMARK" == "leslie3d" ]]; then
    BENCHMARK_CODE=$LESLIE3D_CODE
fi
if [[ "$BENCHMARK" == "namd" ]]; then
    BENCHMARK_CODE=$NAMD_CODE
fi
if [[ "$BENCHMARK" == "gobmk" ]]; then
    BENCHMARK_CODE=$GOBMK_CODE
fi
if [[ "$BENCHMARK" == "dealII" ]]; then # DOES NOT WORK
    BENCHMARK_CODE=$DEALII_CODE
fi
if [[ "$BENCHMARK" == "soplex" ]]; then
    BENCHMARK_CODE=$SOPLEX_CODE
fi
if [[ "$BENCHMARK" == "povray" ]]; then
    BENCHMARK_CODE=$POVRAY_CODE
fi
if [[ "$BENCHMARK" == "calculix" ]]; then
    BENCHMARK_CODE=$CALCULIX_CODE
fi
if [[ "$BENCHMARK" == "hmmer" ]]; then
    BENCHMARK_CODE=$HMMER_CODE
fi
if [[ "$BENCHMARK" == "sjeng" ]]; then
    BENCHMARK_CODE=$SJENG_CODE
fi
if [[ "$BENCHMARK" == "GemsFDTD" ]]; then
    BENCHMARK_CODE=$GEMSFDTD_CODE
fi
if [[ "$BENCHMARK" == "libquantum" ]]; then
    BENCHMARK_CODE=$LIBQUANTUM_CODE
fi
if [[ "$BENCHMARK" == "h264ref" ]]; then
    BENCHMARK_CODE=$H264REF_CODE
fi
if [[ "$BENCHMARK" == "tonto" ]]; then
    BENCHMARK_CODE=$TONTO_CODE
fi
if [[ "$BENCHMARK" == "lbm" ]]; then
    BENCHMARK_CODE=$LBM_CODE
fi
if [[ "$BENCHMARK" == "omnetpp" ]]; then
    BENCHMARK_CODE=$OMNETPP_CODE
fi
if [[ "$BENCHMARK" == "astar" ]]; then
    BENCHMARK_CODE=$ASTAR_CODE
fi
if [[ "$BENCHMARK" == "wrf" ]]; then
    BENCHMARK_CODE=$WRF_CODE
fi
if [[ "$BENCHMARK" == "sphinx3" ]]; then
    BENCHMARK_CODE=$SPHINX3_CODE
fi
if [[ "$BENCHMARK" == "xalancbmk" ]]; then # DOES NOT WORK
    BENCHMARK_CODE=$XALANCBMK_CODE
fi
if [[ "$BENCHMARK" == "specrand_i" ]]; then
    BENCHMARK_CODE=$SPECRAND_INT_CODE
fi
if [[ "$BENCHMARK" == "specrand_f" ]]; then
    BENCHMARK_CODE=$SPECRAND_FLOAT_CODE
fi
# Sanity check
if [[ "$BENCHMARK_CODE" == "none" ]]; then
    echo "Input benchmark selection $BENCHMARK did not match any known SPEC CPU2006 benchmarks! Exiting."
    exit 1
fi

##################################################################
# Check OUTPUT_DIR existence
if [[ ! -d "$OUTPUT_DIR" ]]; then
    echo "Output directory $OUTPUT_DIR does not exist! Exiting."
    exit 1
fi

RUN_DIR=$SPEC_DIR/benchspec/CPU2006/$BENCHMARK_CODE/$RUN_SUFFIX     # Run directory for the selected SPEC benchmark
SCRIPT_OUT=$OUTPUT_DIR/runscript.log                                                                    # File log for this script's stdout henceforth

################## REPORT SCRIPT CONFIGURATION ###################
echo "Command line:"                                | tee $SCRIPT_OUT
echo "$0 $*"                                        | tee -a $SCRIPT_OUT
echo "================= Hardcoded directories ==================" | tee -a $SCRIPT_OUT
echo "GEM5_DIR:                                     $GEM5_DIR" | tee -a $SCRIPT_OUT
echo "SPEC_DIR:                                     $SPEC_DIR" | tee -a $SCRIPT_OUT
echo "==================== Script inputs =======================" | tee -a $SCRIPT_OUT
echo "BENCHMARK:                                    $BENCHMARK" | tee -a $SCRIPT_OUT
echo "OUTPUT_DIR:                                   $OUTPUT_DIR" | tee -a $SCRIPT_OUT
echo "==========================================================" | tee -a $SCRIPT_OUT
##################################################################

#################### LAUNCH GEM5 SIMULATION ######################
echo "" | tee -a $SCRIPT_OUT
echo "" | tee -a $SCRIPT_OUT
echo "--------- Here goes nothing! Starting gem5! ------------" | tee -a $SCRIPT_OUT
echo "" | tee -a $SCRIPT_OUT
echo "" | tee -a $SCRIPT_OUT

# Actually launch gem5!
##根据需要对此进行修改

$GEM5_DIR/build/$ARCH/gem5.opt \
--debug-flags=MemoryAccess \
--outdir=$OUTPUT_DIR \
--debug-file=${BENCHMARK}.out \
$GEM5_DIR/configs/tutorial/spec06_config.py \
--benchmark=$BENCHMARK \
--benchmark_stdout=$OUTPUT_DIR/$BENCHMARK.out \
--benchmark_stderr=$OUTPUT_DIR/$BENCHMARK.err \
--num-cpus=1 \
--cpu-type=TimingSimpleCPU \
--cpu-clock=2.4GHz \
--caches \
--cacheline_size=64 \
--l1d_size=64kB --l1i_size=32kB --l1i_assoc=8 --l1d_assoc=8 \
--l2cache --l2_size=2MB --l2_assoc=8 \
--l3_size=32MB --l3_assoc=4 \
--ruby \
--mem-type=DDR4_2400_8x8 \
--mem-channels=2 \
--mem-ranks=8 \
--mem-size=4096MB -I 2000000 \
| tee -a $SCRIPT_OUT
```

可以使用如下命令单线程运行gem5仿真

```bash
cd $PATH_TO_YOUR_GEM5_INSTALL  
./run_spec2006.sh  <BENCHMARK_NAME>  <FULL_PATH_TO_OUT_DIRECTORY>
```

#### **多线程运行**

对上面的run_spec2006.sh进行修改最后的部分，得到多线程版本的脚本。

```bash
# Actually launch gem5!
$GEM5_DIR/build/$ARCH/gem5.opt \
--debug-flags=MemoryAccess \
--outdir=$OUTPUT_DIR \
--debug-file=${BENCHMARK}.out \
$GEM5_DIR/configs/tutorial/spec06_config.py \
--benchmark=$BENCHMARK \
--benchmark_stdout=$OUTPUT_DIR/$BENCHMARK.out \
--benchmark_stderr=$OUTPUT_DIR/$BENCHMARK.err \
--num-cpus=4 \
--cpu-type=DerivO3CPU \
--cpu-clock=2.4GHz \
--caches \
--cacheline_size=64 \
--l1d_size=64kB --l1i_size=32kB --l1i_assoc=8 --l1d_assoc=8 \
--l2cache --l2_size=2MB --l2_assoc=8 \
--l3_size=32MB --l3_assoc=4 \
--ruby \
--mem-type=DDR4_2400_8x8 \
--mem-channels=2 \
--mem-ranks=8 \
--mem-size=4096MB -I 2000000 \
| tee -a $SCRIPT_OUT
```

可以使用如下命令单线程运行gem5仿真

```bash
cd $PATH_TO_YOUR_GEM5_INSTALL  
./run_spec2006.sh  <BENCHMARK_NAME1;BENCHMARK_NAME2;BENCHMARK_NAME3;BENCHMARK_NAME4>  <FULL_PATH_TO_OUT_DIRECTORY>
```

bench间使用";"分隔。

参考：

<http://www.voidcn.com/article/p-ejrcfcvw-rb.html>
<http://www.voidcn.com/article/p-nyjntyku-qo.html>
<http://www.voidcn.com/article/p-waqmaqjn-rb.html>
<http://www.voidcn.com/article/p-kmdrrpzd-qo.html>
<http://www.voidcn.com/article/p-akuksawb-qo.html>
<http://www.voidcn.com/article/p-ejrcfcvw-rb.html>
<https://markgottscho.wordpress.com/2014/09/20/tutorial-easily-running-spec-cpu2006-benchmarks-in-the-gem5-simulator/>

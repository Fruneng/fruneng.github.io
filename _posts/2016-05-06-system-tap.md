---
layout: post
title: "systemtap centOS/linux 简易教程"
description: ""
category: 
tags: [systemtap]
---



## centOS 7

```
yum --enablerepo=*-debuginfo --showduplicates list kernel kernel-devel kernel-debuginfo

```

看一下内核的版本 安装对应的kernel-debuginfo


```
yum --enablerepo=*-debuginfo install kernel-debuginfo-3.10.0-229.1.2.el7 kernel-devel-3.10.0-229.1.2.el7 systemtap
```


测试一下

```
stap -ve 'probe kernel.function("_do_fork") { print("hello world\n") exit() }'
```


## linux

自己编译内核就不需要装debuginfo这种包了

`make menuconfig` 搜索DEBUG_INFO

```
Symbol: DEBUG_INFO [=y]                                         
 Type  : boolean                                                
 Prompt: Compile the kernel with debug info                     
   Location:                                                    
     -> Kernel hacking                                          
 (1)   -> Compile-time checks and compiler options              
   Defined at lib/Kconfig.debug:120                             
   Depends on: DEBUG_KERNEL [=y] && !COMPILE_TEST [=n]          
                                                                
                                                                
 Symbol: DEBUG_INFO_DWARF4 [=y]                                 
 Type  : boolean                                                
 Prompt: Generate dwarf4 debuginfo                              
   Location:                                                    
     -> Kernel hacking                                          
       -> Compile-time checks and compiler options              
 (2)     -> Compile the kernel with debug info (DEBUG_INFO [=y])
   Defined at lib/Kconfig.debug:161                             
   Depends on: DEBUG_INFO [=y]                                  
                                                                
                                                                
 Symbol: DEBUG_INFO_REDUCED [=y]                                
 Type  : boolean                                                
 Prompt: Reduce debugging information                           
   Location:                                                    
     -> Kernel hacking                                          
       -> Compile-time checks and compiler options              
 (3)     -> Compile the kernel with debug info (DEBUG_INFO [=y])
   Defined at lib/Kconfig.debug:133                             
   Depends on: DEBUG_INFO [=y]                                  
 Symbol: DEBUG_INFO_SPLIT [=y]                                  
 Type  : boolean                                                
 Prompt: Produce split debuginfo in .dwo files                  
   Location:                                                    
     -> Kernel hacking                                          
       -> Compile-time checks and compiler options              
 (4)     -> Compile the kernel with debug info (DEBUG_INFO [=y])
   Defined at lib/Kconfig.debug:146                             
   Depends on: DEBUG_INFO [=y]                                  
```


```
[*] Compile the kernel with debug info                          
    [*]   Reduce debugging information                          
    [*]   Produce split debuginfo in .dwo files                 
    [*]   Generate dwarf4 debuginfo                             
    [*]   Provide GDB scripts for kernel debugging              
```

安装systemtap



## 运行测试

```
stap -ve 'probe kernel.function("do_fork") { print("hello world\n") exit() }'
```



## nginx性能分析

付上春哥的 [nginx-systemtap-toolkit](https://github.com/openresty/nginx-systemtap-toolkit)

[Linux下谁在切换我们的进程](http://blog.yufeng.info/archives/747)

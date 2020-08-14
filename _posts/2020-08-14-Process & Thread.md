---
layout:     post                    # 使用的布局（不需要改）
title:     Processes and Threads    # 标题 
subtitle:     #副标题
date:       2020-08-14             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 
    
---
## Processes and Threads

> An application consists of one or more processes. A process, in the simplest terms, is an executing program. One or more threads run in the context of the process. A thread is the basic unit to which the operating system allocates processor time. A thread can execute any part of the process code, including parts currently being executed by another thread.  

> A job object allows groups of processes to be managed as a unit. Job objects are namable, securable, sharable objects that control attributes of the processes associated with them. Operations performed on the job object affect all processes associated with the job object.  

> A thread pool is a collection of worker threads that efficiently execute asynchronous callbacks on behalf of the application. The thread pool is primarily used to reduce the number of application threads and provide management of the worker threads.  

> A fiber is a unit of execution that must be manually scheduled by the application. Fibers run in the context of the threads that schedule them.  

> User-mode scheduling (UMS) is a lightweight mechanism that applications can use to schedule their own threads. UMS threads differ from fibers in that each UMS thread has its own thread context instead of sharing the thread context of a single thread.  


每个Process都拥有：  

+ A Virtual address space
+ Executable code
+ Open handles to system objects
+ A Security context
+ A unique process identifier
+ Envirnoment variables
+ A proirity class
+ Minimum - Maximum workding set size
+ At least one thread of execution

Threads作为Process内的entity,每个Thread都拥有：  

+ Share virtual space address and system resources
+ Maintain exception handlers
+ Maintain a scheduling proirity
+ Thread Local storage
+ A unique thread identifier
+ A set of structure for saving the thread context until it is scheduler
+ Security context for impersonating clients
Thread Context包含：  

+ Thread's set of machine register
+ Kernel stack
+ A thread environment block
+ A user stack 


## Multitasking

There are two ways to implement multitasking:  
+ As a single process with multiple threads 
+ As multiple processes, each with one or more threads. 

It is typically more efficient for an application to implement multitasking by creating a single, multithreaded process, rather than creating multiple processes, for the following reasons:  

+ The system can perform a context switch more quickly for threads than processes, because a process has more overhead than a thread does (the process context is larger than the thread context).  
+ All threads of a process share the same address space and can access the process's global variables, which can simplify communication between threads.  
+ All threads of a process can share open handles to resources, such as files and pipes.  

## Scheduling

The system scheduler controls multitasking by determining which of the competing threads receives the next processor time slice. The scheduler determines which thread runs next using `scheduling priorities`.  

### Scheduling Priorities

Each thread is assigned a scheduling priority. The priority levels range from zero (lowest priority) to 31 (highest priority).  

The priority of each thread is determined by the following criteria:  

+ The priority class of its process
+ The priority level of the thread within the priority class of its process  

#### Priority Class

Each process belongs to one of the following priority classes:  

+ IDLE_PRIORITY_CLASS
+ BELOW_NORMAL_PRIORITY_CLASS
+ NORMAL_PRIORITY_CLASS
+ ABOVE_NORMAL_PRIORITY_CLASS
+ HIGH_PRIORITY_CLASS
+ REALTIME_PRIORITY_CLASS  

#### Priority Level

The following are priority levels **within** each priority class:  

+ THREAD_PRIORITY_IDLE
+ THREAD_PRIORITY_LOWEST
+ THREAD_PRIORITY_BELOW_NORMAL
+ THREAD_PRIORITY_NORMAL
+ THREAD_PRIORITY_ABOVE_NORMAL
+ THREAD_PRIORITY_HIGHEST
+ THREAD_PRIORITY_TIME_CRITICAL

#### Base Priority

The process priority class and thread priority level are combined to form the base priority of each thread.  

[1-31 Base Priority](https://docs.microsoft.com/en-us/windows/win32/procthread/scheduling-priorities#base-priority)  


## Context Switches

The scheduler maintains a queue of executable threads for each priority level. These are known as *ready* threads.  

When a processor becomes available, the system performs a context switch. The steps in a context switch are:  

1. Save the context of the thread that just finished executing.
2. Place the thread that just finished executing at the end of the queue for its priority.
3. Find the highest priority queue that contains ready threads.
4. Remove the thread at the head of the queue, load its context, and execute it.

The most common reasons for a context switch are:  

+ The time slice has elapsed.
+ A thread with a higher priority has become ready to run.
+ A running thread needs to wait.

## Priority Boosts

Each thread has a *dynamic* priority. This is the priority the scheduler uses to determine which thread to execute.   
Initially, a thread's dynamic priority is the **same as** its base priority.   

*The system does not boost the priority of threads with a base priority level between 16 and 31. Only threads with a base priority between 0 and 15 receive dynamic priority boosts.*  

## Thread Stack Size

Each new thread or fiber receives its **own stack space** consisting of both reserved and initially committed memory.   

A stack is freed when its thread exits. It is not freed if the thread is terminated by another thread.  
## Synchronization Objects

[Synchronization Objects](https://docs.microsoft.com/en-us/windows/win32/sync/synchronization-objects)


## Reference

1. [About Processes and Threads](https://docs.microsoft.com/en-us/windows/win32/procthread/about-processes-and-threads)


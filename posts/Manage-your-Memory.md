---
title: Manage your MeMory
description: Understand how garbage collecting works is critical to know how to manage your memory.
date: 2020-12-23
tags:
  - draft
  - gc
  - memory
  - management
  - java
layout: layouts/post.njk
image: https://images.unsplash.com/photo-1533279443086-d1c19a186416?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=1489&q=80
---

## why Garbage collector

create and forgot no need to remember to delete

use and forgot no need to ask should I delete

use with confidence objects will not vanish or become corrupt behind your back

## The GC Promise

claim no-live objects

## Forms of GC

### **Do nothing**

memory doesn't get free but you still guarantee that it's not collecting live objects

### **Reference Counting**

In COM applications make calls to 2 functions `addRef` and `release`

`addRef` increments a count on an object

`release` decrements the count

when that count goes to 0 the object is no longer being referenced  

and the object can clean itself and remove from the memory

`circular reference`

```java
class A {
private B b;

public void setB(B b) {
    this.b = b;
}
}

class B {
private A a;

public void setA(A a) {
    this.a = a;
}
}

public class Main {
public static void main(String[] args) {
    A one = new A();
    B two = new B();

    // Make the objects refer to each other (creates a circular reference)
    one.setB(two);
    two.setA(one);

    // Throw away the references from the main method; the two objects are
    // still referring to each other
    one = null;
    two = null;
}
}
```

this GC is smart enough to remove them from memory

How it's done (not sure if I got it right)

it gives each object a count reference

when it's assigned the count is incremented by 1

when it's designed the count is decremented by 1

when the count is 0 the object is removed

if you tried to make it yourself you will face issues like maybe you'll have someone decrement the count while the object is still in use or forgot to increment/decrement the count at all

### **Mark and Sweep**

when GC runs it runs in 2 phases

first it walks through all the memory and mark the memory that's still being alive

and then in sweep phase it remove all unused memory

this could leave us with memory that could be fragmented

- **Mark phase**

    to identify the objects that are still alive

    it marks the objects starting from the Root set and marks the objects referenced and then check if these objects are referencing other objects then mark these objects and so on

    and that makes the circular objects are not a problem at all

- **Sweep phase**

    to remove dead objects

    just remove all the objects that are not marked

- **Compact Phase**

    to compact the memory

    compacting the memory may lead to changing the physical addresses of the memory

    specifically in java you don't have access to physical address

### Copying

this could work hand in hand with mark and sweep

solves the fragmented memory problem in Mark and sweep

after the sweep phase

all the memory that's left is copied from one buffer to another

then rearrange it so it's no longer fragmented

uses different spaces to manage memory

it has 2 spaces in the memory from space and to space

the form space contains all the objects allocated in the memory

when from space is full the GC runs

it follows the Mark phase

but during the Sweep phase the marked objects are being moved to "to" space and so compacted at the same time

and then the from space is cleared

then to and from spaces swap rules

to space will act like from space

from space will act like to space

### Incremental

Doesn't look all the memory all the time during garbage collect

### Generational

*form of incremental GC*

if an object survived a GC then it likely means it will be around for long time 

then the GC will not look it for a while and this is good for performance

long living objects are promoted to a different generation

sweep younger generations more often than it sweeps through older generations

the number of generations depends on the environment

in java 2 generations

.Net 3 generations

the objects that survive GC are moved to older generation

new objects are allocated in younger generation

 

## How Garbage Collectors work in Java

things to consider

- Stop the world events

    GC pauses the application when it's running

    But with multi-cores the GC can run concurrently with the app

- Memory Fragmentation

    does it defragment memory all at once

    does it leave it to another stage

    does it leave it fragmented

- Throughput

    how quick can it run?

## Basic concepts in JVM

*die young or live forever*

- Memory space has a young and old generation

In young generation:

- most recent objects are allocated in what's called Eden space
- It has 2 survivor spaces objects that survive GC get moved to one of the survivor spaces
- only one survivor space is used at a time (like From and To spaces)

when an object survive a certain number of GC it's likely to live forever so it's moved to old generation also known as Tenured

There's also permanent generation this one contains java packages and it's never garbage collected

- Minor Garbage Collection

    when GC collect objects in young generation

    happens when young generation is full

    every object is assigned a number starting from 0 and when it moves from Eden to survivor or from survivor to another the number is incremented to keep track of how many times this object survived GC

- Major Garbage Collection Full GC

    when old generation/tenured is full

    slower

    collects both young and old generations

    promotes objects from young generation to old generation if they passed a certain number of GC

    objects get promoted if survivor is full too

### Memory Allocation

- Using pointers to indicate the last allocated space and move it every time you add to the memory —> very cheap —> fine for single thread environment
- For multi thread Java uses Thread Local Allocation Buffers TLAB

**TLAB**

- Each thread gets it's own buffer (space) in the Eden space
- Each thread has it's own memory and pointer
- No locking needed

Tools you can use to manage your memory

Interact with the garbage collector
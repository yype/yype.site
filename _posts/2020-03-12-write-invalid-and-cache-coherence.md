---
layout: post
title: Write Invalid and Cache Coherence
description: 
summary: 
tags: [distributed computing, cache coherence, write-invalid, class notes]
visible: 1
---

// This post was written in Chinese

## Intro

今天分布式听飘了，个人感觉老师放的课件对问题的描述以及解答有些欠缺，后来做了些资料检索，对下面这个简单问题做了些总结：

对于 write-update、write-invalidate 两种 coherence protocol，哪一种在生产消费者模型下的性能更差？

---

首先得知道 coherence protocol 的目的是什么，它的目的很简单：保证多处理器系统下的数据一致性。

有两类这种协议，一种是 snoopy-based 另一种是 directory-based（当然还有一个 shared caches 概念这里也不讨论了），对于前者来说，所有读写、更新操作都以类似广播的方式发给所有处理器，每个处理器相应地 snoop and respond，write-update 和 write-invalid 都是属于这一类，这两种方式的定义分别如下：

- Write-update: When a write operation is observed to a location that a cache has a copy of, the cache controller updates its own copy of the snooped memory location with the new data.
- Write-invalidate: When a write operation is observed to a location that a cache has a copy of, the cache controller invalidates its own copy of the snooped memory location, which forces a read from main memory of the new value on its next access.

Write-invalidate 在一些情况下的优势是很明显的，例如突发大量对单个单元写请求的情况下，它比 write-update 有更好的效果，其每次对内存单元的更新不会引起其他 cache 的更新，因为这个时候其他单元不一定总有 read 请求，write 之后只会对这个内存对应的 cache（其 copy）做一个 invalid 标记，并且在做标记之前如果发现已经是 invalid 状态就不用更新了，因此在没有 read 的情况下只需要发送一次 invalidation 广播，下次其他单元需要访问相应内存时检测到自己 cache 上的 invalid 标记就直接去主存里取数据并更新自己的 cache。

此外还有一种情况，例如 writes to different words of a block，write-update 在每一个 word 写操作都会发送一次更新请求，而 write-invalidate 由于同理也只会发送一次 invalidation 广播。

但是对于 producer-consumer 模型而言，producer 的每次更新都要等待一个 consumer 来消费，这就是与之前两种情况不同的一点。这种情况下如果每次还是采用广播 invalidate 消息的方法，下一次某个 consumer 消费的时候检测到自己 cache invalid 状态后便还需要去访问主存，这是完全多余的了，因为我们完全可以在生成好之后直接更新 consumer 的 cache，这样只需要一次写会主存的操作而不是两次。因此这时 write-update 优于 write-invalidate。

总结一下，对于大量连续的写请求来说，write-invalidate 更好，对于 producer-consumer 这种供需平衡的模型来说 write-update 更优。

---

关于 cache coherency 这方面还有许多复杂并且值得探讨的问题，但由于它们太过学术我没有去深究。

**References**

- https://www3.nd.edu/~mniemier/teaching/2010_B_Fall/lectures/lec_27_slides.pdf.
- https://www.cs.utah.edu/~retrac/papers/hpca07.pdf. 

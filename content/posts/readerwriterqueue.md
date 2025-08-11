+++
date = '2025-08-11T20:18:21+08:00'
draft = true
title = 'Readerwriterqueue 源代码阅读'
tag = ['cpp', '研究']
+++

ReaderWriterQueue 是一个高性能的C++无锁队列实现，专为单生产者-单消费者（SPSC）场景设计。

- 无锁设计：完全无锁实现，enqueue和dequeue操作都是O(1)时间复杂度
- 高性能：在x86架构上，内存屏障编译为无操作指令，性能极佳
- C++11兼容：支持移动语义，减少不必要的拷贝
- 泛型模板：使用模版支持任意类型的元素，类似std::queue
- 内存高效：预分配连续内存块，提供try_enqueue保证不分配内存
- 阻塞版本：提供BlockingReaderWriterQueue支持wait_dequeue操作

适用于需要在两个线程间高效传递数据的场景，如生产者-消费者模式、异步任务处理等。仅需包含头文件即可使用，无需额外依赖

## 跨平台

## 测试

- 单元测试
- 稳定性测试

## 性能

- benchmarks
-

## 参考

- [Readerwriterqueue](https://github.com/cameron314/readerwriterqueue)

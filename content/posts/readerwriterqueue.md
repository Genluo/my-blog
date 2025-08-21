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

## 类图

```mermaid

#endif

```

## 跨平台

## 测试

- 单元测试
- 稳定性测试

### CRTP 设计模式

> 通过 CRTP，基类可以调用派生类的方法，实现了静态多态，在这个项目中是这样使用
>
#### 核心思想

1. "派生类告诉基类自己是谁" - 通过模板参数传递自身类型
2. 静态多态 - 编译时确定调用关系，无运行时开销
3. 类型安全 - 编译时检查，避免类型错误

#### 优势

- 零运行时开销，相比虚函数性能强大很多（不使用虚函数表）
- 编译时类型检查（虚函数运行时多态，无法完全被优化）
- 代码复用（可以使用基类功能，派生类只需要实现特定的功能）

#### 示例

- 基础示例

```cpp
// CRTP 方法
template <typename Derived>
class CRTPBase {
public:
  int calculate() { return static_cast<Derived *>(this)->calculate_impl(); }
};

class CRTPDerived : public CRTPBase<CRTPDerived> {
public:
  int calculate_impl() { return 42; }
};

```

- 测试用例

```cpp
#define REGISTER_TEST(testName) registerTest(#testName, &subclass_t::testName)

template <typename TSubclass> class TestClass {
public:
  typedef TSubclass subclass_t;
  void registerTest(const char *name, bool (subclass_t::*method)());
};

class Test : public TestClass<Test> {
public:
  Test() {
    REGISTER_TEST(test1);
    REGISTER_TEST(test2);
  }
  bool test1();
  bool test2();
};
```

## 性能

- benchmarks
- SIMD 优化

## 问题

### 1. false sharing 问题

伪共享是指两个或多个CPU核心频繁访问同一缓存行中的不同数据，导致缓存行在不同核心之间不断传递，造成性能下降的现象。

```txt
缓存行（64字节）：[变量A][变量B][其他数据...]
                 ↑      ↑
              核心1访问  核心2访问
```

虽然核心1只访问变量A，核心2只访问变量B，但因为它们在同一缓存行中：

核心1修改变量A → 整个缓存行失效
核心2访问变量B → 需要重新加载缓存行
核心2修改变量B → 核心1的缓存行失效
核心1再次访问变量A → 又需要重新加载

#### 避免伪共享

使用 cachelineFiller0 将 front 和 tail 分隔到不同缓存行，生产者线程主要访问的 tail 和消费者线程主要访问的 front 不会相互干扰

```cpp
struct Block {
    weak_atomic<size_t> front;    // 消费者主要访问
    size_t localTail;             // 消费者拥有

    // 缓存行填充 - 确保下面的变量在不同缓存行
    char cachelineFiller0[MOODYCAMEL_CACHE_LINE_SIZE - sizeof(weak_atomic<size_t>) - sizeof(size_t)];

    weak_atomic<size_t> tail;     // 生产者主要访问
    size_t localFront;            // 生产者拥有

    // 再次填充
    char cachelineFiller1[MOODYCAMEL_CACHE_LINE_SIZE - sizeof(weak_atomic<size_t>) - sizeof(size_t)];

    weak_atomic<Block*> next;
    // ...
};
```

### 2. 编译器重排序问题

- 指令重排序
- 内粗访问重新排序
- 循环优化重排序

#### 问题示例

- 数据竞争：data 和 ready 没有同步保护，同时被多个线程访问
- 内存重排序：编译器/CPU可能重排序指令，ready = true 可能在 data = 42之前执行
- 可见性问题：一个线程的写操作可能不会立即对另一个线程可见

> 编译器可能将producer中的指令重排序为：
// ready = true;   // 被提前！
// data = 42;// 被延后！
// 这会导致消费者读取到错误的data值

```cpp
#include <thread>
#include <iostream>

int data = 0;
bool ready = false;

// 线程1：生产者
void producer() {
    data = 42;// Step 1
    ready = true;   // Step 2
}

// 线程2：消费者
void consumer() {
    while (!ready) {
        // 等待
    }
    std::cout << "Data: " << data << std::endl;  // 期望输出42
}
```

#### 解决编译器重排序问题

- 使用 volatile

```cpp
volatile int flag = 0;
int data = 0;

void producer() {
    data = 42;
    flag = 1;  // volatile 防止重排序
}

void consumer() {
    while (flag ==0) {  // volatile 防止优化
        // 等待
    }
    printf("Data: %d\n", data);
}
```

- 使用 内存屏障

```cpp
#include <atomic>

int data = 0;
bool ready = false;

void producer() {
    data = 42;
    std::atomic_signal_fence(std::memory_order_release);  // 编译器屏障
    ready = true;
}

void consumer() {
    while (!ready) {std::atomic_signal_fence(std::memory_order_acquire);  // 编译器屏障
    }
    printf("Data: %d\n", data);
}
```

- 使用原子操作

```cpp
#include <atomic>

int data = 0;
std::atomic<bool> ready{false};

void producer() {
    data = 42;
    ready.store(true, std::memory_order_release);  // 防止重排序
}

void consumer() {
    while (!ready.load(std::memory_order_acquire)) {
        // 等待
    }
    printf("Data: %d\n", data);  // 保证读取到正确值
}
```

#### 编译器重排序&CPU重排序

| 特性 | 编译器重排序 | CPU重排序 |
|------|-------------|----------|
| 发生时机 | 编译时 | 运行时 |
| 影响范围 | 所有平台 | 特定CPU架构 |
| 防护方法 | 编译器屏障 |内存屏障指令 |
| 性能影响 | 编译时优化 | 运行时开销 |

#### 实践

- 单线程通常不用关心，但是多线程需要关系这个问题

### 3. 信号量

- 在 Apple iOS and OSX 平台上选用 Mach 信号量的原因：
  - <http://lists.apple.com/archives/darwin-kernel/2009/Apr/msg00010.html>

### 4. 内存对齐

内存对齐是指数据在内存中的存储位置必须是某个特定数值的倍数。例如，一个4字节的整数可能需要存储在地址为4的倍数的位置上。

- 对齐的数据可以一次读取，不对其可能需要多次
- 对齐的数据更容易命中缓存

### 5. 多线程数据竞争和同步问题

| 特性/机制      | 互斥锁 (Mutex)             | 读写锁 (Shared Mutex)      | 原子操作 (Atomic Operations)     |
| -------------- | -------------------------- | -------------------------- | -------------------------------- |
| **并发性能**   | 较差，锁竞争时性能下降     | 较好，读多写少时效果显著   | 非常好，几乎没有开销             |
| **写操作性能** | 好                         | 差，写操作时锁竞争严重     | 非常好，原子操作通常不需要锁     |
| **读操作性能** | 较差，阻塞等待锁           | 好，允许多个读线程并行     | 非常好，读操作不需要锁           |
| **复杂度**     | 低，易于理解和使用         | 中，管理读写锁和线程竞争   | 高，只有简单的操作可以用原子操作 |
| **适用场景**   | 一般适用于少数临界区的保护 | 适合读多写少的场景         | 适合对单个变量的高效操作         |
| **开销**       | 高，特别是在锁竞争严重时   | 中等，读写锁竞争时会有开销 | 低，原子操作无需阻塞             |


## 参考

- [Readerwriterqueue](https://github.com/cameron314/readerwriterqueue)

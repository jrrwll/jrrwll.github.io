
---
title: "服务限流"
date: 2023-11-25
---

## 简介

限流顾名思义，就是对请求或并发数进行限制；通过对一个时间窗口内的请求量进行限制来保障系统的正常运行。如果我们的服务资源有限、处理能力有限，就需要对调用我们服务的上游请求进行限制，以防止自身服务由于资源耗尽而停止服务。

在限流中有两个概念需要了解：
- **阈值**：在一个单位时间内允许的请求量。如 QPS 限制为 10，说明 1 秒内最多接受 10 次请求。
- **拒绝策略**：超过阈值的请求的拒绝策略，常见的拒绝策略有直接拒绝、排队等待等。

## 算法

### 令牌桶算法

#### Guava RateLimiter - 令牌桶算法实现

提供了两个限流策略：**平滑突发限流 SmoothBursty** 和 **平滑预热限流 SmoothWarmingUp**

**平滑突发限流 SmoothBursty**

```java
// 设置每秒放置的令牌数为100个
RateLimiter r = RateLimiter.create(100);
// 尝试获取 5 个令牌，获取不到则阻塞直至有新的令牌为止
r.acquire(5);
```

**平滑预热限流 SmoothWarmingUp**

```java
// 创建一个平均分发令牌速率为10，预热期为五秒钟的限流器
RateLimiter r = RateLimiter.create(10, 5, TimeUnit.SECONDS);
r.acquire();
```

**实现原理**

```java
// 当前存储令牌数
double storedPermits;
// 最大存储令牌数
double maxPermits;
// 添加令牌时间间隔
double stableIntervalMicros;
/**
 * 下一次请求可以获取令牌的起始时间
 * 由于RateLimiter允许预消费，上次请求预消费令牌后
 * 下次请求需要等待相应的时间到nextFreeTicketMicros时刻才可以获取令牌
 */
private long nextFreeTicketMicros = 0L;
```

### 分布式限流


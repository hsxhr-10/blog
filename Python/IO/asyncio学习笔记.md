# asyncio 学习笔记

asyncio 一般被叫做异步框架，但是根据标准的 IO 模型，很多时候说的异步框架并不是真正的异步，而是 IO 多路复用。asyncio 也不例外，
是基于 [selector](https://github.com/python/cpython/blob/3.9/Lib/asyncio/selector_events.py) 模块做的 IO 多路复用框架，
而 selector 基于 select，select 基于操作系统提供的 IO 多路复用机制，譬如 Linux 的 epoll，macOS 的 kqueue 等

> 下面说的异步、异步框架实际指的是 IO 多路复用

asyncio 的特点和主流的异步框架（tornado、gevent）差不多，主要有以下几点：

- 适用于 IO 密集型任务，相比线程昂贵（创建、销毁、上下文切换），asyncio 里的协程要轻量级很多，
可以存在大量的协程，因此可以较好地提升应用的吞吐量，但是应用的响应时间还是要看单个协程的处理时长，跟异步与否无关。
- 事件循环不能被阻塞，也就是不能存在阻塞代码，无论是自己写的、标准库的、还是第三方库的代码。这就要求生态要好，不然连个异步的数据库驱动都没有，也挺麻烦 😂
- 理论上不适用于 CPU 密集型任务，可以结合进程池、Celery 等工具解决这个问题

> asyncio 需要 Python3.5+，最好是 Python3.7+，功能会多一些，少量新功能需要 Python3.9。另外，asyncio 的接口存在不向后兼容的情况，
> 譬如 "Deprecated since version 3.8, will be removed in version 3.10: The loop parameter." 这类

## 协程

### 协程的定义

```python
import asyncio


async def sleep(second):
    await asyncio.sleep(second)
    print("hello asyncio")


async def main():
    await sleep(1)


if __name__ == "__main__":
    asyncio.run(main())
```

> 只考虑 async/await 的写法，不考虑旧的 @asyncio.coroutine 写法

## 可等待对象

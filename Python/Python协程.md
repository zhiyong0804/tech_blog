# Python协程

#### 协程执行方法
* await 进入被调用的协程函数，执行完毕返回后再继续，而这也是 await 的字面意思。
* asyncio.create_task() 来创建任务
* asyncio.run 来触发运行

#### 执行
```python
import asyncio
import time

async def crawl_page(url):
    print('crawling {}'.format(url))
    sleep_time = int(url.split('_')[-1])
    await  asyncio.sleep(sleep_time)
    print('OK {}'.format(url))

async def main(urls):
    tasks = [asyncio.create_task(crawl_page(url)) for url in urls]
    for task in tasks:
        await task
async def main(urls):
    tasks = [asyncio.create_task(crawl_page(url)) for url in urls]
    await asyncio.gather(*tasks)

start = time.time()
asyncio.run(main(['url_1', 'url_2', 'url_3', 'url_4']))
end = time.time()
print(end-start)
```

#### 取消协程
```python
import asyncio
import time

async def worker_1():
    await asyncio.sleep(1)
    return 1

async def worker_2():
    await asyncio.sleep(2)
    return 2 / 0

async def worker_3():
    await asyncio.sleep(3)
    return 3

async def main():
    task_1 = asyncio.create_task(worker_1())
    task_2 = asyncio.create_task(worker_2())
    task_3 = asyncio.create_task(worker_3())

    await asyncio.sleep(2)
    task_3.cancel()

    res = await asyncio.gather(task_1, task_2, task_3, return_exceptions=True)
    print(res)

start = time.time()
asyncio.run(main())
end = time.time()
print(end-start)
```

#### 生产者和消费者模型
```python
import asyncio
import time
import random

async def consumer(queue, id):
    while True:
        val = await queue.get()
        print('{} get a val {}'.format(id, val))
        await asyncio.sleep(1)

async def producer(queue, id):
    for i in range(5):
        val = random.randint(1, 10)
        await queue.put(val)
        print('{} put a val: {}'.format(id, val))
        await asyncio.sleep(1)

async def main():
    queue = asyncio.Queue()
    con1 = asyncio.create_task(consumer(queue, 'con1'))
    con2 = asyncio.create_task(consumer(queue, 'con2'))
    pro1 = asyncio.create_task(producer(queue, 'pro1'))
    pro2 = asyncio.create_task(producer(queue, 'pro2'))

    await  asyncio.sleep(10)
    con1.cancel()
    con2.cancel()

    await asyncio.gather(con1, con2, pro1, pro2, return_exceptions=True)

start = time.time()
asyncio.run(main())
end = time.time()
print(end-start)
```
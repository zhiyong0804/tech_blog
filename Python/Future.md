# Future

#### 并发执行
```python
import requests
import time
import threading
import concurrent.futures

def download_one(url):
    resp = requests.get(url)
    print('Read {} from {}'.format(len(resp.content), url))
//串行执行
def download_all(sites):
    for site in sites:
        download_one(site)
//多线程版本
def download_all_t(sites):
     with concurrent.futures.ThreadPoolExecutor(max_workers=5) as excutor:
            excutor.map(download_one, sites)
//多进程版本
def download_all_p(sites):
   with concurrent.futures.ProcessPoolExecutor() as excutor:
        excutor.map(download_one, sites)
//futures
def download_all_f(sites):
    with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
        to_do = []
        for site in sites:
            future = executor.submit(download_one, site)
            to_do.append(future)
        for future in concurrent.futures.as_completed(to_do):
            future.result()

def main():
    sites = [
        'https://www.baidu.com',
        'https://www.taobao.com',
        'https://www.jd.com',
        'https://www.tencent.com/zh-cn/index.html',
    ]
    start_time = time.perf_counter()
    download_all_f(sites)
    end_time = time.perf_counter()
    print('Download {} sites in {} seconds'.format(len(sites), end_time - start_time))


if __name__ == '__main__':
    main()
```

#### 为什么多线程每次只能有一个线程执行
* Python 的解释器并不是线程安全的，为了解决由此带来的 race condition 等问题，Python 便引入了全局解释器锁，也就是同一时刻，只允许一个线程执行。当然，在执行 I/O 操作时，如果一个线程被 block 了，全局解释器锁便会被释放，从而让另一个线程能够继续执行。
* 
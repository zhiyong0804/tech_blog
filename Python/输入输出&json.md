# 输入输出&json

#### 输入输出
```python
import re

def parse(text):
    text = re.sub(r'[^\w]', ' ', text)

    text = text.lower()

    word_list = text.split(' ')

    word_list = filter(None, word_list)

    word_cnt = {}

    for word in word_list:
        if word not in word_cnt:
            word_cnt[word] = 0
        word_cnt[word] += 1

    sorted_word_cnt = sorted(word_cnt.items(), key=lambda kv : kv[1], reverse=True)

    return sorted_word_cnt

def main():
  with open('./demo.txt', 'r') as fin:
    text = fin.read()

  word_and_freq = parse(text)

  print(word_and_freq)

  with open('./out.txt', 'w') as fout:
      for word, freq in word_and_freq:
          fout.write('{}{}\n'.format(word, freq))


if __name__ == '__main__':
    main()
```

#### json
* json.dumps() 这个函数，接受 Python 的基本数据类型，然后将其序列化为 string
* json.loads() 这个函数，接受一个合法字符串，然后将其反序列化为 Python 的基本数据类型。

```python
import json

params = {
    'symbol' : '123456',
    'type': 'limit',
    'price': 123.4,
    'amount': 23
}

json_str = json.dumps(params)

print(json_str)

print('json_str {} params {}'.format(type(json_str), params))

origin_params = json.loads(json_str)

print('type origin={}, origin={}'.format(type(origin_params), origin_params))
```

#### for&while
* range() 函数是直接由 C 语言写的，调用它速度非常快。而 while 循环中的“i += 1”这个操作，得通过 Python 的解释器间接调用底层的 C 语言；并且这个简单的操作，又涉及到了对象的创建和删除（因为 i 是整型，是 immutable，i += 1 相当于 i = new int(i + 1)）。所以，显然，for 循环的效率更胜一筹

```python

i = 0
while i < 1000000:
    i += 1

for i in range(0, 1000000):
    pass
```

#### 条件与循环的复用
```python
expression for item in iterable if condition

[(xx, yy) for xx in x for yy in y if xx != yy]
```

#### demo
```python
attributes = ['name', 'dob', 'gender']
values = [['jason', '2000-01-01', 'male'],
['mike', '1999-01-01', 'male'],
['nancy', '2001-02-01', 'female']
]

res = [dict(zip(attributes, value)) for value in values]
print(res)
```
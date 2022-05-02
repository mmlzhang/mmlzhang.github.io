---
title: 15-python内置函数库
keywords:
- 15-python内置函数库
- mlzhang
description : "15-python内置函数库"
---
## 1. functool

#### 1.1 lru_cache

- 当api传入相同参数时，快速返回缓存的值

```python

from urllib import request, error

from functools import lru_cache


@lru_cache(maxsize=32)
def get_pep(num):
    resource = 'http://www.python.org/dev/peps/pep-%04d/' % num
    try:
        with request.urlopen(resource) as s:
            return s.read()
    except error.HTTPError:
        return "NOT found {}".format(num)


for n in 8, 290, 308, 320, 8, 218, 320, 279, 289, 320, 9991:
    pep = get_pep(n)
    print(f"{n}  {len(pep)}  {pep}")
```

#### 1.2 reduce

- 累加

```python
reduce(lambda x, y: x + y, [1, 2, 3])
6
```





## 2. itertools



```python
from itertools import count, cycle, repeat

c = count(10)
[next(c) for _ in range(3)]
[10, 11, 12]

cy = cycle("abc")
[next(cy) for _ in range(10)]
['b', 'c', 'a', 'b', 'c', 'a', 'b', 'c', 'a', 'b']

c = repeat("abc", 3)
[next(c) for _ in range(1)]
['abc']
[next(c) for _ in range(2)]
['abc', 'abc']c = repeat("abc", 3)
[next(c) for _ in range(1)]
['abc']
[next(c) for _ in range(2)]
['abc', 'abc']
```

#### 2.1 chain

- 链接iter

```python
list(chain("ABC", '123'))
['A', 'B', 'C', '1', '2', '3']
```

#### [`groupby()`](https://docs.python.org/zh-cn/3/library/itertools.html#itertools.groupby)

- 注意 * 使用前需要先将值按照顺序进行排序

```python
{os: [item.version for item in items] for os, items in
                        groupby(os_version_list, key=lambda x: x.os)}
```



## filter

过滤，满足条件的返回True此时改值会被返回出来，不满足条件的值被忽略
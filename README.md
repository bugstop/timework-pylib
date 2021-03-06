# timework

[![PyPI](https://img.shields.io/pypi/v/timework)](https://pypi.org/project/timework/)
[![Build Status](https://travis-ci.org/bugstop/timework-timeout-decorator.svg?branch=master)](https://travis-ci.org/bugstop/timework-timeout-decorator)
[![Coverage Status](https://coveralls.io/repos/github/bugstop/timework-timeout-decorator/badge.svg?branch=master)](https://coveralls.io/github/bugstop/timework-timeout-decorator?branch=master)
[![CodeFactor](https://www.codefactor.io/repository/github/bugstop/timework-timeout-decorator/badge)](https://www.codefactor.io/repository/github/bugstop/timework-timeout-decorator)
[![PyPI - Python Version](https://img.shields.io/pypi/pyversions/timework)](https://www.python.org)
[![platform](https://img.shields.io/badge/platform-windows%20%7C%20macos%20%7C%20linux-red)](https://github.com/bugstop/timework-timeout-decorator)

Cross-platform python module to set execution time limits <sup>`timer`, `timeout`, `iterative`</sup> as decorators.

## Install

```bash
pip install timework
```

## Usage

```python
import timework as tw
```

### timework.timer

***A decorator measuring the execution time.***

**`timework.TimeError`** contains three parts:

- `TimeError.message` ***string,*** \<*decorated function name*\>: \<*time used*\> seconds used
- `TimeError.result` return values of the function being decorated
- `TimeError.detail` ***float,*** time used

***Notice:*** In **`@timer`** decorator, `timeout` is used to raise a `Error` **after** the decorated function finishes, but fails to finish within `timeout` seconds. If you want to **terminate** the function with a `timeout` limit, please use **`@limit`**.

```python
import logging

@tw.timer(logging.warning)
def timer_demo_a():
    i = 0
    while i < 2 ** 23:
        i += 1
    return i

@tw.timer(print, detail=True)
def timer_demo_b():
    i = 0
    while i < 2 ** 24:
        i += 1
    return i

@tw.timer(timeout=1)
def timer_demo_c():
    i = 0
    while i < 2 ** 25:
        i += 1
    return i
```
```python
a = timer_demo_a()
b = timer_demo_b()

try:
    c = timer_demo_c()
except tw.TimeError as e:
    print('error:', e.message)
    c = e.result

print(a, b, c)
```
```bash
WARNING:root:timer_demo_a: 0.496672 seconds used
START:  Tue Feb 18 15:06:45 2020
FINISH: Tue Feb 18 15:06:46 2020
timer_demo_b: 0.989352 seconds used
error: timer_demo_c: 1.9817 seconds used
8388608 16777216 33554432
```

### timework.limit

***A decorator limiting the execution time.***

**`timework.TimeError`** only contains:

- `TimeError.message` ***string,*** <*decorated function name*\>: \<*timeout*\> seconds exceeded
- `TimeError.result` ***None,*** *unused*
- `TimeError.detail` ***None,*** *unused*

```python
@tw.limit(3)
def limit_demo(m):
    i = 0
    while i < 2 ** m:
        i += 1
    return i
```
```python
try:
    s = limit_demo(4)
except tw.TimeError as e:
    print(e)
else:
    print('result:', s)

try:
    s = limit_demo(30)
except tw.TimeError as e:
    print(e)
else:
    print('result:', s)
```
```bash
result: 16
limit_demo: 3 seconds exceeded
```

### timework.iterative

***A decorator used to process iterative deepening.***

**`timework.TimeError`** contains three parts:

- `TimeError.message` ***string,*** \<*decorated function name*\>.iterative_deepening: \<*timeout*\> seconds exceeded
- `TimeError.result` return values at current level of the iterative deepening search
- `TimeError.detail` ***collections.deque,*** results of the previous levels

***Notice:*** Please use keyword arguments when calling the function that is being decorated. Make sure the **max-depth** variable of the inner function is given at `key`<sup>(`key='max_depth'` by default)</sup> and whose value should be the maximum search-depth<sup>*(integer)*</sup>.

```python
@tw.iterative(3)
def iterative_demo_a(max_depth):
    i = 0
    while i < 2 ** max_depth:
        i += 1
    return max_depth, i

@tw.iterative(3, history=5, key='depth')
def iterative_demo_b(depth):
    i = 0
    while i < 2 ** depth:
        i += 1
    return depth
```
```python
try:
    s = iterative_demo_a(max_depth=10)
except tw.TimeError as e:
    print(e.message)
    print(e.result, e.detail)
else:
    print('result:', s)

try:
    s = iterative_demo_a(max_depth=25)
except tw.TimeError as e:
    print(e.message)
    print(e.result, e.detail)
else:
    print('result:', s)

try:
    s = iterative_demo_b(depth=25)
except tw.TimeError as e:
    print(e.message)
    print(e.result, e.detail)
else:
    print('result:', s)
```
```bash
result: (10, 1024)
iterative_demo_a.iterative_deepening: 3 seconds exceeded
(20, 1048576) deque([(20, 1048576)], maxlen=1)
iterative_demo_b.iterative_deepening: 3 seconds exceeded
20 deque([16, 17, 18, 19, 20], maxlen=5)
```

*You can read docstrings for more details.*

## License

MIT License &copy; <a href="https://github.com/bugstop" style="color: black !important;text-decoration: none !important;">bugstop</a>

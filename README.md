# timework

A package used to set time limits.

## Install

```
pip install timework
```

## Usage

```python
import timework as tw


@tw.timer(out=print)
def timer_demo():
    i = 0
    while True:
        i += 1


@tw.limit(timeout=1.5)
def limit_demo():
    i = 0
    while True:
        i += 1


@tw.progressive(timeout=2)
def progressive_demo(i, max_depth):
    while i < max_depth:
        i += 1
    return max_depth + i
   
   
timer_demo()

try:
	limit_demo()
except Exception as e:
	print(e)

try:
	progressive_demo(max_depth=10)
except Exception as e:
	rc = str(e)
	print(rc)
```

## License

MIT © bugstop 
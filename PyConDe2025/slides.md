[comment]: # (Set the theme:)
[comment]: # (THEME = league)
[comment]: # (CODE_THEME = base16/zenburn)


# Supercharge your testing with inline-snapshot
# Supercharge Your Testing with inline-snapshot

[comment]: # (!!!)

## About Me

Frank Hoffmann

* working since 4 years on open-source projects
* AST related things (pysource-minimize/-codegen, ...)
* @15r10nk on Github, Fosstodon and X
* or 15r10nk@polarbit.de

[comment]: # (!!!)

### What is snapshot testing?

normal testing
``` python
assert 1 + 1 == 2
```

general snapshot testing
``` python
match_snapshot(1 + 1)  # 2 is written to file
```

[comment]: # (!!!)

### What is inline-snapshot testing?

``` python
from inline_snapshot import snapshot

assert 1 + 1 == snapshot()
```

...
``` bash
> pytest
```

...
``` python
from inline_snapshot import snapshot

assert 1 + 1 == snapshot(2)
```

[comment]: # (!!!)

### Benefits

* no indirection
* code is easy to read/review
* no file names
* can be formatted like normal python code
* works well for strings or larger data structures
* simple semantics
    ``` python
    def snapshot(x):
        return x
    ```
  (used by *-\-inline-snapshot=disable* or in CI)

[comment]: # (!!!)

### Rules

* snapshot object creation and comparison are separate
* you can put snapshot() everywhere
* you can compare it multiple times

``` python
expected = snapshot()


def test_example():
    assert 1 + 1 == expected
    assert 5 - 3 == expected
    check_all([2 + 0, -2 * -1], expected)


def check_all(values, expected_snapshot):
    for value in values:
        assert value == expected_snapshot
```

[comment]: # (!!!)

### Rules

* you can use  snapshot() in @parametrize

``` python
@pytest.mark.parametrize(
    "a,b,result",
    [
        (1, 1, snapshot()),
        (2, 2, snapshot()),
        (4, 4, snapshot()),
    ],
)
def test_add(a, b, result):
    assert a + b == result
```


[comment]: # (!!!)

### Rules

* you can compare almost everything with an snapshot
  * list,dict,set,tuple,enums...
  * dataclass, pydantic models, attrs

``` python
from dataclasses import dataclass


@dataclass
class Point:
    x: int
    y: int


def test_dataclass():
    assert Point(1, 2) == snapshot()
```

[comment]: # (!!!)

### Rules

* you can also put special values inside snapshots
  * dirty-equals expressions
  * snapshots
  * custom code

[comment]: # (!!!)

#### dirty-equals

``` python
from uuid import uuid4

def data():
    return {"user":"Bod","id":uuid4()}

def test_data():
    assert data()==snapshot()
```




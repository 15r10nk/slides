[comment]: # (Set the theme:)
[comment]: # (THEME = league)
[comment]: # (CODE_THEME = androidstudio)


## Supercharge your testing with inline-snapshot

[comment]: # (!!!)

## About Me

Frank Hoffmann

* working since 4 years on open-source projects
* AST related tooling (pysource-minimize/-codegen, ...)
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

<!-- inline-snapshot: first_block -->
``` python
from inline_snapshot import snapshot


def test_add():
    assert 1 + 1 == snapshot()
```
``` bash
> pytest
```
<!-- inline-snapshot: create -->
``` python hl_lines="3"
from inline_snapshot import snapshot


def test_add():
    assert 1 + 1 == snapshot(2)
```


[comment]: # (!!!)

### What is inline-snapshot testing?

``` python
from inline_snapshot import snapshot

assert 1 + 5 == snapshot(2)
```
![approve](assets/approve.png)


``` python hl_lines="3"
from inline_snapshot import snapshot

assert 1 + 5 == snapshot(6)
```


[comment]: # (!!!)

### Benefits

* works well for strings or larger data structures
* no indirection
* no file names
* code is easy to read/review
* can be formatted like normal python code
* simple semantics
    ``` python
    def snapshot(x):
        return x
    ```
  (used by *-\-inline-snapshot=disable* or in CI)

[comment]: # (!!!)

### Rules

* separate snapshot creation and comparison
* you can put snapshot() everywhere
* you can compare it multiple times

<!-- inline-snapshot: create first_block outcome-passed=1 -->
``` python
from inline_snapshot import snapshot

expected = snapshot(2)


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

<!-- inline-snapshot: create first_block outcome-passed=3 -->
``` python
from inline_snapshot import snapshot
import pytest


@pytest.mark.parametrize(
    "a,b,result",
    [
        (1, 1, snapshot(2)),
        (2, 2, snapshot(4)),
        (4, 4, snapshot(8)),
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

<!-- inline-snapshot: create first_block outcome-passed=1 outcome-errors=1 -->
``` python
from inline_snapshot import snapshot
from dataclasses import dataclass


@dataclass
class Point:
    x: int
    y: int


def test_dataclass():
    assert Point(1, 2) == snapshot(Point(x=1, y=2))
```

[comment]: # (!!!)

### Rules

* you can also put special values inside snapshots
  * dirty-equals expressions
  * snapshots
  * custom code

[comment]: # (!!!)

#### dirty-equals


<!-- inline-snapshot: create first_block outcome-passed=1 outcome-errors=1 -->
``` python
from uuid import uuid4, UUID
from inline_snapshot import snapshot


def data():
    return {"user": "Bod", "id": uuid4()}


def test_data():
    assert data() == snapshot(
        {
            "user": "Bod",
            "id": UUID(
                "ea99d93c-7628-43d3-a031-175c38db664a"
            ),
        }
    )
```

[comment]: # (|||)

#### dirty-equals

<!-- inline-snapshot: outcome-errors=1 -->
``` python hl_lines="1 3 12"
from uuid import uuid4
from inline_snapshot import snapshot
from dirty_equals import IsUuid


def data():
    return {"user": "Bod", "id": uuid4()}


def test_data():
    assert data() == snapshot(
        {"user": "Bod", "id": IsUUID()}
    )
```

[comment]: # (|||)

#### dirty-equals

<!-- inline-snapshot: create first_block outcome-passed=1 -->
``` python
from uuid import uuid4
from inline_snapshot import snapshot
from dirty_equals import IsJson


def data():
    return {"user": "Bod", "data": '{"a":5,"b":10}'}


def test_data():
    assert data() == snapshot(
        {
            "user": "Bod",
            "data": IsJson({"a": 5, "b": 10}),
        }
    )
```

[comment]: # (|||)

#### dirty-equals

<!-- inline-snapshot: create first_block outcome-passed=1 -->
``` python
from uuid import uuid4
from inline_snapshot import snapshot
from dirty_equals import IsJson


def data():
    return {"user": "Bod", "data": '{"a":5,"b":10}'}


def test_data():
    assert data() == snapshot(
        {
            "user": "Bod",
            "data": IsJson(snapshot({"a": 5, "b": 10})),
        }
    )
```



[comment]: # (!!!)

## There is more

* operators <=, >=, in
* snapshots inside snapshots
* external()


[comment]: # (!!!)

> Perfection is achieved, not when there is nothing more to add, but when there is nothing left to take away.

<small>
-- Antoine de Saint-Exupéry, Airman's Odyssey
</small>

[comment]: # (!!!)

### Insider Features

* create and fix normal assertions

``` python
def test_create():
    assert 1 + 1 == ...


def test_fix():
    assert 1 + 5 == 2
```


[comment]: # (|||)

### Insider Features

* create and fix normal assertions

``` python
def test_create():
    assert 1 + 1 == 2


def test_fix():
    assert 1 + 5 == 6
```

[comment]: # (!!!)

### Thank you

to all my sponsors

<div style="display: flex;gap: 30px;flex-direction: row;justify-content: center;" >

![logfire](assets/sponsors/logfire.png)

![astral](assets/sponsors/astral.png)

![tiangolo](assets/sponsors/tiangolo.jpeg)

</div>
<div style="display: flex;gap: 30px;flex-direction: row;justify-content: center;" >

![pawamoy](assets/sponsors/pawamoy.jpeg)

![alexmojaki](assets/sponsors/alexmojaki.png)

![ddanier](assets/sponsors/ddanier.jpeg)

![nathanjmcdougall](assets/sponsors/nathanjmcdougall.jpeg)

</div>

and everyone who uses inline-snapshot.

---
layout: post
title: "context manager(with statement)에서 return 하면 무슨 일이 일어날까?"
tags: ["Python"]
comments: true
---

context manager(with statement)에서 return 하면 무슨 일이 일어날까?

---

## Question
``` python
with SomeClass(params) as module:
    return module.anything
```

위 처럼 Context manager(with statement) 내부에서 return을 하게 되면 무슨일이 생길까?  
흔히 쓰는 file `open`의 경우 `close`를 잘 할까? class 의 경우 `__exit__` method를 항상 호출할까?

## Answer
결론부터 말하자면, **알아서 잘 한다**.

``` python
module = SomeClass(params)

try:
  return module.anything
finally:
  # call module.__exit__
```

질문에서 보여준 코드는 위의 코드처럼 작동한다. 
즉 `close`나 `__exit__`의 경우 finally 구문에 들어있는 것처럼 실행되기 때문에 항상 실행되는 것이다.
이는 [PEP-343](https://www.python.org/dev/peps/pep-0343/#examples) 의 예제로도 나와있다.
나만 모르고 썻을 수도 있지만(!) 사실 PEP-343을 살펴보면 `with` 자체가 try/finally 구문의 standard 사용을 위한 statement 인것이다.

---
참고 : <https://stackoverflow.com/questions/9885217/in-python-if-i-return-inside-a-with-block-will-the-file-still-close>

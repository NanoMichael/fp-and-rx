---
marp: true
paginate: true
theme: default
class:
  - lead
  - invert
backgroundColor: #44475a
color: #f8f8f2
---

<style>

code {
  display: inline-block;
  font-family: "Monaco", monospace;
  font-size: 0.8em;
  letter-spacing: 0;
  margin: -0.1em 0.15em;
  padding: 0.1em 0.2em;
  vertical-align: baseline;
  color: yellow;
}

pre {
  display: block;
  border: 0px;
  border-radius: 5px;
  min-height: 1em;
  overflow: visible;
  background:linear-gradient(180deg, #282a36 0, #282a36);
}

pre code {
  box-sizing: border-box;
  margin: 0;
  min-width: 100%;
  font-size: 0.9em;
  color: #f8f8f2;
  line-height: 140%;
}

pre code svg[data-marp-fitting=svg] {
  max-height: calc(580px - 1em);
}
</style>

<!-- _class: lead invert -->
<style scoped>
h1 {
  font-size: 2.5em;
  color: #ffb86c;
  text-align: center;
}
h3 {
  text-align: center;
}
section {
  text-align: center;
}
</style> 

# FP and Rx

### 函数式编程和 Rx 简明介绍

https://github.com/nanomichael

---

## 一个例子

```Java
if (a != null) {
  b = getB(a);
  if (b != null) {
    c = getC(b);
    if (c != null) {
      d = getD(c);
      // ...
    }
  }
}
```

---

### Optional and `'?.'`

```Java
Optional
  .ofNullable(a)
  .flatMap(a -> Optional.ofNullable(getB(a)))
  .flatMap(b -> Optional.ofNullable(getC(b)))
  .flatMap(c -> Optional.ofNullable(getD(c)))
  // ...
  .ifPresent(x -> doSomething());
```

```Kotlin
a
  ?.let { getB(it) }
  ?.let { getC(it) }
  ?.let { getD(it) }
  // ...
  ?.run { doSomething() }
```

---

## 另一个例子

```Kotlin
val a = listOf(1, 2, 3)
val b = listOf(4, 5, 6)
val c = listOf(7, 8, 9)
// ...
val list = mutableListOf<List<Int>>()
for (ai in a) {
  for (bi in b) {
    for (ci in c) {
      // ...
      list += listOf(ai, bi, ci, ...)
    }
  }
}
```

---

### flatMap

```Kotlin
val list = a
  .flatMap { ai ->
    b.map { bi -> listOf(ai, bi) }
  }
  .flatMap { list ->
    c.map { ci -> list + ci }
  }
  // ...
```

将 `if` 和 `for` 后的代码块看作是回调函数，同步代码也会产生 `callback hell`。

---

## Function composition

文件 A 的内容为另一个文件 B 的地址，给定文件 A 的地址，读取文件 B 的内容：

- 读取文件 A 的内容
- 将文件 A 的内容作为参数，读取文件 B 的内容

```Kotlin
fun readFile(path: String): String {
  val content = // ... read file
  return content
}
val contentOfA = readFile(pathOfA)
val contentOfB = readFile(contentOfA)
```

---

### 另一种形式

```
(f . g) x = f g x
```

```
fun <T, R1, R2> ((R1) -> R2).compose(g: (T) -> R1) = { t ->
  this(g(t))
}

val contentOfB = (::readFile.compose(::readFile))(pathOfA)
```

![w:180 h:180](./what.jpg)

---

### Chain style

```Kotlin
fun <T, R1, R2> ((T) -> R1).then(f: (R1) -> R2) = { t ->
  this(f(t))
}

val f = readFile
  .then { content -> content.filter { isDigit(it) } }
  .then { digits -> digits.toInt() }

val num = f()
```

---

### Async

```Kotlin
fun readFile(path: String, onRead: (content: String) -> Unit) {
  val content = // ... read file
  onRead(content)
}

readFile(pathOfA) { contentOfA ->
  readFile(contentOfA) { contentOfB ->
    // ... do something
  }
}
```

---

## Function composition

> FP is all about function composition,
> nothing more.

我们希望的是：

```Kotlin
val readAFunction = ???
val readBFunction = ???
val composition = compose(readAFunction, readBFunction)
composition()
```

---

## Monad

---

## FP vs Rx

---

## Coroutine

---

## Use Rx

- Control driven and Data driven
- Proactive and Reactive
- Purity

---

## References

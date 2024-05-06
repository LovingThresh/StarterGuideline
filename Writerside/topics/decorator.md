# decorator

## 基本实现
装饰器是Python中一个非常有用的功能，它允许你在不修改原始函数或方法的代码的前提下，增加额外的功能。我们从一个简单的例子开始，逐步展示如何使用装饰器。

### 基本的装饰器
让我们先定义一个简单的装饰器，它在函数执行前后打印一些信息，这有助于理解函数的调用时间和执行状态。

```python
def simple_decorator(func):
    def wrapper():
        print("Something is happening before the function is called.")
        func()
        print("Something is happening after the function is called.")
    return wrapper

@simple_decorator
def say_hello():
    print("Hello!")

say_hello()
```

### 装饰器的工作原理：
1. **定义装饰器**：
	- `simple_decorator` 是一个装饰器工厂，它接受一个函数 `func` 作为参数。
	- 它定义了一个内部函数 `wrapper`，这个 `wrapper` 函数将在原始函数 `func` 周围添加额外的行为。

2. **应用装饰器**：
	- 使用 `@simple_decorator` 语法，我们将 `simple_decorator` 应用到 `say_hello` 函数上。
	- 这相当于执行了 `say_hello = simple_decorator(say_hello)`。

3. **调用被装饰的函数**：
	- 当你调用 `say_hello()` 时，实际上是在调用 `wrapper` 函数。
	- `wrapper` 函数首先执行一些操作（在这里是打印信息），然后调用原始的 `say_hello` 函数，最后再执行一些操作（再次打印信息）。

### 输出结果：
```
Something is happening before the function is called.
Hello!
Something is happening after the function is called.
```

### 带参数的装饰器
装饰器也可以设计成支持带参数的函数。我们来看一个例子，其中装饰器会打印函数的执行时间：

```python
import time

def timing_decorator(func):
    def wrapper(*args, **kwargs):
        start_time = time.time()
        result = func(*args, **kwargs)
        end_time = time.time()
        print(f"Function {func.__name__} took {end_time - start_time} 
        seconds to complete.")
        return result
    return wrapper

@timing_decorator
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")
```

在这个例子中：
- `timing_decorator` 装饰器计算并打印了 `greet` 函数的执行时间。
- `wrapper` 函数通过 `*args` 和 `**kwargs` 能够接受任意数量的位置参数和关键字参数，这使得装饰器更加通用。

通过这些简单的示例，你可以看到装饰器如何在不修改原有函数代码的情况下，为函数添加新的功能。这使得装饰器在Python中成为处理横切关注点（如日志、性能监控、事务处理等）的理想选择。

## 基本理解
装饰器可以称为函数的函数，我们在声明的时候需要注意它的层级结构，第一层进行声明，第二层进行函数修饰，需要额外注意的是一定要明确内层函数(包装器)的返回值

在使用装饰器时，通常涉及到两层函数：

1. **外层函数**：这是装饰器本身，负责接收被装饰的函数作为参数。这一层的主要职责是接收参数（如果装饰器需要参数则还包括装饰器的参数），并返回内层函数，也就是实际的包装器（wrapper）。
2. **内层函数（包装器）**：这个函数包裹（或“装饰”）了原始函数。当被装饰的函数被调用时，实际上是调用这个内层函数。内层函数在调用原始函数之前后可以执行一些附加的操作，例如打印日志、计算执行时间等。这个内层函数可以接收任意的参数和关键字参数，以便它可以适用于任何被装饰的函数。

### 详细说明：

- **第一层（装饰器声明层）**：
  这一层定义了装饰器的行为。如果装饰器本身需要参数（例如，你可能想要一个装饰器来打印不同级别的日志），则这一层还会处理这些参数。这层的主要职责是返回第二层的函数。

- **第二层（包装器层）**：
  这层实际包裹了原始函数。它通常会执行一些预处理，调用原始函数，然后执行一些后处理。这个层次的函数负责具体的“装饰”工作，即增加原始函数的附加功能。

这种结构不仅增加了代码的灵活性和可重用性，而且还保持了原始函数的清晰和独立性，因为所有附加的功能都是在装饰器中处理的。

### 示例分析：

```python
def simple_decorator(func):
    def wrapper(*args, **kwargs):
        print("Something is happening before the function is called.")
        result = func(*args, **kwargs)
        print("Something is happening after the function is called.")
        return result
    return wrapper

@simple_decorator
def say_hello(name):
    print(f"Hello, {name}!")

say_hello("Alice")
```

在这个例子中，`my_decorator` 是一个接受参数的装饰器。当你使用 `@my_decorator(10, 20)` 修饰 `add` 函数时，实际上是在创建一个能够传递给 `add` 函数的包装器，而这个包装器在执行 `add` 之前打印了装饰器的参数。这就是装饰器通过两层结构如何增加函数功能的一个典型例子。

## wrapper与func参数
当你定义一个装饰器时，确保 `wrapper` 函数接收的参数与被装饰函数（`func`）的参数保持一致是非常重要的。这样可以确保被装饰的函数能够正确接收到所有它需要的参数，而且可以透明地加入装饰逻辑而不会破坏原有的功能。

### 参数匹配的原则
如果你的 `func` 需要特定的参数，那么 `wrapper` 函数也需要能够接受这些相同的参数，不论是位置参数还是关键字参数。这是因为 `wrapper` 函数实际上在调用 `func` 时，需要将接收到的所有参数原样传递给 `func`。

### 参数的一致性
参数名（比如示例中的 `name`）不一定非得完全匹配，但通常为了代码的可读性和维护性，保持参数名一致是一个好的实践。如果参数名不同，你仍然需要确保参数在传递时能正确对应，这通常需要在 `wrapper` 中明确地指定参数名称。

### 示例解释
举一个例子，假设原始函数是这样的：

```python
def greet(person):
    print(f"Hello, {person}!")
```

如果你的装饰器中的 `wrapper` 函数使用了不同的参数名，如下所示：

```python
def simple_decorator(func):
    def wrapper(name):  # 这里的参数名为 `name` 而不是 `person`
        print("Before calling the function.")
        result = func(name)  # 即便这里使用了 `name`，仍然需要确保其正确传给 `person`
        print("After calling the function.")
        return result
    return wrapper
```

尽管在 `wrapper` 中使用了 `name` 而非 `person`，只要在调用 `func(name)` 时参数能正确对应，函数依然可以正常运行。但为了避免混淆，保持参数名的一致性通常是更好的选择。

### 总结
总的来说，`wrapper` 函数的参数应该能够覆盖 `func` 所有的参数，不管是通过相同的参数名，还是通过使用 `*args` 和 `**kwargs` 来通用地接收任何参数。这样，装饰器才能在不影响原函数功能的前提下，添加额外的功能或处理逻辑。
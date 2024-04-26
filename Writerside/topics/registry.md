# registry

## 简介
注册表（Registry）的思想在许多现代编程库中非常流行，特别是在机器学习和计算机视觉领域。这种模式主要用于在软件系统中灵活地注册和使用各种组件（如模型、函数或类）而不需要硬编码它们的具体实现。这使得软件更加模块化，扩展性更强，同时也便于维护和升级。

### 注册表模式的基本概念：

1. **定义注册表**：
   注册表本质上是一个中央存储，它维护了名称到对象的映射。这些对象通常是类、函数或配置。

2. **注册组件**：
   开发者可以通过某种机制（通常是装饰器或显式注册函数）将组件注册到注册表中。注册通常包括为组件指定一个唯一的名字。例如，在深度学习库中，你可以注册不同类型的神经网络模型或层。

3. **使用组件**：
   当系统运行并需要特定组件时，可以通过其注册名从注册表中检索并创建实例。这种方式使得用户或其他系统部分可以在不知道对象具体实现细节的情况下，动态地使用这些组件。

### 注册表的好处：

- **灵活性**：可以在不修改现有代码的情况下，通过添加新的组件扩展系统功能。
- **解耦**：系统的各个部分通过注册表连接，减少了直接依赖，提高了代码的可维护性。
- **易于管理**：所有组件都在一个地方注册和管理，方便查找和替换。

### 实际应用例子：

在机器学习库中，如 `timm` 和 `openmmlab`：
- **timm**：用于图像识别的库，通过注册表来管理大量不同的模型和组件。用户可以很容易地试验不同的模型，而无需改动底层代码。
- **openmmlab**：这是一个开源的计算机视觉算法库集合，使用注册表来处理不同的算法、模型和组件，使得添加新算法或者模型变得非常简单。

注册表模式是现代软件工程中一个非常有用的设计模式，特别是在需要高度模块化和可扩展性的领域，如机器学习和计算机视觉。

## 示例
### 示例：Python中的简单注册表实现

```python
class Registry(dict):
    def register(self, name, item):
        if name in self:
            raise KeyError(f"Item '{name}' is already registered.")
        self[name] = item

    def get(self, name, default=None):
        return self.get(name, default)

# 创建注册表实例
registry = Registry()

# 装饰器用于注册函数或类
def register(name):
    def decorator(item):
        registry.register(name, item)
        return item
    return decorator

@register('example_class')
class Example:
    def run(self):
        print("Example running")

# 使用注册表
instance = registry['example_class']()
instance.run()
```

这个简单的示例展示了如何设计和实现一个基本的注册表，包括注册和检索功能。这种方法可以根据实际应用的需求进行扩展和改进。
当你使用装饰器时，理解它的调用过程确实很重要，这可以帮助你更好地掌握Python中这一强大的功能。装饰器本质上是一个函数，它接受一个函数作为参数并返回一个新的函数。装饰器的目的是在不修改原始函数的情况下，增加函数的功能。

让我们详细分解上面提到的注册表装饰器的调用过程：

### 1. 定义装饰器
我们首先定义了一个名为 `register` 的装饰器。这个装饰器接受一个参数 `name`，这个参数指定了将来要注册到注册表中的名称。

```python
def register(name):
    def decorator(item):
        registry.register(name, item)
        return item
    return decorator
```

### 2. 装饰器内部结构
- `register` 函数定义了另一个内部函数 `decorator`，这个内部函数实际上承担了装饰器的作用。
- `decorator` 函数接受一个参数 `item`，这个 `item` 将是需要被装饰的函数或类( 。 )
- 在 `decorator` 函数内部，我们调用了 `registry.register(name, item)`，这个方法将 `item` 注册到全局注册表 `registry` 中，并用 `name` 作为键。
- 最后，`decorator` 函数返回原始的 `item`，这意味着被装饰的对象（无论是函数还是类）在经过注册后保持不变。

### 3. 使用装饰器
当我们使用装饰器 `@register('example_class')` 时，这里的 `'example_class'` 作为 `name` 参数传递给 `register` 函数。

```python
@register('example_class')
class Example:
    def run(self):
        print("Example running")
```

- 首先，`register('example_class')` 调用生成了一个 `decorator` 函数。
- 然后，`Example` 类作为参数传递给 `decorator` 函数。
- 在 `decorator` 函数内部，`Example` 类注册到 `registry` 并以 `'example_class'` 为键。
- 最终，原始的 `Example` 类返回并定义完成。

### 4. 调用被装饰的类
当你从注册表中检索并创建 `Example` 类的实例时，你得到的是原始的未修改的类：

```python
instance = registry['example_class']()
instance.run()  # 输出: Example running
```

这个过程演示了如何通过装饰器动态地注册类，同时不改变类的定义。装饰器提供了一个非常强大的方式来扩展和修改函数和类的行为，而无需直接修改其代码。

如果你对python装饰器概念仍然感到困惑的话，你可以移步到[装饰器初探](decorator.md)

学习完成之后，请查看engine的registry[源码](https://github.com/open-mmlab/mmengine/blob/main/mmengine/registry/registry.py)
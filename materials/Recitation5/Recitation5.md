---
title: SI100B_Spring_2026_Recitation_5
separator: <!--s-->
verticalSeparator: <!--v-->
theme: simple
highlightTheme: github
css: assets/custom.css
autoTitlePage: true
makeTitle:
  lecture: SI100B Spring 2026 Recitation 5
  title: 推导式、函数、测试、异常与字典
  detail: SI100B 2026 Staff | 2026-04-10
makeThanks: true
---

# 本次复习课内容

<!--v-->

## 说在前面

- 这次复习课覆盖 **Lecture 11** 与 **Lecture 12** 的核心内容。
  - 如何把代码写得**更简洁**？
  - 如何把函数写得**更灵活**？
  - 如何让程序在出错时**更可控**？
  - 如何用更合适的数据结构组织数据？

<!--v-->

## Topics

- List Comprehension
- Default Parameters
- Functions as Objects / Returning Functions
- Testing and Debugging
- Exceptions 与 Assertions
- Dictionaries

<!--s-->

# 一、Lecture 11 总览

<!--v-->

## Lecture 11 的主线

Lecture 11 表面上包含多个分散知识点，但本质上都在回答同一个问题：

**如何让程序既简洁，又具有可维护性？**

- 推导式解决的是：**同样的逻辑，能否更紧凑地表达？**
- 默认参数解决的是：**函数接口，能否同时兼顾常用情况与特殊需求？**
- 函数对象与闭包解决的是：**行为本身，能否像数据一样被配置与复用？**
- 测试与调试解决的是：**代码写出来之后，如何证明它可靠？**

<!--s-->

# 二、List Comprehension

<!--v-->

## 1. 推导式解决的是什么问题？

许多程序都在重复同一种结构：

- 遍历一个可迭代对象；
- 按某个规则筛选元素；
- 对保留下来的元素做变换；
- 生成一个新的列表。

例如：给定一组温度读数，只保留高于 30 度的读数，并把它们转换为华氏温度。

```python
temps = [21, 35, 28, 31, 33]
result = []
for t in temps:
    if t > 30:
        result.append(t * 9 / 5 + 32)
```

这种模式高度重复，因此 Python 提供了更紧凑的表达方式。

<!--v-->

## 2. 基本语法

推导式的标准形式为：

```python
[expression for elem in iterable if test]
```

其中：

- `iterable` 决定“遍历来源”；
- `if test` 决定“筛选条件”，这一部分可省略；
- `expression` 决定“放进新列表里的内容”。

因此，推导式不是随意压缩代码，而是把**遍历、筛选、变换**三件事压缩进一个统一结构中。

<!--v-->

## 3. 与普通 `for` 循环的对应关系

下面两段代码在逻辑上等价：

```python
prices = [12, 25, 8, 31]
new_prices = []
for p in prices:
    new_prices.append(p + 2)
```

```python
new_prices = [p + 2 for p in prices]
```

理解推导式时，最重要的一点是：

- 它**创建新列表**；
- 它不是原地修改已有列表；
- 它更适合表达“批量生成结果”，而不是复杂控制流。

<!--v-->

## 4. 如何正确阅读一条推导式？

很多同学一看到下面的表达式就从左往右机械读：

```python
[p + 2 for p in prices if p < 30]
```

更合理的阅读顺序是：

1. 先看 `for p in prices`：遍历谁；
2. 再看 `if p < 30`：筛掉谁；
3. 最后看 `p + 2`：保留后如何变换。

也就是说，**执行顺序更接近 `for → if → expression`，书写顺序则是 `expression → for → if`。**

<!--v-->

## 5. 例 1：筛选后再变换

```python
scores = [59, 88, 73, 41, 95]
passed = [s + 5 for s in scores if s >= 60]
print(passed)   # [93, 78, 100]
```

含义不是“给所有人加 5 分，再筛选”，而是：

- 先遍历所有分数；
- 只保留 `s >= 60` 的分数；
- 对保留的分数加 5。

这里也提醒一个常见问题：**理解顺序错误，往往会直接导致答案写反。**

<!--v-->

## 6. 例 2：对字符串序列做处理

```python
names = ["ana", "Bob", "carl", "Dora"]
cleaned = [name.lower() for name in names if len(name) >= 4]
print(cleaned)   # ['carl', 'dora']
```

这个例子体现了推导式的两个常见用途：

- 统一格式，例如全部转小写；
- 清洗数据，例如只保留长度满足条件的字符串。

因此，推导式非常适合做**预处理、标准化、筛选**这类工作。

<!--v-->

## 7. 例 3：构造更复杂的元素

推导式中放入列表元素的，并不一定只是一个数字。

```python
nums = [1, 2, 3, 4, 5]
pairs = [(n, n ** 2) for n in nums if n % 2 == 1]
print(pairs)   # [(1, 1), (3, 9), (5, 25)]
```

这说明 `expression` 可以是：

- 一个数字；
- 一个字符串；
- 一个元组；
- 一个列表；
- 甚至更复杂的表达式。

关键在于：**新列表的每个元素，来自同一个统一生成规则。**

<!--v-->

## 8. 例 4：字符位移

课堂中出现过字符移动的例子。这里给出更正式的写法：

```python
word = "data"
shifted = ''.join([chr(ord(ch) + 1) for ch in word])
print(shifted)   # "ebub"
```

分解来看：

- `for ch in word`：逐字符遍历字符串；
- `ord(ch) + 1`：得到下一个 ASCII 编码；
- `chr(...)`：重新转回字符；
- `''.join(...)`：把字符列表拼回字符串。

这类题目本质上是在考查你是否能把**字符序列**也视为可迭代对象。

<!--v-->

## 9. 例 5：从混合数据中提取信息

```python
data = ["CS", 101, "Spring", 2026, "A"]
lengths = [len(x) for x in data if type(x) == str]
print(lengths)   # [2, 6, 1]
```

它同时考查：

- 推导式的结构；
- 类型判断；
- 表达式对不同对象的适用性。

如果你忘记了筛选步骤，直接对所有元素取 `len`，程序就会在整数处报错。

<!--v-->

## 10. 推导式与普通循环的取舍

虽然推导式很简洁，但并不意味着它总是最优选择。

适合使用推导式的情形：

- 逻辑清晰；
- 只有一层主要变换；
- 条件筛选不复杂；
- 生成的是一个新列表。

不适合使用推导式的情形：

- 分支很多；
- 需要多步中间变量；
- 需要复杂异常处理；
- 可读性会明显下降。


<!--v-->

## 11. 读代码写结果

```python
result = [x[0].upper() for x in ["apple", "banana", "kiwi"] if len(x) > 4]
```

分析步骤建议固定为：

1. 序列里有哪些元素？
2. 哪些元素通过了条件？
3. 对通过条件的元素应用了什么表达式？

最终结果：

```python
['A', 'B']
```

<!--s-->

# 三、Default Parameters

<!--v-->

## 1. 默认参数要解决什么问题？

函数接口设计时，往往会遇到这样的情况：

- 某些参数几乎总是取同一个常用值；
- 但在少数情况下，又希望调用者可以自行修改。

例如，一个日志函数通常默认在控制台打印，但偶尔也希望关闭输出或改成文件输出。

```python
def report(msg, prefix="[INFO]"):
    print(prefix, msg)
```

这就是默认参数的使用场景：**为常见情形提供默认设置，同时保留调整空间。**

<!--v-->

## 2. 基本写法

```python
def greet(name, title="Student"):
    return f"Hello, {title} {name}"
```

调用时有两种情况：

```python
greet("Alice")          # 使用默认值 "Student"
greet("Alice", "TA")    # 显式覆盖默认值
```

因此，默认参数不是“可有可无的装饰”，而是在函数定义阶段就把“常见情况”编码进了接口设计。

<!--v-->

## 3. 为什么它比全局变量更合适？

假设你想控制函数精度、打印格式或阈值，可能有几种做法：

- 直接写死在函数内部；
- 定义一个全局变量；
- 把它变成参数；
- 把它变成**带默认值的参数**。

相比之下，默认参数通常更合理，因为它：

- 不依赖外部状态；
- 不会影响所有函数调用；
- 让接口更清晰；
- 便于测试不同参数设置下的行为。

<!--v-->

## 4. 例 1：带精度的格式化函数

```python
def format_price(price, digits=2):
    return f"{price:.{digits}f}"
```

使用时：

```python
format_price(3.14159)      # '3.14'
format_price(3.14159, 4)   # '3.1416'
```

这个例子说明默认参数非常适合表示：

- 默认精度；
- 默认阈值；
- 默认模式；
- 默认开关。

<!--v-->

## 5. 例 2：搜索函数中的默认选项

```python
def contains_keyword(text, keyword, ignore_case=True):
    if ignore_case:
        text = text.lower()
        keyword = keyword.lower()
    return keyword in text
```

这里 `ignore_case=True` 就是一种常见策略：

- 多数情况下，默认采用宽松匹配；
- 若有特殊需要，可显式传入 `False`。

这比写两个几乎重复的函数更整洁，也更符合抽象思想。

<!--v-->

## 6. 默认参数的规则

在函数定义中，**有默认值的参数必须放在后面**：

```python
def f(x, y=0):     # 合法
    pass
```

```python
# def f(x=0, y):   # 不合法
#     pass
```

原因并不神秘：解释器必须知道哪些位置参数应该先和前面的参数匹配，哪些参数才可以回退到默认值。

<!--v-->

## 7. 位置参数与关键字参数

考虑如下函数：

```python
def connect(host, port=8080, secure=False):
    return host, port, secure
```

下面这些调用是清晰且合法的：

```python
connect("example.com")
connect("example.com", 9000)
connect("example.com", secure=True)
connect(host="example.com", port=9000, secure=True)
```

关键字参数的价值在于：**调用处更具可读性，也更不容易把多个同类型参数写错位置。**

<!--v-->

## 8. 什么时候适合使用默认参数？

适合的情况：

- 大部分调用都使用同一设置；
- 该设置是局部于函数的，不应交给全局变量控制；
- 该参数具有明确、稳定、合理的默认值。

不太适合的情况：

- 默认值难以解释；
- 参数很多且彼此依赖；
- 默认值会掩盖设计缺陷；
- 调用者其实几乎每次都要改动它。

<!--v-->

## 9. 设计函数接口时的一个判断标准

看到某个参数时，可以自问：

> “如果调用者不写这个参数，系统是否能给出一个合理且可预期的行为？”

若答案是肯定的，那么它往往适合作为默认参数。

例如：

- `epsilon=0.01`
- `verbose=False`
- `ignore_case=True`
- `max_retry=3`

这些默认值都具有明确语义，也能支持绝大多数常见调用。

<!--s-->

# 四、函数也是对象

<!--v-->

## 1. 什么叫“函数也是对象”？

在 Python 中，函数不仅可以被调用，也可以：

- 赋值给变量；
- 作为参数传入其他函数；
- 作为返回值返回。

例如：

```python
def is_even(x):
    return x % 2 == 0

checker = is_even
print(checker(6))   # True
```

这里 `checker` 与 `is_even` 指向的是同一个函数对象。

<!--v-->

## 2. 最容易混淆的地方

下面两句完全不是一回事：

```python
checker = is_even
checker = is_even(6)
```

第一句中，`checker` 保存的是**函数本身**；  
第二句中，`checker` 保存的是**函数调用后的结果**。

因此，看到括号时必须敏感：

- **没有括号：** 函数对象；
- **有括号：** 调用函数并获得返回值。

<!--v-->

## 3. 为什么这一点重要？

因为一旦把函数视为对象，就可以做更灵活的抽象。

例如，可以把不同“判定标准”作为可替换部件传入：

```python
def count_if(L, cond):
    total = 0
    for x in L:
        if cond(x):
            total += 1
    return total
```

这里 `cond` 并不是一个普通数据，而是一个“可执行的规则”。

<!--v-->

## 4. 例子：把规则作为参数传入

```python
def is_long_word(s):
    return len(s) >= 5

words = ["code", "python", "AI", "review"]
print(count_if(words, is_long_word))   # 2
```

这种写法的优点是：

- `count_if` 只负责遍历与计数；
- “什么叫满足条件”由外部函数决定；
- 逻辑更清晰，复用性更高。

这体现了课程中反复强调的思想：**分解与抽象。**

<!--s-->

# 五、Functions Returning Functions

<!--v-->

## 1. 返回函数在解决什么问题？

有时我们并不想每次都手写一整套新函数，而是希望：

- 先给出一个“配置”；
- 再得到一个按该配置定制好的函数。

例如：

- 固定折扣率，得到一个价格函数；
- 固定乘数，得到一个倍增函数；
- 固定阈值，得到一个判定函数。

这就是“函数工厂”的思路。

<!--v-->

## 2. 经典结构

```python
def make_multiplier(k):
    def multiply(x):
        return k * x
    return multiply
```

这里：

- 外层函数 `make_multiplier` 负责接收配置 `k`；
- 内层函数 `multiply` 负责处理具体输入 `x`；
- 返回值不是数字，而是一个已经记住了 `k` 的新函数。

<!--v-->

## 3. 两种调用方式

方式一：一步写完。

```python
print(make_multiplier(3)(5))   # 15
```

方式二：先保存函数，再调用。

```python
triple = make_multiplier(3)
print(triple(5))               # 15
```

第二种方式更常见，因为它更清晰，也更体现“先配置，再使用”的思想。

<!--v-->

## 4. 为什么 `triple` 会记住 3？

因为内层函数在定义时，已经与外层作用域中的变量建立联系。

```python
def make_multiplier(k):
    def multiply(x):
        return k * x
    return multiply
```

当执行 `make_multiplier(3)` 时，返回的 `multiply` 并不是一个“空壳”，它带着 `k = 3` 这一上下文一起被返回。

这就是闭包的直观含义：**函数不仅带代码，也带环境。**

<!--v-->

## 5. 更生动的例子：生成折扣函数

```python
def make_discount(rate):
    def discounted(price):
        return price * (1 - rate)
    return discounted
```

使用方式：

```python
student_discount = make_discount(0.15)
vip_discount = make_discount(0.30)

print(student_discount(200))   # 170.0
print(vip_discount(200))       # 140.0
```

这比每次手动写 `price * 0.85` 或 `price * 0.70` 更规范，也更不容易出错。

<!--v-->

## 6. 再举一类例子：生成判定函数

```python
def make_threshold(limit):
    def check(x):
        return x >= limit
    return check
```

```python
is_adult = make_threshold(18)
is_passing = make_threshold(60)

print(is_adult(20))    # True
print(is_passing(55))  # False
```

这类写法很适合构造“带参数的规则”。

<!--v-->

## 7. 什么时候值得返回函数？

适合的情形：

- 行为本身需要被“定制”；
- 外层函数负责配置，内层函数负责执行；
- 希望避免重复写高度相似的函数。

不适合的情形：

- 只是为了炫技；
- 普通参数传递已经足够清楚；
- 使用后反而让读者更难理解代码。

<!--v-->

## 8. 与课程思想的联系

返回函数的意义不只是“语法有趣”，而在于它体现了两层抽象：

- 第一层：把“行为”封装成函数；
- 第二层：把“生成行为的方法”再封装成函数。

因此，这一部分本质上仍然是在训练：

- 分解问题；
- 隐藏细节；
- 构造可复用模块。


<!--s-->

# 六、Testing and Debugging

<!--v-->

## 1. 测试与调试不是同一件事

这两个词经常一起出现，但目标不同：

- **Testing**：验证程序是否满足规格；
- **Debugging**：当程序不满足规格时，定位并修复问题。

换句话说：

- 测试回答的是“**它对吗？**”
- 调试回答的是“**它为什么不对？**”

如果这两者混在一起，复习时就容易只记流程，不理解方法论。

<!--v-->

## 2. Defensive Programming 的思想

课程中强调的 defensive programming，可以概括为三点：

- 为函数写清楚规格说明；
- 尽量模块化；
- 对输入、输出和关键假设做检查。

其目标不是让程序“永远不出错”，而是：

- 更早暴露错误；
- 更准确定位错误；
- 避免坏结果悄悄传播到后续步骤。

<!--v-->

## 3. 为什么模块化有利于测试？

一个 80 行的函数很难整体测试；  
四个 20 行、职责明确的函数通常容易测试得多。

原因在于：

- 每个函数的输入输出更明确；
- 边界条件更容易枚举；
- 出错范围更容易缩小；
- 改一部分后，可以有针对性地回归测试。

因此，模块化不仅是为了代码美观，更是为了降低测试与调试成本。

<!--v-->

## 4. 什么时候算“准备好测试”？

至少应满足两个条件：

- 程序已经没有语法错误、明显静态语义错误；
- 你已经知道对于哪些输入，正确输出应该是什么。

如果连预期结果都不清楚，那么所谓“测试”往往只是机械运行程序，而不是验证正确性。

<!--v-->

## 5. 三类测试

课程中提到的三类测试分别是：

- **Unit Testing**：分别测试各个函数或模块；
- **Regression Testing**：修改代码后重新跑旧测试，防止旧 bug 回归；
- **Integration Testing**：测试多个模块拼起来是否还能正常工作。

实际编程中，最容易被忽视的往往是 regression testing，因为很多人修了一个 bug，却无意中引入了另一个 bug。

<!--v-->

## 6. Black Box 的典型边界

对一个字符串处理函数，黑盒测试往往至少会考虑：

- 空字符串；
- 单字符串；
- 很长字符串；
- 全部合法字符；
- 合法与非法字符混合。

对一个数值函数，黑盒测试通常会考虑：

- 正数、负数、零；
- 很大值、很小值；
- 恰好在边界附近的值。

也就是说，黑盒测试的重点是：**按输入空间分区，并特别关注边界。**

<!--v-->

## 7. Glass Box Testing

Glass box testing 则是看实现代码，尽量覆盖不同执行路径。

重点通常包括：

- 每个 `if/else` 分支是否都走到；
- 循环是否出现“不进入、进入一次、进入多次”三种情况；
- 复合条件的不同部分是否都被触发。

它的价值在于：**有些 bug 只有真正走到某条路径时才会暴露。**

<!--v-->

## 8. 一个循环测试的例子

假设有函数：

```python
def count_positive(nums):
    total = 0
    for x in nums:
        if x > 0:
            total += 1
    return total
```

从 glass box 角度，至少要覆盖：

- `nums = []`：循环体一次也不执行；
- `nums = [3]`：循环体执行一次，且条件为真；
- `nums = [-2]`：循环体执行一次，且条件为假；
- `nums = [1, -1, 2]`：循环体执行多次。

这样才算把主要路径覆盖到了。

<!--v-->

## 9. Path-complete 也不代表绝对正确

即使一个测试集合覆盖了所有代码路径，也不能推出程序一定正确。

原因包括：

- 某条路径虽然被执行，但测试数据不足以触发隐藏 bug；
- 规格本身可能被误解；
- 多个模块组合时可能出现新的问题；
- “路径覆盖”不等于“输入空间覆盖”。

因此，测试永远是在**提高置信度**，而不是做数学证明。

<!--v-->

## 10. 调试时最重要的不是“改”，而是“定位”

低效调试的典型表现是：

- 看见报错就乱改；
- 一次改很多地方；
- 不知道 bug 真正出现在哪一步。

更合理的做法是：

1. 先复现问题；
2. 缩小错误范围；
3. 确认错误原因；
4. 再做最小修改。

这也是为什么 print、assert、模块化和单元测试会彼此配合。

<!--v-->

## 11. Print 调试为什么有效？

因为它能把程序运行过程“显形”。

例如：

```python
def avg(nums):
    print("nums =", nums)
    return sum(nums) / len(nums)
```

虽然这种方法简单，但它常常能迅速回答几个关键问题：

- 变量到底是不是你以为的值？
- 程序到底有没有进入某个分支？
- 循环到底执行了多少次？

很多调试困难，本质上是“对程序当前状态想象错误”。

<!--s-->

# 七、Lecture 12 总览

<!--v-->

## Lecture 12 的主线

Lecture 12 主要讨论两件事：

- 程序在运行中出错时，应该如何处理；
- 当数据之间存在“名称到信息”的映射关系时，应该如何组织。

前半部分关注**程序鲁棒性**，包括 exception 与 assertion；  
后半部分关注**数据抽象**，即 dictionary。

<!--v-->

## 本讲的复习重点

- 什么时候应当捕获异常，什么时候应当直接停止；
- `try/except`、`raise`、`assert` 各自解决什么问题；
- 为什么某些任务用 list 很别扭，而用 dict 很自然；
- 如何根据“访问模式”选择数据结构。

<!--s-->

# 八、Exceptions 与 try/except

<!--v-->

## 1. 什么是 exception？

当程序执行遇到与预期不一致的情况时，Python 会抛出异常。

常见异常包括：

- `IndexError`：索引越界；
- `TypeError`：类型不匹配；
- `NameError`：变量不存在；
- `ZeroDivisionError`：除以零；
- `ValueError`：值的形式不合法。

异常不是“程序脾气不好”，而是在提醒你：**当前执行状态已经超出原有假设。**

<!--v-->

## 2. 为什么异常处理重要？

如果对异常完全不处理，程序通常会直接终止。  
这种行为有时是正确的，但并非总是最优。

例如，一个表单输入函数可能会遇到非法输入；  
一个文件读取函数可能会遇到缺失文件；  
一个分数统计函数可能会遇到空列表。

问题不是“会不会出错”，而是：**出错时要不要继续，以及如何继续。**

<!--v-->

## 3. `try/except` 的基本结构

```python
try:
    # 可能出错的代码
except SomeError:
    # 出错后的处理逻辑
```

含义是：

- 先执行 `try` 中的语句；
- 若没有异常，则正常继续；
- 若抛出匹配的异常，则转入对应的 `except` 分支。

因此，`try/except` 不是为了掩盖错误，而是为了**控制错误的传播方式**。

<!--v-->

## 4. 例 1：用户输入

```python
try:
    year = int(input("Enter a year: "))
    print(2026 - year)
except ValueError:
    print("Input must be an integer.")
```

这里捕获 `ValueError` 的理由很清楚：

- 用户输入不可完全信任；
- 非法输入是系统预料之中的情况；
- 我们希望程序给出反馈，而不是直接崩溃。

这类场景非常适合使用异常处理。

<!--v-->

## 5. 例 2：字典查找

```python
prices = {"apple": 5, "banana": 3}

try:
    print(prices["orange"])
except KeyError:
    print("orange is not in the price table.")
```

这个例子说明：  
异常处理并不只针对用户输入，也适用于**运行时环境中的不确定性**，例如：

- key 是否存在；
- 文件是否存在；
- 数据格式是否符合预期。

<!--v-->

## 6. 捕获具体异常比裸 `except` 更好

不推荐这样写：

```python
except:
    print("something went wrong")
```

因为它的问题在于：

- 会把本不该吞掉的错误也吞掉；
- 错误信息过于模糊；
- 调试时很难定位真实原因。

更合理的做法是明确写出异常类型，例如：

```python
except ValueError:
except ZeroDivisionError:
except KeyError:
```

这样处理逻辑才是可解释的。

<!--v-->

## 7. 多个 `except` 的使用

```python
try:
    a = int(input("a = "))
    b = int(input("b = "))
    print(a / b)
except ValueError:
    print("Input must be integers.")
except ZeroDivisionError:
    print("b cannot be zero.")
```

这个结构体现出两个原则：

- 不同错误应尽量对应不同反馈；
- 只有当错误原因不同，修复建议才有意义。

对使用者而言，这比一句笼统的 “Bug in input” 更有帮助。

<!--v-->

## 8. 什么时候应当 `raise`？

如果函数发现了自己无法合理处理的问题，与其静默返回错误值，不如直接抛出异常：

```python
def read_score(s):
    if not s.isdigit():
        raise ValueError("score must contain only digits")
    return int(s)
```

`raise` 的意义在于：

- 明确告诉调用者“这里出现了非法状态”；
- 阻止坏结果继续传播；
- 让错误在更合适的层级被处理。

<!--v-->

## 9. 不要用异常掩盖设计问题

异常处理不是“万能补丁”。

如果某段代码逻辑混乱、类型不清、输入约定不明，再多写几个 `try/except` 也无法真正提高质量。  
更好的做法仍然是：

- 先写清楚规格；
- 再设计数据流；
- 最后处理真正不可避免的不确定性。
<!--s-->

# 九、Assertions

<!--v-->

## 1. `assert` 是什么？

`assert` 用于检查某个你认为“本应为真”的条件：

```python
assert condition, "message"
```

如果条件为真，程序继续执行；  
如果条件为假，抛出 `AssertionError`。

它的重点不是“优雅处理错误”，而是：

- 尽早暴露违背约定的状态；
- 清楚指出是哪条假设失效了。

<!--v-->

## 2. `assert` 与 `try/except` 的角色不同

二者都与错误有关，但用途不同：

- `try/except`：面对**外部不确定输入**时，决定如何应对；
- `assert`：面对**程序内部应满足的契约**时，要求立刻暴露问题。

简言之：

- exception 更像“应急处理”；
- assertion 更像“契约检查”。

<!--v-->

## 3. 例 1：检查前置条件

```python
def average(nums):
    assert len(nums) > 0, "nums must be non-empty"
    return sum(nums) / len(nums)
```

这里使用 `assert` 很合理，因为：

- “列表不能为空”是函数前提；
- 如果前提不满足，继续计算没有意义；
- 让程序在这里停下，比返回错误结果更安全。

<!--v-->

## 4. 例 2：检查中间状态

`assert` 不一定只用于函数入口，也可用于程序内部的重要状态检查：

```python
def normalize(score):
    assert 0 <= score <= 100, "score out of range"
    return score / 100
```

这种写法的意义在于：

- 一旦上游把坏数据传进来，问题会在最靠近源头的位置被暴露；
- 调试时更容易定位责任边界。

<!--v-->

## 5. 什么时候适合用 `assert`？

适合：

- 检查函数前提是否成立；
- 检查关键不变量；
- 检查某一步计算后状态是否仍然合法。

不太适合：

- 处理普通用户输入错误；
- 作为日常分支逻辑的替代品；
- 用来“温柔地恢复”程序执行。

换言之，`assert` 不是为了容错，而是为了**拒绝带病运行**。

<!--v-->

## 6. 成绩统计例子：三种策略

设有函数：

```python
def avg(grades):
    return sum(grades) / len(grades)
```

若 `grades = []`，就会出现 `ZeroDivisionError`。  
此时常见的三种策略是：

1. 打印警告并返回特殊值；
2. 改变规则，空列表平均分记为 0；
3. 用 `assert` 直接禁止这种输入。

这三种都可能“能运行”，但语义完全不同。

<!--v-->

## 7. 为什么第三种往往更利于调试？

如果你的系统规定“学生必须至少有一项成绩”，那么空列表并不是普通情况，而是数据错误。  
此时：

```python
assert len(grades) != 0, "no grades data"
```

往往比悄悄返回 `0.0` 更合理，因为返回 `0.0` 可能在后续步骤中被误当作真实成绩。

<!--v-->

## 8. `assertions vs exceptions`

可以用一句话区分：

- **Exception**：世界不稳定，所以我要处理意外；
- **Assertion**：程序应满足契约，否则立刻停止。

这一区分在设计代码时非常重要，因为它决定你是在做：

- “输入恢复”，还是
- “逻辑防线”。

<!--s-->

# 十、Dictionaries

<!--v-->

## 1. 为什么列表有时不够用？

如果我们想存储学生成绩，使用多个平行列表会很不方便：

```python
names = ["Ana", "John", "Matt"]
grades = ["A", "B", "A-"]
emails = ["a@x.com", "j@x.com", "m@x.com"]
```

问题在于：

- 多个列表必须始终同步；
- 查找某个人的信息需要先找索引；
- 更新数据时容易漏改某一列。

这说明，列表更适合按**位置**访问，而不适合按**名称**访问。

<!--v-->

## 2. 字典的核心思想

字典存储的是 **key : value** 映射关系。

```python
grades = {
    "Ana": "A",
    "John": "B",
    "Matt": "A-"
}
```

此时最自然的查询方式变成：

```python
grades["Ana"]
```

它不再依赖整数索引，而依赖“名字”这一逻辑标识。

<!--v-->

## 3. 什么时候应优先考虑字典？

如果你的主要问题是：

- “根据名字找信息”；
- “根据编号找对象”；
- “根据单词找频数”；
- “根据课程号找教室”；

那么字典通常比列表更自然。

可以概括为：  
**当访问模式是‘由标识到内容’，字典往往优于列表。**

<!--v-->

## 4. 基本语法

```python
d = {}
d = {"apple": 5, "banana": 3}
```

其中：

- key 通常是不可变对象，如 `str`、`int`、`tuple`；
- value 可以是几乎任意对象，如数字、字符串、列表，甚至另一个字典。

因此，字典是一种非常灵活的组织结构。

<!--v-->

## 5. 查找、添加、修改、删除

```python
prices = {"apple": 5, "banana": 3}

print(prices["apple"])   # 5
prices["orange"] = 4     # 添加
prices["banana"] = 6     # 修改
del prices["apple"]      # 删除
```

这几类操作都围绕同一个核心动作：**通过 key 直接定位 value。**

<!--v-->

## 6. 查找失败会发生什么？

```python
prices = {"apple": 5}
# print(prices["orange"])   # KeyError
```

这与列表越界类似，说明“你请求的标识不存在”。

因此，在不确定 key 是否存在时，常见策略包括：

- 先用 `in` 判断；
- 使用异常处理；
- 在更高层级保证 key 一定存在。

<!--v-->

## 7. `in` 检查的到底是什么？

这是一个非常容易考错的点。

```python
grades = {"Ana": "B", "Matt": "A"}
```

```python
"Ana" in grades    # True
"A" in grades      # False
```

原因是：  
**对字典使用 `in`，默认检查的是 key，而不是 value。**

这一点若记错，许多判断题都会写反。

<!--v-->

## 8. 迭代字典的三种常见方式

```python
grades.keys()
grades.values()
grades.items()
```

典型写法：

```python
for name in grades:
    ...

for grade in grades.values():
    ...

for name, grade in grades.items():
    ...
```

其中最常用、也最推荐的是 `items()`，因为它同时给出 key 与 value，最适合写清楚逻辑。

<!--v-->

## 9. 不要依赖遍历顺序来思考题目

课程中强调一个学习策略：

> 在初学阶段，应把字典视为“无固定顺序”的映射。

这样做的好处是，你会把注意力集中在：

- key 和 value 的对应关系；
- 查找和更新逻辑；
- 数据结构是否合适；

而不会把答案建立在某种偶然顺序上。

<!--v-->

## 11. 例子：嵌套字典

```python
students = {
    "Ana": {"grade": "A", "email": "ana@x.com"},
    "Bob": {"grade": "B", "email": "bob@x.com"}
}
```

查询时：

```python
students["Ana"]["email"]
```

嵌套字典适用于“对象—属性”结构，例如：

- 学生 → 成绩、邮箱、作业状态；
- 商品 → 价格、库存、类别；
- 城市 → 人口、面积、邮编。

<!--v-->

## 12. list 与 dict 如何选择？

可以用一个简单判断：

- 如果核心是“按位置处理一串元素”，用 **list**；
- 如果核心是“按名字/编号查对应信息”，用 **dict**。

例如：

- 保存考试成绩序列：list 更自然；
- 保存“学生名 → 成绩”：dict 更自然；
- 保存词序列：list；
- 保存“单词 → 出现次数”：dict。

<!--s-->

# 十一、字典综合应用

<!--v-->

## 1. 词频统计？

它几乎天然对应字典：

- key：单词；
- value：出现次数。

题目看似在做文本处理，实际上在考查两件事：

- 是否能识别“映射关系”；
- 是否能用循环不断更新字典状态。

<!--v-->

## 2. 第一步：预处理文本

```python
text = "code code review makes code clearer"
words = text.split()
print(words)
# ['code', 'code', 'review', 'makes', 'code', 'clearer']
```

`split()` 的作用是把字符串转成单词列表。  
注意：真正工程场景中还会涉及大小写、标点和清洗，但在课程层面，核心结构已经足够清楚。

<!--v-->

## 3. 第二步：累计词频

```python
freq = {}
for word in words:
    if word in freq:
        freq[word] += 1
    else:
        freq[word] = 1
```

这段代码非常重要，因为它体现了字典更新的标准范式：

- 如果 key 已经存在，就在旧值基础上更新；
- 如果不存在，就初始化。

<!--v-->

## 4. 结果是什么样？

```python
print(freq)
# {'code': 3, 'review': 1, 'makes': 1, 'clearer': 1}
```

这个结果说明字典不是“按出现顺序保存所有单词”，而是把相同单词**压缩汇总**成一个计数字段。

<!--v-->

## 5. 再进一步：找最高频单词

```python
best_word = None
best_count = -1
for word, count in freq.items():
    if count > best_count:
        best_word = word
        best_count = count
```

这里再次用到课程中的一个基本模式：

- 设立当前最优解；
- 遍历所有候选；
- 若发现更优者，则更新。

因此，词频题往往不是单独考字典，而是把字典与遍历结合起来。

<!--v-->

## 6. 更正式的函数化写法

```python
def count_words(text):
    freq = {}
    for word in text.split():
        if word in freq:
            freq[word] += 1
        else:
            freq[word] = 1
    return freq
```

这类题目中，函数规格应尽量写清楚：

- 输入是什么；
- 输出是什么；
- 是否区分大小写；
- 是否保留标点。

规格越清楚，测试越容易做。

<!--s-->
# 总结

<!--v-->

## 最后总结

- **表达层面**：用 comprehension、默认参数、函数抽象，把代码写得更清楚；
- **可靠性层面**：用 testing、debugging、exception、assertion，让程序行为更可控；
- **数据层面**：用 dictionary 按照“标识 → 信息”的方式组织数据。

<!--v-->

## Q&A 环节

有任何问题欢迎提问。

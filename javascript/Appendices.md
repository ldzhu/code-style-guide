## 附录

#### JSDoc标签参考

​	JSDoc在JavaScript中有多种用途。除了用于生成文档外，它还用于控制工具。最著名的是Closure Compiler 类型注解

##### 类型注解和其他Closure Compiler 注解

​	Closure Compiler 使用的JSDoc注解见[Annotating JavaScript for the Closure Compiler](https://github.com/google/closure-compiler/wiki/Annotating-JavaScript-for-the-Closure-Compiler)和

 [Types in the Closure Type System](https://github.com/google/closure-compiler/wiki/Types-in-the-Closure-Type-System)两篇文章。

##### 文档注解

​	除了在[Annotating JavaScript for the Closure Compiler](https://github.com/google/closure-compiler/wiki/Annotating-JavaScript-for-the-Closure-Compiler) 中描述的JSDoc之外，下面纯粹以生成文档为目的的标签是通用的，并且得到了很多文档生成工具的支持（例如JsDossier）。

| 标签                  | 模板                                       | 描述                                       |
| ------------------- | ---------------------------------------- | :--------------------------------------- |
| `@author` 或`@owner` | `@author username@google.com (First Last)` | 记录文件的作者或测试者，通常只在`@fileoverview`注释中使用，`@owner`标签在单元测试中用于确定测试结果归谁所有。 |
| `@bug`              | `@bug bugnumber`                         | 指明测试函数是回归哪些bug。多个bug应该每个都有自己的`@bug`行，确保尽可能容易的搜索到回归测试函数。 |
| `@code`             | `{@code...}`                             | 指明JSDoc描述中的某个术语是代码，使得在生成的文档中能被正确格式化      |
| `@see`              | `{@see Link}`                            | 查看另一个类函数或方法                              |
| `@supported `       | `@supported Description`                 | 在摘要中使用，用来指明支持哪些浏览器                       |
| `@desc`             | `@desc Message`                          |                                          |

​	你可能在第三方代码中见过其他JSDoc的类型注解，他们在JSDoc工具包标签引用中，但并不是合法的谷歌风格。

##### 框架特定注解

​	下面是特定框架的指定注解：

| 标签            | 模板            | 文档                                       |
| ------------- | ------------- | ---------------------------------------- |
| `@expose`     | `@expose`     |                                          |
| `@inheritDoc` | `@inheritDoc` | https://github.com/google/closure-compiler/wiki/Polymer-Pass |

##### 注意标准Closure Compiler注解的说明

​	下面的标签曾经是标准但现在已弃用：

| 标签              | 模板            | 描述                                 |
| --------------- | ------------- | ---------------------------------- |
| `@expose`       | `@expose`     | 弃用，不要使用，用`@export`或`@nocollapse`替代 |
| **@nocollapse** | `@inheritDoc` | 弃用，不要使用，用`@override`替代             |



#### 常见的理解有误的样式规则

​	以下是谷歌JS编码规范的一些不为人知或通常理解有误的事实。（以下是真实的陈述；这不是在瞎扯）

- 源文件中既不需要版权声明，也不需要`@author`。（也没有明确的建议）

- 除了第一个是构造函数外，类中其他成员没有严格规定其顺序。

- 空块通常可以简洁的表示为`{}`，详情见[空块：可以简化](空块：可以简化)。

- 换行的主要目的是：倾向于在较高语法层级上断开（[从哪里断开](从哪里断开)）。

- JSDoc、注释、字符串字面量中允许出现非ASCII码字符，事实上，当它们比等价的转义

  Unicode更容易阅读时，我们推荐使用非ASCII码字符。



#### 样式相关工具

​	下面这些已有的工具从各个方面支持谷歌风格。

##### Closure Compiler

​	该程序执行类型检测和其他检测、优化和其他转换（例如ES6转成低级的ES5代码）。

##### clang-format

​	该程序重新格式化JS源代码为谷歌风格，此外，还进行一些非必需但常能提高可读性的格式化操作。

​	`clang-format `不是必需的。作者允许改变其输出，审查员也可以要求更改输出；争议按照通常的方式解决。但是，子目录中的文件可以不选择在本地强制执行。

##### Closure compiler linter

​	该程序检测各种错误步骤和对立模式。

##### Conformance framework

​	Conformance framework工具是Closure Compiler的一部分，它为开发者提供了一种简单的方法来指定一组额外的检测，运行在基于标准检测的代码上。Conformance可以禁止访问某个属性、调用某个方法，或是丢失位置的类型信息。

​	这些规则通常用在关键限制上（例如定义全局变量，可能会破坏原本的代码）和安全模式里（例如使用`eval`或`innerHTML`）。

​	获取更多信息参考官方文档[JS Conformance Framework](https://github.com/google/closure-compiler/wiki/JS-Conformance-Framework)。



#### 传统平台的特例

##### 概要

​	这部分描述了当程序员无法使用最新的ES6语法时需要遵循特殊和额外的规则。特殊情况下建议如下：

- 允许使用`var` 声明

- 允许使用`arguments`

- 允许没有默认值的可选参数




##### 使用`var`

###### `var`声明不是块级作用域

​	`var`声明的作用域在函数、脚本、模块内，这会导致产生非预期的现象，特别是在引用循环函数中的`var`声明时，例子如下：

```javascript
for (var i = 0; i < 3; ++i) {
  var iteration = i;
  setTimeout(function() { console.log(iteration); }, i*1000);
}

// logs 2, 2, 2 -- NOT 0, 1, 2
// because `iteration` is function-scoped, not local to the loop.
```

###### 声明变量尽可能地靠近第一次使用的地方

​	尽管`var`声明的作用域从函数左括号开始，为了可读性，`var`声明仍需尽量靠近第一次使用的地方。但是，如果变量在块外被引用，不要在块内声明这个变量。例如：

```javascript
function sillyFunction() {
  var count = 0;
  for (var x in y) {
    // "count" could be declared here, but don't do that.
    count++;
  }
  console.log(count + ' items in y');
}
```

###### 为常量使用`@const`注解

​	可以使用`const`关键字的全局声明，请用`@const`替代`var`声明（这对于局部变量可选）

##### 不要在块作用域内进行函数声明

不要像下面这样：

```javascript
if (x) {
  function foo() {}
}
```

​	虽然大多数JS编译器在ES6之前已支持块内函数声明，但这并不规范。在实现上彼此不一致，和现在标准的ES6块内声明函数表现也不一致。ES5及之前版本只允许在脚本根部进行函数声明，在严格模式下明确禁止函数声明出现在块作用域内。

​	为了保持一致性，用函数表达式初始化的方式替代块内函数声明。

```javascript
if (x) {
  var foo = function() {};
}
```



##### 利用`goog.provide`/`goog.require`进行依赖管理

​	`goog.provide`已弃用，就算项目中存在`goog.provide`，所有新文件应该使用`goog.module`。下面的规则只针对 之前存在的`goog.provide`文件。

###### 概要

- 先`goog.provide`语句再`goog.require`语句。将所有`goog.provide`和`goog.require`语句用空行隔开。

- 按字母顺序进行排序（首字母大写）

- `goog.provide`和`goog.require`语句不要换行，必要时才能超过80列。

- 只提供顶级符号

  ​

  ​	在2016年10月，`goog.provide`和`goog.require`的依赖管理已弃用。所有新的文件，甚至在旧文件中仍使用`goog.provide`的文件，都应使用`goog.module`。

  ​	`goog.provide`语句应该写在一起，放在开头，`goog.require`语句在其后面。这两部分用空行隔开。

  ​	和其他语言中的导入语句相似，`goog.provide`和`goog.require`语句应该独占一行，即使语句超过了80列的限制。

  ​	语句按照字母顺序排序，后续单词第一个字母要大写：

  ```javascript
  goog.provide('namespace.MyClass');
  goog.provide('namespace.helperFoo');

  goog.require('an.extremelyLongNamespace.thatSomeoneThought.wouldBeNice.andNowItIsLonger.Than80Columns');
  goog.require('goog.dom');
  goog.require('goog.dom.TagName');
  goog.require('goog.dom.classes');
  goog.require('goog.dominoes');
  ```

  ​	类中定义的成员需要在同一文件里，只有顶层类需要提供文件，文件中相同类包含多个成员定义（例如枚举，内部类等）。

  像这样：

  ```javascript
  goog.provide('namespace.MyClass');
  ```

  而不是像这样：

  ```javascript
  goog.provide('namespace.MyClass');
  goog.provide('namespace.MyClass.CONSTANT');
  goog.provide('namespace.MyClass.Enum');
  goog.provide('namespace.MyClass.InnerClass');
  goog.provide('namespace.MyClass.TypeDef');
  goog.provide('namespace.MyClass.staticMethod');
  ```

  不同命名空间下的成员可以像这样提供：

  ```javascript
  goog.provide('foo.bar');
  goog.provide('foo.bar.CONSTANT');
  goog.provide('foo.bar.method');
  ```

  ###### 利用`goog.scope`进行别名

  ​	`goog.scope`已弃用。即使项目中已使用`goog.scope`，新文件也不应使用`goog.scope`。

  ​	`goog.scope`可以用来缩短代码中对不同命名空间内符号的引用。

  ​	每个文件只能添加一个`goog.scope`的调用，并始终放在全局范围内。

  ​	`goog.scope(function() {`的调用必须以空行开始，跟在`goog.provide`语句、`goog.require`语句或顶层注释之后。调用必须在文件的最后一行关闭，在作用域的结束语句后要附加`//goog.scope`。分号与注释用两个空格分开。

  ​	和C++的命名空间相似，不要在`goog.scope`声明下缩进，而是对齐继续。

  ​	只能对那些不会被重新分配给另一个对象（例如，大多数构造函数、枚举和命名空间）的名称使用别名。不要像下面这样（参见下面是如何别名构造函数）：

  ```JavaScript
  goog.scope(function() {
  var Button = goog.ui.Button;

  Button = function() { ... };
  ...
  ```

  ​	别名必须和全局变量的最后一个属性一致。

  ```javascript
  goog.provide('my.module.SomeType');

  goog.require('goog.dom');
  goog.require('goog.ui.Button');

  goog.scope(function() {
  var Button = goog.ui.Button;
  var dom = goog.dom;

  // Alias new types after the constructor declaration.
  my.module.SomeType = function() { ... };
  var SomeType = my.module.SomeType;

  // Declare methods on the prototype as usual:
  SomeType.prototype.findButton = function() {
    // Button as aliased above.
    this.button = new Button(dom.getElement('my-button'));
  };
  ...
  });  // goog.scope
  ```

  ​

  ​




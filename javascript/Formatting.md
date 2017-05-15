### 4.格式化

**术语说明**：块状结构（block-like construct）指的是一个类、函数、方法或代码块的主体。需要注意的是([章节5.2数组字面量](Language-features.md)和[章节5.3对象字面量](Language-features.md))，任何数组或对象字面量可被选择性地视为块状结构。

> 提示：使用`clang-format`。Js社区已努力将 `clang-format`与一些流行的编辑器集成，保证`clang-format`在Js文件上正确执行。

#### 4.1大括号

##### 4.1.1大括号被用于所有控制结构

大括号与所有控制结构一起使用（例如`if`、`else`、`for`、`do`及`while`等），即使主体只包含一条语句，非空块的第一句也必须从本行开始执行。

非法：

```javascript
if (someVeryLongCondition())
  doSomething();

for (let i = 0; i < foo.length; i++) bar(foo[i]);
```

例外：一个简单的`if`语句（没有`else`语句），在单行完全适用的情况下，为提高可读性，该单行可不需要大括号。 这是控制结构可以省略大括号或换行的唯一情况。

```javascript
if (shortCondition()) return;
```

##### 4.1.2非空块：K & R 风格

对于非空块和块状结构，大括号遵循 Kernighan 和 Ritchie 风格（[Egyptian brackets](https://blog.codinghorror.com/new-programming-jargon/)）：

- 左大括号前不换行
- 左大括号后换行
- 右大括号前换行
- 如果右大括号是一个语句、函数体或类的终止，则右大括号后换行。如果右大括号后面跟着`else`、`catch`、`while`或逗号、分号、右括号，那么右大括号后面不换行。

例如：

```javascript
class InnerClass {
  constructor() {}

  /** @param {number} foo */
  method(foo) {
    if (condition(foo)) {
      try {
        // Note: this might fail.
        something();
      } catch (err) {
        recover();
      }
    }
  }
}
```

##### 4.1.3空块：可以简化

空块或块状构造可以在打开之后立即关闭，在`{}`之间没有字符、空格或换行，除非它是多块语句的一部分（直接包含多个块：`if/else`或`try/catch/finally`），即使大括号内没内容，右大括号后也要换行。

例如：

```javascript
function doNothing() {}
```

非法：

```javascript
if (condition) {
  // …
} else if (otherCondition) {} else {
  // …
}

try {
  // …
} catch (e) {}
```

#### 4.2块缩进：2个空格

每次打开新的块或块状结构时，缩进增加2个空格。当块结束时，缩进返回到上一缩进级别。缩进级别适用于整个块中的代码和注释。(详见[章节4.1.2非空块：K & R 风格](#4.1.2非空块：K & R 风格)中的代码示例)

#####  4.2.1数组字面量：可被选择性地视为块状

任何数组字面量可被选择性地像块状结构一样格式化，例如下列都是有效的（并非详尽清单）：

```javascript
const a = [
  0,
  1,
  2,
];

const b =
    [0, 1, 2];
```

```javascript
const c = [0, 1, 2];

someMethod(foo, [
  0, 1, 2,
], bar);
```

其他组合也是允许的，特别当强调元素之间的语义而分组时，而不仅仅是为了减少较大数组的垂直大小。

##### 4.2.2对象字面量：可被选择性地视为块状

任何对象字面量可被选择性地像块状结构一样格式化，同样的例子适用于[章节4.2.1数组字面量：可被选择性地视为块状](#4.2.1数组字面量：可被选择性地视为块状) ，例如下列都是有效的（并非详尽清单）：

```javascript
const a = {
  a: 0,
  b: 1,
};

const b =
    {a: 0, b: 1};
```

```javascript
const c = {a: 0, b: 1};

someMethod(foo, {
  a: 0, b: 1,
}, bar);
```
##### 4.2.3类字面量

类字面量（无论是声明还是表达式）都像块一样缩进，不要在方法或类声明的右大括号后面加分号（像赋值语句，包含类表达式的情况仍以分号结尾）。除非该类扩展了一个模板化类型，否则用`extends`关键字，而不是用`@extends`JsDoc注解。

例如：

```javascript
class Foo {
  constructor() {
    /** @type {number} */
    this.x = 42;
  }

  /** @return {number} */
  method() {
    return this.x;
  }
}
Foo.Empty = class {};
```

```javascript
/** @extends {Foo<string>} */
foo.Bar = class extends Foo {
  /** @override */
  method() {
    return super.method() / 2;
  }
};

/** @interface */
class Frobnicator {
  /** @param {string} message */
  frobnicate(message) {}
}
```

##### 4.2.4函数表达式

当在函数调用的参数列表中声明一个匿名函数时，函数体的缩进比上一级缩进增加2个空格。

例如：

```javascript
prefix.something.reallyLongFunctionName('whatever', (a1, a2) => {
  // Indent the function body +2 relative to indentation depth
  // of the 'prefix' statement one line above.
  if (a1.equals(a2)) {
    someOtherLongFunctionName(a1);
  } else {
    andNowForSomethingCompletelyDifferent(a2.parrot);
  }
});

some.reallyLongFunctionCall(arg1, arg2, arg3)
    .thatsWrapped()
    .then((result) => {
      // Indent the function body +2 relative to the indentation depth
      // of the '.then()' call.
      if (result) {
        result.use();
      }
    });
```

##### 4.2.5 Switch语句

和其他块一样，switch块里的内容缩进增加2个空格。

在switch标签右括号后新起一行，该行缩进级别增加2个空格，就像块被打开一样。一个明确的块在语法层面上被用来划分变量的作用域。switch标签左括号返回上一级缩进，就行块被关闭一样。

每个case和break语句块之间可选择性的用空行隔开。

例如：

```javascript
switch (animal) {
  case Animal.BANDERSNATCH:
    handleBandersnatch();
    break;

  case Animal.JABBERWOCK:
    handleJabberwock();
    break;

  default:
    throw new Error('Unknown animal');
}
```

#### 4.3语句

##### 4.3.1每行一个语句

每个语句后要换行。

##### 4.3.2必须有分号

每个语句必须以分号结尾，不能依赖自动插入的分号。

#### 4.4列限制:80

Js代码的列限制为80个字符。除非下面的例外情况，否则超过此限制的任何行都必须被换行，详见[章节4.5换行](#4.5换行)所述。

**例外：**

1. 不可能满足列限制的行(例如，可被复制粘贴的，JSDoc中的一个长URL，或是一个长的shell命令)。
2. `goog.module`和`goog.require`语句(见[章节3.3 goog.module语句](Source-file-structure.md)和[章节3.3 goog.require语句](Source-file-structure.md))。

#### 4.5换行

**术语说明：** 当单行表达式的代码被分为多行时，我们称之为换行。

我们并没有全面、确定性的准则来决定在每一种情况下如何换行。很多时候，对于同一段代码会有好几种有效的换行方式。

> **说明：** 虽然换行的典型原因是为了避免溢出列限制，但事实上符合列限制的代码也可以进行换行，这由代码作者自行决定。

> **提示：** 通过提取方法或局部变量可以解决不得不换行的问题。

##### 4.5.1从哪里断开

自动换行的基本准则是：更倾向于在**更高的语法级别**处断开。如：

建议：

```javascript
currentEstimate =
    calc(currentEstimate + x * currentEstimate) /
        2.0f;
```

不建议：

```javascript
currentEstimate = calc(currentEstimate + x *
    currentEstimate) / 2.0f;
```

在上面的例子中，语法级别从高到低依次为：赋值、除法、函数调用、传参、数字常量。

运算符包装如下：

1. 换行在运算符后断开。（注意：这一点与 Google Java语言的编程风格不同。）这条规则不适用于"."，它并不能称为真正的运算符。

2. 方法名或构造函数名与左括号（`(`）留在同一行。

3. 逗号（`,`）与其前面的内容留在同一行。

> 说明：换行的主要目的是为了使代码更为清晰，但对代码要求行数越少越好，则换行就没有必要了。

##### 4.5.2换行缩进至少增加4个空格

换行时，第一行后面的每一行（每个连续行）从原始行缩进至少增加4个空格，除非它属于块缩进的规则。

当存在连续换行时，缩进可能会适当的变为不止4个空格，一般而言，较深语法级别的连续行以4的倍数增加缩进，两个连续行使用相同的缩进当且仅当它们开始于同级语法元素。

[章节4.6.3水平对齐](#4.6.3水平对齐)一节中指出，不鼓励使用可变数目的空格将内容和前面的行对齐。

#### 4.6空白

##### 4.6.1垂直空白

以下情况需要使用一个空行：

1. 在类或对象字面量的连续方法之间 
   **例外：** 两个连续字段之间的空行（在它们之间没有其他代码）是可选的。这样的空白行根据需要用于创建字段的逻辑分组。
2. 在方法体内，语句的逻辑分组间使用空行。函数体的开始和结束处不允许有空行。
3. 类内的第一个成员前或最后一个成员后的空行是可选的。（既不鼓励也不反对这样做，视个人喜好而定。）
4. 要满足本文档中其他节的空行要求。

多个连续的空行是允许的，但没有必要这样做(也不鼓励这样做)。

##### 4.6.2水平空白

使用水平空白取决于位置，主要分为三大类：头部（一行的开始），尾部（一行的结束）和内部。头部空白（例如缩进）在别处说明，尾部空白被禁止。

除了语言需求和其它风格规则，除了文字，注释和Jsdoc用到单个空格，单个ASCII空格仅出现在以下几个地方：

1. 分隔任何保留字（如：`if`、`for`或`catch`）与紧随其后的左括号（`(`）。

2. 分隔任何保留字（如：`else`或`catch`）与其前面的右大括号（`}`）。

3. 在任何左大括号前（`{`），两个例外： 
   a.当对象字面量是函数的第一个参数或是数组字面量的第一个元素时（例如：`foo({a:[{c:d}]})`）

   b.在模板表达式中，被语法所禁止（例如：`abc${1+2}def`）

4. 在二元或三元运算符的两侧。

5. 在逗号（`,`）或分号（`;`）后，注意这些字符之前不允许使用空格。

6. 在对象字面量中冒号（`:`）后面

7. 以双斜杠（`//`）开始的单行注释，双斜杠两边都要空格。这里可以允许多个空格，但没有必要。

8. 在JsDoc注解开启字符（`/**`）后和关闭字符（`*/`）两边（例如：简写的格式声明和类型转换：

   `this.foo = /** @type {number} */ (bar);` or `function(/** string */ foo) {`）


##### 4.6.3水平对齐：不推荐

**术语说明：** 水平对齐指的是通过增加可变数量的空格来使某一行的字符与上一行的相应字符对齐。

这是允许的，但Google编程风格对此**不推荐**。即使对于已经使用水平对齐的代码，我们也不需要去保持这种风格。

以下示例先展示未对齐的代码，然后是对齐的代码，两种都可以，但后者不做要求：

```javascript
{
  tiny: 42, // this is great
  longer: 435, // this too
};

{
  tiny:   42,  // permitted, but future edits
  longer: 435, // may leave it unaligned
};
```

> 提示：对齐可增加代码可读性，但它为日后的维护带来问题。考虑未来某个时候，我们需要修改一堆对齐的代码中的一行。这可能导致原本很漂亮的对齐代码变得错位，这是被允许的。很可能它会提示程序员（比如你）调整周围代码的空白来使这一堆代码重新水平对齐。一行改动现在引发了一个区域“爆炸”。这就会让你做许多的无用功，增加了reviewer的工作并且可能导致更多的合并冲突。

##### 4.6.4函数参数

倾向于把所有函数参数和函数名放在同一行，如果这么做超过了80列的限制，参数必须在合适的地方换行。为了节省空间，你可能会尽可能的在接近80的地方换行，或者将每个参数放一行以增强可读性。缩进应该为4个空格，可以和圆括号对齐，但不推荐。下面为参数换行的几种最常规模式：

```javascript
//参数从新的一行开始，缩进4个空格。当参数和函数名（或"function"关键字）在同一行不合适，但完全适合在第二行时，倾向这么写。在函数名很长、没有重复缩进的保留参数名、空间不足的情况下很有效
doSomething(
    descriptiveArgumentOne, descriptiveArgumentTwo, descriptiveArgumentThree) {
  // …
}

// 如果参数列表很长，在80个字符处换行，使用较少的垂直控件，但违反矩形规则因此不推荐。
doSomething(veryDescriptiveArgumentNumberOne, veryDescriptiveArgumentTwo,
    tableModelEventHandlerProxy, artichokeDescriptorAdapterIterator) {
  // …
}

// 四个空格，每行一个参数，在函数名很长的时候使用这种写法，保留参数名，强调每个参数。
doSomething(
    veryDescriptiveArgumentNumberOne,
    veryDescriptiveArgumentTwo,
    tableModelEventHandlerProxy,
    artichokeDescriptorAdapterIterator) {
  // …
}
```

#### 4.7用小括号来限定组：推荐

除非作者和reviewer都认为去掉小括号也不会使代码被误解，或是去掉小括号能让代码更易于阅读，否则我们不应该去掉小括号。我们没有理由假设读者能记住整个运算符优先级表。

不要给这些表达式加上多余的小括号：

`delete`, `typeof`, `void`, `return`, `throw`, `case`, `in`, `of`, or `yield`.

类型转换需要加上小括号：`/** @type {!Foo} */ (foo)` 。

#### 4.8注释

本节讨论注释的实现。 JSDoc在[章节7 JSDoc](JSDoc.md)中单独讲解。

##### 4.8.1块注释风格

块注释与其周围的代码在同一缩进级别。它们可以是`/* ... */`风格，也可以是`// ...`风格。为了在没有额外上下文的情况下使注释较为明显，对于多行的`/* ... */`注释，后续行必须以`*`开始， 并且与前一行的`*`对齐。当变量名和方法名不足够表达其含义时，应该在参数后对其添加注释。

```javascript
/*
 * This is
 * okay.
 */

// And so
// is this.

/* This is fine, too. */

someFunction(obviousParam, true /* shouldRender */, 'hello' /* name */);
```

注释不要封闭在由星号或其它字符绘制的框架里。

对上述任何注释不要使用JSDoc（`/**···*/`）。


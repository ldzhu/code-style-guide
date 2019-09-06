## 语言特性
Javascript包含许多模棱两可的（甚至是危险的）特性。本章节描述了哪些特性可以或者不可以被使用，以及在使用它们时的一些附加约束。

### 局部变量声明

#### 使用`const`和`let`
使用`const`或者`let`声明局部变量。默认使用`const`，当一个变量需要重新赋值时使用`let`。不能使用`var`关键字。

#### 单独声明每个变量
每个局部变量声明仅声明一个变量。不能使用像`let a = 1, b = 2;`这样的变量申明。

#### 需要时才声明变量，并尽快初始化
局部变量不能习惯地声明在包含块或者块状结构（类、成员函数和构造函数的实现部分（花括号中间部分））的开头。反而应该声明在它们首次使用尽可能进的地方（合理的地方），目的是减小它们的作用域。

#### 根据需要声明类型
JSDoc的类型注解可以加在变量声明代码行上一行，或者变量声明代码行内，变量名的前面。

示例：
```javascript
const /** !Array<number> */ data = [];

/** @type {!Array<number>} */
const data = [];
```

> 提示：在很多情况下，编译器可以推断出模板类型而不是其参数。特别是当常量初始化或者构造函数调用不包含模板参数类型的值这种情况下（例如：空的数组、对象、Map或Set），或者变量在闭包中被修改。局部变量类型注解在这些情况下特别有用，否则编译器会将模板参数推断为未知类型。

### 数组字面量

#### 使用尾部逗号
每当在数组的最后一个元素和关闭括号之间有一个换行时，包含一个尾部逗号。

```javascript
const values = [
  'first value',
  'second value',
];
```

#### 不要使用可变数组构造函数
如果参数被添加或删除，构造函数非常容易出错。使用一个字面量来替代。

非法的：
```javascript
const a1 = new Array(x1, x2, x3);
const a2 = new Array(x1, x2);
const a3 = new Array(x1);
const a4 = new Array();
```

除了第三种情况，这是按预期工作的：
- 如果x1是整数，则a3是一个大小为x1的数组，其中所有元素都是undefined；
- 如果x1是任何其他数字，则会抛出异常；
- 如果x1是其他的，那么他将是一个单元素数组，这个元素就是x1。

应该这样写
```javascript
const a1 = [x1, x2, x3];
const a2 = [x1, x2];
const a3 = [x1];
const a4 = [];
```

如果恰当，允许使用`new Array(length)`这种方式明确地给一个数组分配指定的长度。

#### 非数字属性
不要在数组（length除外）上定义或使用非数字属性。使用`Map`或`Object`代替。

#### 解构
可以在赋值语句的左侧使用数组字面量来执行结构（例如从单个数组或可遍历对象中解包多个元素）。可以包括最后的`rest`元素（`...`和变量名之间没有空格）。未使用的元素应该省略。

示例：
```javascript
const [a, b, c, ...rest] = generateResults();
let [, b,, d] = someArray;
```

解构也可以用于函数参数（注意参数名称是必须但忽略的）。如果解构的数组参数是可选的，则始终指定`[]`作为默认值，并在左侧提供默认值：

```javascript
/** @param {!Array<number>=} param1 */
function optionalDestructuring([a = 4, b = 2] = []) { … };
```

非法的：
```javascript
function badDestructuring([a, b] = [4, 2]) { … };
```

> 提示：对于将多个值包装/解包到函数的参数或返回值中，尽可能选择对象解构而不是数组解构，因为它允许命名各个元素并为每个元素指定不同的类型。

#### 展开运算符
数组字面量可能包括展开运算符（`...`），以使一个或多个其他可遍历对象中的元素扁平化。应该使用展开运算符，而不是使用`Array.prototype`这种笨拙的结构。`...`后面没有空格。

示例：
```javascript
[...foo]   // preferred over Array.prototype.slice.call(foo)
[...foo, ...bar]   // preferred over foo.concat(bar)
```

### 对象字面量

#### 使用尾部逗号
每当在对象的最后一个属性和关闭括号之间有一个换行时，包含一个尾部逗号。

#### 不要使用对象构造函数
虽然对象没有数组那样的问题，但为了一致性仍然不允许使用对象构造函数。使用对象字面量（例如`{}`或`{a: 0, b: 1, c: 2}`）代替。

#### 对象的`key`不要混用引号
对象字面量可以表示结构体（键/标志没有引号）或者字典（可计算的键使用引号）。不要在一个对象字面量里面混用这两种键类型。

非法的：
```javascript
{
  a: 42, 	// 结构体风格的 没有引号的 key
  'b': 43, 	// 字典风格的 有引号的 key
}
```

#### 计算的属性名
计算的属性名（例如：`{['key' + foo()]: 42}`）是允许使用的，并且被认为是字典式的键，除非计算的属性是一个标志（例如：[Sysbol.iterator]）。枚举值也可用于计算的键，但在同一个字面量里不能和其他非枚举的键混用。

#### 方法简写
方法可以使用方法简写（`{method() {...}}`）定义在对象字面量里，以此来代替冒号后跟一个`function`或者箭头函数。

示例：
```javascript
return {
  stuff: 'candy',
  method() {
    return this.stuff; // Returns 'candy'
  },
}
```

注意：方法简写或函数中的`this`指的是对象字面量自身，然而箭头函数中的`this`指向对象字面量外部的作用域。

示例：
```javascript
class {
  getObjectLiteral() {
    this.stuff = 'fruit';
    return {
      stuff: 'candy',
      method: () => this.stuff,  // Returns 'fruit'
    };
  }
}
```

#### 属性简写
对象字面量中允许使用属性简写。

示例：
```javascript
const foo = 1;
const bar = 2;
const obj = {
  foo,
  bar,
  method() {return this.foo + this.bar;},
};
assertEquals(3, obj.method());
```

#### 解构
对象解构模式可以在赋值语句的左侧使用，以从单个对象中执行解构/解包多个值。

解构对象也可以用作函数的参数，但应尽可能简单：单层无引号的缩写属性。更深层次的嵌套和计算属性可能不会用于参数解构。在解构参数的左侧指定默认值（`{str = 'some default'} = {}`，而不是`{str} = {str: 'some default'}`），如果解构对象本身是可选的，则它必须默认为`{}`。用于解构参数的JSDOC可以被赋予任何名称（这个名称没有被使用，但对于编译器来说是必须的）。

示例：
```javascript
/**
 * @param {string} ordinary
 * @param {\{num: (number|undefined), str: (string|undefined)\}=} param1
 *     num: The number of times to do something.
 *     str: A string to do stuff to.
 */
function destructured(ordinary, {num, str = 'some default'} = {})
```

非法的：
```javascript
/** @param {\{x: {num: (number|undefined), str: (string|undefined)}\}} param1 */
function nestedTooDeeply({x: {num, str}}) {};
/** @param {\{num: (number|undefined), str: (string|undefined)\}=} param1 */
function nonShorthandProperty({num: a, str: b} = {}) {};
/** @param {\{a: number, b: number\}} param1 */
function computedKey({a, b, [a + b]: c}) {};
/** @param {\{a: number, b: string\}=} param1 */
function nontrivialDefault({a, b} = {a: 2, b: 4}) {};
```

解构也可以用于`goog.require`语句，在这种情况下不能被包装：整个语句占一行，不管它有多长（参见 “goog.require语句”章节）

#### 枚举
枚举是通过将`@enum`注解添加到对象字面量来定义的。定义完成后，其他属性不能添加到枚举中。枚举必须是常量，而且所有的枚举值都必须是不可改变的。

```javascript
/**
 * Supported temperature scales.
 * @enum {string}
 */
const TemperatureScale = {
  CELSIUS: 'celsius',
  FAHRENHEIT: 'fahrenheit',
};

/**
 * An enum with two options.
 * @enum {number}
 */
const Option = {
  /** The option used shall have been the first. */
  FIRST_OPTION: 1,
  /** The second among two options. */
  SECOND_OPTION: 2,
};
```

### 类

#### 构造函数
构造函数对于具体的类来说是可选的。子类构造函数必须在设置任何字段或访问`this`之前调用`super()`方法。接口不能定义构造函数。

#### 字段
在构造函数中为所有实际字段赋值（即除方法之外的所有属性）。使用`@const`注解的字段永远不能被重新赋值（这些不需要深刻的不可变）。必须使用`@private`对私有字段进行注解 ，其名称必须以下划线结尾。具体类的原型上的字段永远不能被赋值。

示例：
```javascript
class Foo {
  constructor() {
    /** @private @const {!Bar} */
    this.bar_ = computeBar();
  }
}
```

>提示：在构造函数完成之后，不应将在类的实例里添加或删除属性，因为它会显著阻碍虚拟机的优化能力。如果需要，延迟初始化的字段应在构造函数中显式设置为`undefined`，以防止稍候的类型更改。为一个对象添加`@struct`注解将检查未声明的属性没有被添加/访问。类默认添加了这个注解。

#### 计算属性
计算属性只能在属性为符号时在类中使用。字典式属性（即带引号的或计算的非符号的键，如“对象的`key`不要混用引号”章节定义的那样）是不允许使用的。应该对任何逻辑上可迭代的类定义`[Symbol.iterator]`方法。除此之外，`Sysbol`应该谨慎使用。

>提示：小心使用任何其他的内置符号（例如：`Symbol.isConcatSpreadable`），因为他们不会被编译器填充，因此在旧版浏览器中不起作用。

#### 静态方法
在不影响可读性的情况下，优先选择模块局部函数而非私有静态方法。

静态方法只能在基类本身上调用。不应该在包含动态实例的变量上调用静态方法（如果这么做，必须使用`@nocollapse`定义），该动态实例可能是构造函数或子类构造函数。不能在没有定义此方法的子类上直接调用静态方法。

非法的：
```javascript
class Base { /** @nocollapse */ static foo() {} }
class Sub extends Base {}
function callFoo(cls) { cls.foo(); }  // discouraged: don't call static methods dynamically
Sub.foo();  // illegal: don't call static methods on subclasses that don't define it themselves
```

#### 旧式类声明
虽然ES6类是首选，但是有些情况下使用ES6类可能不可行。例如：
- 如果存在或者即将存在某些子类，包括创建子类的框架，不能立即改成ES6类的语法。如果这样一个类使用ES语法，所有没有使用ES6类语法的下游子类也需要同时修改。
- 在调用超类构造函数之前，框架需要知道一个已知的`this`值，因为具有ES6超类的构造函数直到`super`调用返回之前无法访问实例的`this`，

在所有其他方面，本风格指南仍然适用于此规则：`let`,` const`，默认参数，`rest`参数和箭头函数都应该在合适的时候使用。

`goog.defineClass`允许类似ES6类语法的类的定义：
```javascript
let C = goog.defineClass(S, {
  /**
   * @param {string} value
   */
  constructor(value) {
    S.call(this, 2);
    /** @const */
    this.prop = value;
  },

  /**
   * @param {string} param
   * @return {number}
   */
  method(param) {
    return 0;
  },
});
```

或者，虽然`goog.defineClass`应该是所有新代码的首选，但也允许使用更传统的语法。

```javascript
/**
  * @constructor @extends {S}
  * @param {string} value
  */
function C(value) {
  S.call(this, 2);
  /** @const */
  this.prop = value;
}
goog.inherits(C, S);

/**
 * @param {string} param
 * @return {number}
 */
C.prototype.method = function(param) {
  return 0;
};
```

每个实例属性应该在构造函数中定义，如果有一个超类，应该在调用超类构造函数之后定义实例属性。应该在构造函数的原型上定义方法。

正确定义构造函数原型层级结构比首次定义构造函数更难。因此，最好使用`the Closeure Library`库中的`goog.inherits`。

#### 不要直接操纵原型
类关键字允许比定义原型属性更信息、更可读的类定义。普通的实现代码没有理由操纵这些对象，尽管他们对于定义“5.4.5旧式类声明”章节中定义的`@record`接口和类仍然很有用。明确禁止混合和修改内置对象的原型。

例外：框架代码（例如`Polymer`或者`Angular`）可能需要使用原型，不应该为了避免使用原型而采取更糟糕的解决办法。

例外：在接口中定义字段（请参见“5.4.9 接口”章节）。

#### `Getters`和`Setters`
不要使用[JavaScript的getter和setter属性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get)。他们可能令人惊讶和难以推理，并且在编译器中的支持有限。提供普通方法替代。

例外：当使用数据绑定框架（如`Angular`和`Polymer`）时，可以谨慎使用getters和setters。但是请注意，编译器的支持有限。当使用他们时，必须在类或者对象字面量中使用 `get foo()`或者`set foo(value)`定义，或者如果不可能，则使用`Object.defineProperties`进行定义。不要使用`Object.defineProperty`， 这会干扰属性重命名。getters不能改变可观察状态。

非法的：
```javascript
class Foo {
  get next() {return this.nextId++;}
}
```

#### 重写`toString`
`toString`方法可能被重写，但是必须始终是成功执行的，并且不能有可见的副作用。

> 提示：特别地方在toString方法里面调用其他方法，因为特殊条件下可能导致死循环。

#### 接口
接口可以用`@interface`或者`@record`声明。通过`@record`声明的接口可以被类或者对象字面量显式（即通过`@implements`）或隐式的继承。

接口的所有非静态方法体必须为空块。字段必须在接口体之后定义为原型上的存根。

示例：
```javascript
/**
 * Something that can frobnicate.
 * @record
 */
class Frobnicator {
  /**
   * Performs the frobnication according to the given strategy.
   * @param {!FrobnicationStrategy} strategy
   */
  frobnicate(strategy) {}
}

/** @type {number} The number of attempts before giving up. */
Frobnicator.prototype.attempts;
```

### 函数

#### 顶级函数
导出的函数可以直接定义在`exports`对象上，也可以在局部定义然后单独导出。鼓励使用后者，局部定义的函数不能被声明为`@private`。

示例：
```javascript
/** @return {number} */
function helperFunction() {
  return 42;
}
/** @return {number} */
function exportedFunction() {
  return helperFunction() * 2;
}
/**
 * @param {string} arg
 * @return {number}
 */
function anotherExportedFunction(arg) {
  return helperFunction() / arg.length;
}
/** @const */
exports = {exportedFunction, anotherExportedFunction};
```

```javascript
/** @param {string} arg */
exports.foo = (arg) => {
  // do some stuff ...
};
```

#### 嵌套函数和闭包
函数可能包含嵌套函数定义。如果给函数一个名称是有用的，那么他应该被分配给一个本地的`const`。

#### 箭头函数
箭头函数提供了简洁的语法，并解决了许多与`this`相关的难题。相对于`function`关键字更倾向于使用箭头函数来定义函数，特别是对于嵌套函数（参见“5.3.5方法简写”章节）。

相对于`f.bind(this)`，特别是`goog.bind(f, this)`，更倾向于使用箭头函数。避免写`const self = this`这种语句。箭头函数对回调函数特别有用，有时会传递意外的附加参数。

箭头的右侧可以是单个表达式或者块。如果只有一个非结构化的参数，参数周围的括号是可选的。

>提示：对只有一个参数的箭头函数参数仍然使用括号是最佳实践。因为当增加一个参数而忘记添加括号时，代码可能仍然会正常合理的解析（但是不是我们想要的逻辑，是错误的）。

#### 生成器
生成器启用了许多有用的抽象，可以根据需要使用。

当定义生成器函数时，将`*`添加到`function`关键字（如果存在）后面，并将其与函数名称之间使用一个空格隔开。当使用委托`yields`时，将`*`添加到`yield`关键字。

示例：
```javascript
/** @return {!Iterator<number>} */
function* gen1() {
  yield 42;
}

/** @return {!Iterator<number>} */
const gen2 = function*() {
  yield* gen1();
}

class SomeClass {
  /** @return {!Iterator<number>} */
  * gen() {
    yield 42;
  }
}
```

#### 参数
必须在函数定义之前的JSDoc中使用JSDoc注解定义函数参数类型，除了在省略所有类型的`@overrides`相同签名的情况下。

参数类型可以在行内指定，紧接在参数名称之前（如`(/** number */ foo, /** string */ bar) => foot + bar;`）。行内指定的方式和通过`@param`类型注解的方式不能在同一个函数定义内混用。

##### 默认参数
参数列表中的可选参数允许使用`=`运算符。可选参数必须在`=`两边都包含空格，可以像所需参数一样命名（即不带`opt_`前缀），在JSDoc类型注释的所需参数后添加`=`后缀，不使用会产生可观察到的副作用的初始化。所有的可选参数在函数定义时必须有一个默认值，即使值是`undefined`。

示例：
```javascript
/**
 * @param {string} required This parameter is always needed.
 * @param {string=} optional This parameter can be omitted.
 * @param {!Node=} node Another optional parameter.
 */
function maybeDoSomething(required, optional = '', node = undefined) {}
```

谨慎使用默认参数。当存在不具有自然顺序的可选参数时，更倾向于使用解构（参见“5.3.7 解构”章节）来创建可读API。

>注意：与Python的默认参数不同，可以使用返回新的可变对象（如`{}`或`[]`）的初始化函数，因为每次使用默认值都会对初始化函数重新求值，所以一个对象不会在调用之间共享。

>提示：包括函数调用在内的任意表达式都可以用作初始化函数，但这些表达式应该因可能的简单。避免暴露共享可变状态的初始化函数，因为这样很容易在函数调用之间引入无意的耦合。

##### 剩余参数
使用剩余参数代替访问`arguments`变量。在JSDoc中剩余参数的类型是添加一个`...`前缀。剩余参数必须是参数列表中的最后一个参数。在`...`和参数名之间没有空格。不要将剩余参数命名为`var_args`。永远不要将一个局部变量或者参数命名为`arguments`，这会混淆内置的`arguments`参数。

示例：
```javascript
/**
 * @param {!Array<string>} array This is an ordinary parameter.
 * @param {...number} numbers The remainder of arguments are all numbers.
 */
function variadic(array, ...numbers) {}
```

#### 返回值
除了使用`@override`省略所有类型的相同函数签名的情况，必须在函数定义上方的JSDoc文档注释中直接指定函数的返回值类型。

#### 泛型
在必要的时候在类定义上方的JSDoc文档注释中使用`@template TYPE`来声明泛型函数和方法。

#### 展开运算符
函数调用可以使用展开运算符（`...`）。当一个数组或可迭代对象被解包成一个可变函数的多个参数时，倾向于使用展开运算符而不是`Function.prototype.apply`。在`...`后面没有空格。

示例：
```javascript
function myFunction(...elemments) {};
myFunction(...array, ...iterable, ...generator());
```

### 字符串字面量

#### 使用单引号
普通字符串字面量使用单引号（`'`）分隔，而不是双引号（`"`）。

>提示：如果一个字符串包含一个单引号，考虑使用模板字符串以避免转义。

普通字符串字面量不能跨越多行。

#### 模板字符串
在复杂的字符串连接中使用模板字符串（用\`分隔），特别是如果涉及多个字符串字面量。模板字符串可以跨越多行。

如果模板字符串跨越多行，则不需要遵循封闭块的缩进规则，尽管添加的空格可能不重要。

示例：
```javascript
function arithmetic(a, b) {
  return `Here is a table of arithmetic operations:
${a} + ${b} = ${a + b}
${a} - ${b} = ${a - b}
${a} * ${b} = ${a * b}
${a} / ${b} = ${a / b}`;
}
```

#### 不使用行延续
不要在普通或者模板字符串字面量中使用行延续（也就是在字符串中使用反斜杠结束一行）。即使ES5允许这样做，如果在斜杠之后有任何尾随的空格，它可能会导致棘手的错误，而且对代码阅读者而言并不明显。

非法的：
```javascript
const longString = 'This is a very long string that far exceeds the 80 \
    column limit. It unfortunately contains long stretches of spaces due \
    to how the continued lines are indented.';
```

相反，应该这样写
```javascript
const longString = 'This is a very long string that far exceeds the 80 ' +
    'column limit. It does not contains long stretches of spaces since ' +
    'the concatenated strings are cleaner.';
```

### 数字字面量
数字可以以十进制、十六进制、八进制或二进制表示。分别为十六进制、八进制和二进制添加准确的小写字母的（`0x`, `0o`, `ob`）前缀。除非后面立即跟着`x`, `o`或者`b`，否则字符串不要以`0`开头。

### 控制结构

#### `for`循环
ES6语言现在有三种不同的for循环。所有类型的循环都可以使用，但`for-of`循环应该尽可能的优先选择。

`for-in`循环可能只能用在词典风格的对象上（参见“5.3.3  对象的`key`不要混用引号”章节），不应该用于遍历数组。`Object.prototype.hasOwnProperty`应该用于`for-in`循环来排除不需要的原型属性。尽可能的优先选择`for-of`和`Object.keys`而不是`for-in`。

#### 异常
异常是语言的重要组成部分，应该在异常场景发生时使用。总是抛出`Error`或者`Error`类的子类：不要抛出字符串字面量或者其他的对象。当构造一个`Error`时总是使用`new`关键字。

自定义异常提供了一种从函数传递额外错误信息的好方法。应该在原生`Error`类型不足的地方定义和使用自定义异常。

倾向于通过特殊错误处理方法（例如传递错误容器引用类型或返回具有错误属性的对象）抛出异常。

##### 空`catch`块
绝大多数情况下对一个被捕获的异常什么事情都不做是不正确的。某些情况下如果真正在`catch`块中什么都不做，必须在注释中说明理由。

示例：
```javascript
try {
  return handleNumericResponse(response);
} catch (ok) {
  // it's not numeric; that's fine, just continue
}
return handleTextResponse(response);
```

非法的：
```javascript
try {
  shouldFail();
  fail('expected an error');
} catch (expected) {
  
}
```

>提示：与其他一些语言不同，像上面这样的模式根本不起作用，因为这将捕获失败抛出的错误。使用`assertThrows()`来代替。

#### `switch`语句
术语说明：`switch`语句块的花括号内有一个或多个语句组。每个语句组由一个或多个`switch`标签（`case FOO:` 或者 `default:`）组成，后面跟一个或多个语句。

##### 隐式贯穿：注释
在`switch`语句块内，每个语句组都会突然中止（`break` ,`return`, 或者`throw exception`），或者用注释标记，以指示执行将会继续进入下一个语句组。任何表达隐式贯穿概念的注释（通常是`//fall throuth`）都是可以的。该特殊注释在`switch`块的最后一个语句组中不是必需的。

示例：
```javascript
switch (input) {
  case 1：
  case 2:
  	prepareOneOrTwo();
  	//fall through
  case 3:
  	handleOneTwoOrThree();
  	break;
  default:
  	handleLargeNumber(input);
}
```

##### `default`语句块是必需的
每个`switch`语句都包含一个`default`语句组，即使`default`语句组不包含任何代码。

#### `this`
仅在类的构造器、方法，或者定义在类的构造器和方法中的箭头函数中使用`this`。任何其他使用`this`的地方必须在立即封闭的函数的JSDoc中明确声明`@this`。

永远不要使用`this`指向全局对象，`eval`表达式的上下文，`event`的`target`，或者不必要的`call(), apply()`函数。

### 不允许使用的特性

#### `with`
不要使用`with`关键字。这会让你的代码更难理解，而且从ES5之后在严格模式下被禁用。

#### 动态代码求值
不要使用`eval`或者`Function(...string)`构造器（代码加载程序除外）。这些特性是潜在的危险，并且在CSP（内容安全策略）环境中不起作用。

#### 自动分号插入
始终使用分号终止语句（函数、类定义以及上面提到的除外）。

#### 非标准特性
不要使用非标准特性。这包括已经被删除的旧特性（例如`WeakMap.clear`），尚未标准化的新特性（例如，当前的TC39工作草案，任何阶段的提案，或提议但尚未完成的Web标准），或仅在某些浏览器中实现的专有特性。仅适用当前ECMA-262或WHATWG标准中定义的功能（请注意，针对特定API，例如Chrome扩展程序或Node.js编写的项目显然可以使用这些API）。非标准语言“扩展”（如某些外部转译器提供的）被禁止。

#### 原生类型包装对象
永远不要使用`new`对原生类型对象包装（`Boolean`, `Number`, `String`, `Symbol`），也不在类型注解中引入他们。

非法的：
```javascript
const /** Boolean */ x = new Boolean(false);
if (x) alert(typeof x); // alert 'object' - WAT?
```

包装器可以作为强制函数调用（其优于使用`+`或连接空字符串或创建标志）。

示例：
```javascript
const /** boolean */ x = Boolean(0);
if (!x) alert(typeof x); // alerts 'boolean', as expected
```

#### 修改内置对象
永远不要修改内置类型，无论是通过向其构造函数或原型添加方法。避免依赖这样做的库。请注意，JS编译器的运行时库将尽可能提供符合标准的填充，没有别的可以修改内置对象。

除非绝对必须的情况下（例如，第三方API要求），不要将变量添加到全局对象。

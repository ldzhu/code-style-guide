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
 * @param {{num: (number|undefined), str: (string|undefined)}=} param1
 *     num: The number of times to do something.
 *     str: A string to do stuff to.
 */
function destructured(ordinary, {num, str = 'some default'} = {})
```

非法的：
```javascript
/** @param {{x: {num: (number|undefined), str: (string|undefined)}}} param1 */
function nestedTooDeeply({x: {num, str}}) {};
/** @param {{num: (number|undefined), str: (string|undefined)}=} param1 */
function nonShorthandProperty({num: a, str: b} = {}) {};
/** @param {{a: number, b: number}} param1 */
function computedKey({a, b, [a + b]: c}) {};
/** @param {{a: number, b: string}=} param1 */
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




















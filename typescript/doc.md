### Doc

​	JSDoc用在所有的类、字段和方法。











#### 一般形式

​	 JSDoc块的基本形式如下例所示：

```javascript
/**
 * Multiple lines of JSDoc text are written here,
 * wrapped normally.
 * @param {number} arg A number to do something to.
 */
function doSomething(arg) { … }
```

​	或者是以下单行形式：

```javascript
/** @const @private {!Foo} A short bit of JSDoc. */
this.foo_ = foo;
```

​	如果单行注释溢出到多行，必须采用多行风格的`/**`和`*/`包裹注释。

​	许多工具从JSDoc注释中提取元数据，以实现代码校验和优化。因此，注释必须有良好的格式。



#### Markdown

​	JSDoc是用Markdown写的，虽然它在必要时可能会包含html。

​	注意，自动提取JSDoc（例如JsDossier）的工具通常会忽略纯文本格式，所以如果你这样写：

```javascript
/**
 * Computes weight based on three factors:
 *   items sent
 *   items received
 *   last timestamp
 */
```

它会输出成下面这样：

```javascript
Computes weight based on three factors: items sent items received last timestamp
```

因此用Markdown列表替换：

```javascript
/**
 * Computes weight based on three factors:
 *  - items sent
 *  - items received
 *  - last timestamp
 */
```



#### JSDoc标签

​	Google风格的JSDoc标签有的一个子集。 完整列表参见 JSDoc标签引用。 大多数标签必须独占一行，标签在行的开头。

不合法：

```javascript
/**
 * The "param" tag must occupy its own line and may not be combined.
 * @param {number} left @param {number} right
 */
function add(left, right) { ... }
```

​	不需要附加数据的简单标签（例如 `@private`, `@const`, `@final`, `@export`）可以被组合在同一行，需要时跟随一个可选类型。

```javascript
/**
 * Place more complex annotations (like "implements" and "template")
 * on their own lines.  Multiple simple tags (like "export" and "final")
 * may be combined in one line.
 * @export @final
 * @implements {Iterable<TYPE>}
 * @template TYPE
 */
class MyClass {
  /**
   * @param {!ObjType} obj Some object.
   * @param {number=} num An optional number.
   */
  constructor(obj, num = 42) {
    /** @private @const {!Array<!ObjType|number>} */
    this.data_ = [obj, num];
  }
}
```

​	什么时候组合标签、按什么顺序组合是没有硬性规定的，但需保持一致。

​	JavaScript中注解类型的一般信息参见[Annotating JavaScript for the Closure Compiler](https://github.com/google/closure-compiler/wiki/Annotating-JavaScript-for-the-Closure-Compiler) 和[Types in the Closure Type System](https://github.com/google/closure-compiler/wiki/Types-in-the-Closure-Type-System)。



#### 换行

​	标签换行需要缩进四个空格，描述文字可与上一行的描述对齐，但这种水平对齐没必要。

```javascript
/**
 * Illustrates line wrapping for long param/return descriptions.
 * @param {string} foo This is a param with a description too long to fit in
 *     one line.
 * @return {number} This returns something that has a description too long 
 *     to fit in one line.
 */
exports.method = function(foo) {
  return 5;
};
```

​	`@fileoverview`标签的描述不需要缩进。



#### 顶/文件级注释

​	一个文件开头可能会有摘要，版权声明、作者信息和默认可见级别的选项。当文件有多个类定义组成时通常建议有一个文件摘要。头部的注释旨在告诉那些不熟悉代码的阅读者这个文件里有什么。如果存在摘要，摘要将描述这个文件的内容、依赖和兼容性信息。换行不用缩进。

例如：

```javascript
/**
 * @fileoverview Description of file, its uses and information
 * about its dependencies.
 * @package
 */
```



#### 类注释

​	类、接口和记录必须有描述，包含任何模板参数，实现的接口，可见性和其他合适的标签。类的描述需要给阅读者提供足够的信息让他们知道如何、何时去使用这些类，以及正确使用类所需的任何其他注意事项。文本描述可能在构造函数上省略。`@constructor`和`@extends`注释不与类关键字一起使用，除非该类用于声明`@interface`或扩展一个泛型类。

```javascript
/**
 * A fancier event target that does cool things.
 * @implements {Iterable<string>}
 */
class MyFancyTarget extends EventTarget {
  /**
   * @param {string} arg1 An argument that makes this more interesting.
   * @param {!Array<number>} arg2 List of numbers to be processed.
   */
  constructor(arg1, arg2) {
    // ...
  }
};

/**
 * Records are also helpful.
 * @extends {Iterator<TYPE>}
 * @record
 * @template TYPE
 */
class Listable {
  /** @return {TYPE} The next item in line to be returned. */
  next() {}
}
```



#### 枚举和typedef注释

​	 枚举和typedef必须描述，公共枚举和typedef必须有一个非空的描述。个别枚举项可以记录在上一行的JSDoc注释中。

```javascript
/**
 * A useful type union, which is reused often.
 * @typedef {!Bandersnatch|!BandersnatchType}
 */
let CoolUnionType;


/**
 * Types of bandersnatches.
 * @enum {string}
 */
const BandersnatchType = {
  /** This kind is really frumious. */
  FRUMIOUS: 'frumious',
  /** The less-frumious kind. */
  MANXOME: 'manxome',
};
```



#### 方法和函数注释

​	参数和返回类型必须描述。`this`的类型在需要时描述。如果方法、参数和返回描述（非类型）可以从其余方法的JSDoc或其签名中显而易见，则可以省略。方法描述应以第三人称书写。如果一个方法覆盖了一个超类方法，它必须包含`@override`注解。如果有类型被修改，重写的方法必须包含`@param` 和 `@return`注解，但如果所有类型都一样则可省略。

```javascript
/** This is a class. */
class SomeClass extends SomeBaseClass {
  /**
   * Operates on an instance of MyClass and returns something.
   * @param {!MyClass} obj An object that for some reason needs detailed
   *     explanation that spans multiple lines.
   * @param {!OtherClass} obviousOtherClass
   * @return {boolean} Whether something occurred.
   */
  someMethod(obj, obviousOtherClass) { ... }

  /** @override */
  overriddenMethod(param) { ... }
}

/**
 * Demonstrates how top-level functions follow the same rules.  This one
 * makes an array.
 * @param {TYPE} arg
 * @return {!Array<TYPE>}
 * @template TYPE
 */
function makeArray(arg) { ... }
```

​	匿名函数不需要JSDoc，虽然当类型不能自动推测时，参数类型可以指定为内联。

```javascript
promise.then(
    (/** !Array<number|string> */ items) => {
      doSomethingWith(items);
      return /** @type {string} */ (items[0]);
    });
```



#### 属性注释

​	属性类型必须描述。如果名称和类型提供的信息足以理解代码，私有类型可省略描述。

​	公开导出的常量注释方法和属性一样。表达式初始化的`@const`属性具有明显的类型，可以省略注释。

> 提示：如果直接从一个声明了类型的构造函数参数赋值，或者直接从一个声明了返回类型的函数调用中直接赋值，那么可以认为`@ const`属性的类型是“显而易见的”。从更复杂的表达式中赋值的属性和非常量属性应该具有显式声明的类型。

```javascript
/** My class. */
class MyClass {
  /** @param {string=} someString */
  constructor(someString = 'default string') {
    /** @private @const */
    this.someString_ = someString;

    /** @private @const {!OtherType} */
    this.someOtherThing_ = functionThatReturnsAThing();

    /**
     * Maximum number of things per pane.
     * @type {number}
     */
    this.someProperty = 4;
  }
}

/**
 * The number of times we'll try before giving up.
 * @const
 */
MyClass.RETRY_COUNT = 33;
```



####类型注解

​	类型注解在`@param`, `@return`, `@this`和`@type`标签中使用，`@const`, `@export`和其他可见标签中选用，附加在JSDoc标签的类型注解必须在花括号内。

##### 可空

​	类型系统定义非空、可空的修饰符分为`!`和`?`。默认情况下原始类型（`undefined`, `string`, `number`, `boolean`, `symbol`和 `function(...): ...`）和字面量

(`{foo: string, bar: number}`) 都是非空，不用给这些类型添加`!`。对象类型

 (`Array`, `Element`, `MyClass`等) 默认可为空，但不能直接通过`@typedef`的名字判断默认为非空类型。因此，除了原始类型和字面量，其他类型都必须通过`?`或`!`显式指出它们可为空或非空。

##### 类型转换

​	在类型检测不能准确推测出表达式类型的情况下，可以通过添加类型注解绑定类型，表达式在括号内，注意，括号不能省略。

```javascript
/** @type {number} */ (x)
```

##### 模板参数类型

​	总是指定模板参数。这样能使编译器更好地工作，也能使读者更容易理解代码。

不推荐：

```javascript
const /** !Object */ users = {};
const /** !Array */ books = [];
const /** !Promise */ response = ...;
```

推荐：

```javascript
const /** !Object<string, !User> */ users = {};
const /** !Array<string> */ books = [];
const /** !Promise<!Response> */ response = ...;

const /** !Promise<undefined> */ thisPromiseReturnsNothingButParameterIsStillUseful = ...;
const /** !Object<string, *> */ mapOfEverything = {};
```

下列情况不使用模板参数：

- `object`用于类型层次结构，而不是像map式结构那样

  ​

#### 可见标签

​	可见注解（`@private`, `@package`, `@protected`）可以在@ fileoverview块中或任何导出的符号或属性中指定。无论是在函数内还是在模块的顶层，不要为局部变量指定可见性。

所有`@private`名称必须以下划线结束。

## 语言特性

本章节主要参考Google Javascript Code Style，保留了同样适用于Typescript的部分，对于Javascript的某些特性的描述（例如类,接口,函数等），
由于Typescript有更明确的类型系统，所以在本章节进行了山间。

>Javascript包含许多模棱两可的（甚至是危险的）特性。本章节描述了哪些特性可以或者不可以被使用，以及在使用它们时的一些附加约束。



### 局部变量声明


#### 使用`const`和`let`

使用`const`或者`let`声明局部变量。默认使用`const`，当一个变量需要重新赋值时使用`let`。

不能使用`var`关键字。

```typescript
/* 不推荐*/
public checkGroupGridData(originalData: any[], 
        currentData: any[], 
        checkFields: string[]): CheckSettingResult {
    validationResult: CheckSettingResult = {};
    currentData.forEach((group, groupIndex) => {
        group.items.forEach((item, itemIndex) => {
            let itemCopy = originalData[groupIndex].items[itemIndex];
            let result: CheckDataItemResult = 
                this.checkSettingItem(item, itemCopy, checkFields);
            // 省略...
            if (validationResult.valid && !result.equal) {
                let changedItem = _.cloneDeep(item);
                // 省略...
            }
        });
    });
    return validationResult;
}
s
/* 推荐*/
public checkGroupGridData(originalData: any[], 
        currentData: any[], 
        checkFields: string[]): CheckSettingResult {
    const validationResult: CheckSettingResult = {};
    currentData.forEach((group, groupIndex) => {
        group.items.forEach((item, itemIndex) => {
            const itemCopy = originalData[groupIndex].items[itemIndex];
            const result: CheckDataItemResult = 
                this.checkSettingItem(item, itemCopy, checkFields);
            // 省略...
            if (validationResult.valid && !result.equal) {
                const changedItem = _.cloneDeep(item);
                // 省略...
            }
        });
    });
    return validationResult;
}
```


#### 单独声明每个变量

每个局部变量声明仅声明一个变量。不能使用像`let a = 1, b = 2;`这样的变量申明。


#### 需要时才声明变量，并尽快初始化

局部变量不能习惯地声明在包含块或者块状结构（类、成员函数和构造函数的实现部分（花括号中间部分））的开头。反而应该声明在它们首次使用尽可能近的
地方（合理的地方），目的是减小它们的作用域。


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

除了第三种情况，其他是按预期工作的：

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

可以在赋值语句的左侧使用数组字面量来执行解构（例如从单个数组或可遍历对象中解包多个元素）。可以包括最后的`rest`元素（`...`和变量名之间没有
空格）。未使用的元素应该省略。

示例：
```javascript
const [a, b, c, ...rest] = generateResults();
let [, b,, d] = someArray;
```

解构也可以用于函数参数（注意参数名称是必须但忽略的）。如果解构的数组参数是可选的，则始终指定`[]`作为默认值，并在左侧提供默认值：

```javascript
/** @param {!Array<number>=} param1 */
function optionalDestructuring([a = 4, b = 2] = []) {};
```

非法的：

```javascript
function badDestructuring([a, b] = [4, 2]) {};
```

> 提示：对于将多个值包装/解包到函数的参数或返回值中，尽可能选择对象解构而不是数组解构，因为它允许命名各个元素并为每个元素指定不同的类型。


#### 展开运算符

数组字面量可能包括展开运算符（`...`），以使一个或多个其他可遍历对象中的元素扁平化。应该使用展开运算符，而不是使用`Array.prototype`这种笨
拙的结构。`...`后面没有空格。

示例：
```javascript
const a = [...foo];             // preferred over Array.prototype.slice.call(foo)
const b = [...foo, ...bar];     // preferred over foo.concat(bar)
```


### 对象字面量


#### 使用尾部逗号

每当在对象的最后一个属性和关闭括号之间有一个换行时，包含一个尾部逗号。


#### 使用对象字面量代替对象构造函数

虽然对象没有数组那样的问题，但为了一致性仍然不允许使用对象构造函数。使用对象字面量（例如`{}`或`{a: 0, b: 1, c: 2}`）代替。


#### 对象的`key`不要混用引号

对象字面量可以表示结构体（键/标志没有引号）或者字典（可计算的键使用引号）。不要在一个对象字面量里面混用这两种键类型。


非法的：
```javascript
{
    a: 42, 	    // 结构体风格的 没有引号的 key
    'b': 43, 	// 字典风格的 有引号的 key
}
```


#### 计算的属性名

不允许使用计算的属性名（例如：`{['key' + foo()]: 42}`）。


#### 方法简写

方法可以使用方法简写（`{method() {}}`）定义在对象字面量里，以此来代替冒号后跟一个`function`或者箭头函数。

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

解构对象也可以用作函数的参数，但应尽可能简单：单层无引号的缩写属性。更深层次的嵌套和计算属性可能不会用于参数解构。在解构参数的左侧指定默认值
（`{str = 'some default'} = {}`，而不是`{str} = {str: 'some default'}`），如果解构对象本身是可选的，则它必须默认为`{}`。用于解构参
数的JSDOC可以被赋予任何名称（这个名称没有被使用，但对于编译器来说是必须的）。

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


### 字符串字面量



#### 使用单引号

普通字符串字面量使用单引号（`´`）分隔，而不是双引号（`"`）。

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

不要在普通或者模板字符串字面量中使用行延续（也就是在字符串中使用反斜杠结束一行）。即使ES5允许这样做，如果在斜杠之后有任何尾随的空格，它可能
会导致棘手的错误，而且对代码阅读者而言并不明显。

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

数字可以以十进制、十六进制、八进制或二进制表示。分别为十六进制、八进制和二进制添加准确的小写字母的（`0x`, `0o`, `ob`）前缀。


### 控制结构



#### `for`循环

ES6语言现在有三种不同的for循环。所有类型的循环都可以使用，但`for-of`循环应该尽可能的优先选择。

`for-in`循环可能只能用在词典风格的对象上，不应该用于遍历数组。`Object.prototype.hasOwnProperty`应该用于`for-in`循环来排除不需要的原
型属性。尽可能的优先选择`for-of`和`Object.keys`而不是`for-in`。


#### 异常

异常是语言的重要组成部分，应该在异常场景发生时使用。总是抛出`Error`或者`Error`类的子类：不要抛出字符串字面量或者其他的对象。

当构造一个`Error`时总是使用`new`关键字。

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
} catch (expected) {
    
}
```


#### `switch`语句

术语说明：`switch`语句块的花括号内有一个或多个语句组。每个语句组由一个或多个`switch`标签（`case FOO:` 或者 `default:`）组成，后面跟一
个或多个语句。


##### if-else分支大于三个时使用switch-case

```typescript
/* 不推荐*/
if(data.reportType === 1) {
    this.selectedPriority = this.priorityList[0];
} else if(data.reportType === 2) {
    this.selectedPriority = this.priorityList[1];
} else if(data.reportType === 3) {
    this.selectedPriority = this.priorityList[2];
} else {
    this.isThresholdShow = true;
    this.threshold = [30, 35, 40, 45, 50, 55, 60, 65, 70];
    this.curThreshold = 50;
}

/* 推荐*/
let type = '';
switch(selectedType) {
    case ObjectTypeEnum.port:
    	type = RouteTypeEnum.port;
    	break;
    case ObjectTypeEnum.lsp:
    	type = RouteTypeEnum.lsp;
    	break;
    case ObjectTypeEnum.pw:
    	type = RouteTypeEnum.pw;
    	break;
    case ObjectTypeEnum.link:
    	type = RouteTypeEnum.link;
    	break;
    case ObjectTypeEnum.trans:
    	type = RouteTypeEnum.trans;
    	break;
    default:
    	type = '';
}
```


##### 隐式贯穿：注释

在`switch`语句块内，每个语句组都会突然中止（`break` ,`return`, 或者`throw exception`），或者用注释标记，以指示执行将会继续进入下一个
语句组。任何隐式贯穿都必须添加注释

示例：
```javascript
switch (input) {
    case 1:
        // fall through
    case 2:
  	    prepareOneOrTwo();
  	    // fall through
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

仅在类的构造器、方法，或者定义在类的构造器和方法中的箭头函数中使用`this`。

任何其他使用`this`的地方必须说明`this`的指向。

永远不要使用`this`指向全局对象，`eval`表达式的上下文，`event`的`target`，或者不必要的`call(), apply()`函数。


### 不允许使用的特性

#### `with`

不要使用`with`关键字。这会让你的代码更难理解，而且从ES5之后在严格模式下被禁用。


#### 动态代码求值

不要使用`eval`或者`Function(...string)`构造器（代码加载程序除外）。这些特性是潜在的危险，并且在CSP（内容安全策略）环境中不起作用。


#### 自动分号插入

始终使用分号终止语句（函数、类定义以及上面提到的除外）。


#### 非标准特性

不要使用非标准特性。这包括已经被删除的旧特性（例如`WeakMap.clear`），尚未标准化的新特性（例如，当前的TC39工作草案，任何阶段的提案，或提议
但尚未完成的Web标准），或仅在某些浏览器中实现的专有特性。仅适用当前ECMA-262或WHATWG标准中定义的功能（请注意，针对特定API，例如Chrome扩展
程序或Node.js编写的项目显然可以使用这些API）。非标准语言“扩展”（如某些外部转译器提供的）被禁止。


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

永远不要修改内置类型，无论是通过向其构造函数或原型添加方法。避免依赖这样做的库。请注意，JS编译器的运行时库将尽可能提供符合标准的填充，没有别
的可以修改内置对象。

除非绝对必须的情况下（例如，第三方API要求），不要将变量添加到全局对象。

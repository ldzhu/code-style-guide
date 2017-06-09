### 命名

#### 对所有标识符都通用的规则

标识符只能使用ASCII字母和数字，在少数情况下，像Angular这样的框架就会使用

下划线和不常用的美元符号（$）。

在合理范围内，尽可能的给出描述性的命名。不要担心去节约水平空间，能让你的代码被新的阅读者直观的理解更重要。不要对项目外的读者使用不明确或不熟悉的缩写，并且不要通过删除单词内的字母进行缩写。

```javascript
priceCountReader      // 没有缩写.
numErrors             // "num"是大家都知道的缩写.
numDnsConnections     // 大多数人都知道"DNS"是什么 .
```

不推荐：

```javascript
n                     // 无意义.
nErr                  // 不明确的缩写.
nCompConns            // 不明确的缩写.
wgcConnections        // 只有你的团队知道是什么意思.
pcReader              // 很多单词都可缩写为"pc".
cstmrId               // 删除了中间单词.
kSecondsPerDay        // 不要用匈牙利的符号.
```

#### 标识符类型的规则

##### 包名

​	包名称都是lowerCamelCase风格。例如`my.exampleCode.deepSpace`，不要是`my.examplecode.deepspace` 或 `my.example_code.deep_space`

##### 类名

​	Class、interface、record和 typedef命名遵循UpperCamelCase风格，在模块内部未导出的类不用`@private`标记，因此命名不用以下划线结尾。

​	类型名称通常是名词或名词短语。 例如Request，ImmutableList或VisibilityMode。 另外，接口名称有时可以是形容词或形容词短语（例如，Readable）。

##### 方法名

​	方法名都以lowerCamelCase风格编写。私有方法名必须以下划线结尾。

​	方法名称通常是动词或动词短语。 例如，`sendMessage`或`stop`。属性的 `getter` 和 `setter`方法不是必需的，如果使用它们则应将其命名为`getFoo`（返回布尔值时，命名可选择`isFoo`或`hasFoo`）或`setFoo`，替代 `getter` 和`setter`。

​	下划线可能出现在JUnit测试方法名称中，用以分隔名称的逻辑。一个典型的模式是

`test<MethodUnderTest>_<state>`，例如`testPop_emptyStack`。尚未出现给测试方法命名的标准命名准则。

##### 枚举命名

​	枚举名都以UpperCamelCase风格编写，和类的命名相似，通常是单个名词。枚举中的每一项以`CONSTANT_CASE`形式命名。

##### 常量命名

​	常量名命名模式为`CONSTANT_CASE`，全部字母大写，用下划线分隔单词。常量名没有理由以下划线结尾，因为私有静态属性可以被（隐式私有）模块局部变量代替。

###### 常量的定义

​	任何常量都是一个`@const`静态属性或模块局部`const`声明，但不是所有的`@const`静态属性和模块局部`const`都是常量。在判断是否为常量时，考虑一下这个字段是否真的像一个不可变的常量。例如，如果任何实例的观测状态可以改变，它就不是一个常量。仅仅是想永远不改变对象通常是不够的。

例如：

```javascript
// 常量
const NUMBER = 5;
/** @const */ exports.NAMES = ImmutableList.of('Ed', 'Ann');
/** @enum */ exports.SomeEnum = { ENUM_CONSTANT: 'value' };

// 非常量
let letVariable = 'non-const';
class MyClass { 
  constructor() { /** @const */ this.nonStatic = 'non-static'; } 
};
/** @type {string} */ MyClass.staticButMutable = 'not @const, can be reassigned';
const /** Set<String> */ mutableCollection = new Set();
const /** ImmutableSet<SomeMutableType> */ mutableElements = ImmutableSet.of(mutable);
const Foo = goog.require('my.Foo');  // mirrors imported name
const logger = log.getLogger('loggers.are.not.immutable');
```

​	常量名通常是名词或名词短语。

###### 本地别名

​	为了提高全名的可读性时使用本地别名。`goog.require`使用相同的规则。维护别名的最后一部分，别名也可以被用在函数中。别名必须为`const`。

例如：

```javascript
const staticHelper = importedNamespace.staticHelper;
const CONSTANT_NAME = ImportedClass.CONSTANT_NAME;
const {assert, assertInstanceof} = asserts;
```

##### 非常量字段名

​	非常量字段名（静态或其他）以lowerCamelCase风格编写,私有字段以下划线结尾。

这些名称通常是名词或名词短语。 例如，`computedValues`或`index_`。

##### 参数名

 	参数名以lowerCamelCase风格编写，注意，参数为构造函数时也适用。

​	应该避免公共方法中的单字符参数名称。

​	**例外**：当第三方框架需要时，参数名称可以以$开头。 但不适用于任何其他标识符（例如局部变量或属性）。

##### 局部变量名

​	局部变量名以lowerCamelCase风格编写。除了上述模块局部（顶层）常量。在函数作用域内的常量命名仍按lowerCamelCase风格编写。注意，当变量被赋为构造函数时也适用。

##### 模板参数名

​	模板参数名应该简化，单字或单字母标识符必须全大写，例如`TYPE`或`THIS`。

### CamelCase：定义

​	有时不止一种合理的方式可以将短语转换为CamelCase模式，例如首字母缩略词或不寻常的结构，如“IPv6”或“iOS”。为了提高可预测性，Google Style指定（尽量）使用以下确定性方案。

从名称的文本形式开始：

1.将短语转换为纯ASCII并去掉所有撇号。例如，`Müller's algorithm`转换成`Muellers algorithm`.

2.将结果划分为单词，在空格和任何存在的标点符号（通常为连字符）处分隔。

​	a.推荐：如果有任何词在常用的情况下已经具有常规的CamelCase外观，将其拆分为其组成部分（例如：`AdWords` 变为 `ad words`）。

3.将所有字母小写（包括缩写），再按照以下情况大写第一个字符：

​	a.每个单词——upper camel case风格

​	b.除了第一个单词外的每个单词——lower camel case风格

4.最后，将所有单词连接成一个词

例如：

| 文本形式                  | 正确                | 不正确               |
| --------------------- | ----------------- | ----------------- |
| XML HTTP request      | XmlHttpRequest    | XMLHTTPRequest    |
| new customer ID       | newCustomerId     | newCustomerID     |
| inner stopwatch       | innerStopwatch    | innerStopWatch    |
| supports IPv6 on iOS? | supportsIpv6OnIos | supportsIPv6OnIOS |
| YouTube importer      | YouTubeImporter   | YoutubeImporter*  |

*为可接受但不推荐

> **提示：** 一些单词在英语中有不明确的连字符：例如“nonempty”和“non-empty”都是正确的，所以方法名称`checkNonempty`和`checkNonEmpty`也都是正确的。
>








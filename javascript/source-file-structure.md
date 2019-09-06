## 源文件结构
源文件由以下顺序组成：

1. 许可或版权信息，如果需要；
2. @fileoverview JSDoc, 如果需要；
3. goog.module 语句；
4. goog.require 语句；
5. 文件的实现。

除了“文件的实现”可能有一到两个空行外，上述其他部分之间使用**一个空行**来分割。

### 许可或版权信息
如果文件有许可或版权信息，它应该放在文件的开头。

### @fileoverview JSDoc
参考[7.5头部/文件级别注释]()的格式规则。

### goog.module语句
所有的文件必须在一个单行内准确的声明一个`goog.module`，包含`goog.module`定义的行不能被封装，所以它不受80列的限制。

完整的`goog.module`参数定义了一个命名空间。视情况需要，它通常由包名（反映代码目录结构的标识符）加上类、枚举或接口名称组成的字符串。

例子：
```javascript
goog.module('search.urlHistory.UrlHistoryService');
```

#### 层级
永远不要将模块的命名空间命名为另一个模块命名空间的直接子级。

不合法的：
```javascript
goog.module('foo.bar');   // 'foo.bar.qux' would be fine, though
goog.module('foo.bar.baz');
```

目录的层级反映了命名空间的层级，所以更深嵌套层级是更高层级父目录的子目录。注意，这意味着父命名空间的所有者一定知道所有子命名空间，因为他们存在于相同的目录中。

#### `goog.setTestOnly`
一个单独的`goog.modules`语句后面可以可选的跟着一个`goog.setTestOnly`调用。

#### `goog.module.declareLegacyNamespace`
一个单独的`goog.module`语句后面可以可选的跟着`goog.module.declareLegacyNamespace()`调用。尽可能的避免`goog.module.declareLegacyNamespace()`调用

例子：
```javascript
goog.module('my.test.helpers');
goog.module.declareLegacyNamespace();
goog.setTestOnly();
```

`goog.module.declareLegacyNamespace`存在的目的是简化传统命名空间的转换，但是带来了一些命名约束的问题。鉴于子模块名必须在父命名空间之后创建，这个名称不能是任何其他`goog.module`的子或者父名称（例如：`goog.module('parent')`和`goog.module('parent.child')`不能同时安全的存在，`goog.module('parent')`和`goog.module('parent.child.grandchild')`也不行）。

#### ES6模块
不要使用ES6模块(即导出和导入关键字)，因为它们的语义尚未完成。注意，一旦语义为全标准，此策略将被修改。

### google.require 语句
导入语句是通过`goog.require`实现的，他们组合成一个代码块并且紧跟在模块定义后面。每个`goog.require`被赋值给一个单独的常量别名，或者拆分成几个常量别名。无论是在代码中还是在类型注解中，这些别名是唯一可接受的引用require依赖的方式：除了作为`goog.require`的参数，永远不要使用全限定名称。别名应该尽可能的匹配导入的模块名以`.`分割的最后一个部分的名字，如果需要消除歧义其他的部分可能也包含在内（使用恰当的大小写，这样一来别名的命名仍然可以正确地标识他们的类型），除非它显著地提升了可读性。`goog.require`语句不能出现在文件其他的任何位置。

如果一个模块只导入它的副作用，那么这个赋值可能会被省略，但是完全限定名可能不会出现在文件中其他任何地方。这个时候就需要一个注释来解释为什么这个导入是需要的，同时抑制编译警告。

导入语句按照以下规则排序：首先，在左边只有一个名字的导入语句排在前面，按照名字的字母顺序排列；其次，在左边使用解构赋值的导入语句排在之后，同样按照名字的字母顺序排列；最后，单独的`goog.require`语句排在最后（通常这些模块的导入只是为了导入它们的副作用）。

> 提示：不需要记住这个顺序规则并手动的执行。可以通过IDE来报告这些没有正确排序的导入。

如果一个很长的别名或者模块名导致导入代码超过每行80列的限制，这行代码不可以折行：`goog.require`行对于每行80列的限制规则来说是个例外。

示例：
```javascript
const MyClass = goog.require('some.package.MyClass');
const NsMyClass = goog.require('other.ns.MyClass');
const googAsserts = goog.require('goog.asserts');
const testingAsserts = goog.require('goog.testing.asserts');
const than80columns = goog.require('pretend.this.is.longer.than80columns');
const {clear, forEach, map} = goog.require('goog.array');
/** @suppress {extraRequire} Initializes MyFramework. */
goog.require('my.framework.initialization');
```

非法的：
```javascript
const randomName = goog.require('something.else'); // name must match

const {clear, forEach, map} = // don't break lines
    goog.require('goog.array');

function someFunction() {
  const alias = goog.require('my.long.name.alias'); // must be at top level
  // …
}
```

#### `goog.forwardDeclare`
`goog.forwardDeclare`并不经常需要，但他是一个打破循环依赖或者引用最近加载的代码的很有价值的工具。这些`goog.forwardDeclare`语句自身一起分组，并且紧紧跟在`goog.require`语句后面。`goog.forwardDeclare`语句必须遵循`goog.require`语句相同的排序风格规则。

### 文件的实现部分
真正的代码实现部分紧跟着所有的声明依赖信息之后（至少使用一个空行隔开）。

实现部分代码可能由模块局部定义（常量、变量、类、函数等），以及导出语句等组成。


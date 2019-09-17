## 命名



### 通用规则


标识符只能使用ASCII字母和数字，在少数情况下（像Angular这样的框架），会使用下划线和美元符号（$）。

命名要尽可能简短，但必要的话就别怕长，能让你的代码被阅读者直观的理解更重要。

不要对项目外的读者使用含糊不清或不熟悉的缩写。

不要通过删除单词内的字母进行缩写。

不要为私有属性名添加_前缀。
> 在es6之前，js没有类的概念，也没有public, private 关键字，很多js库(例如jQuery)使用_前缀的命名方式表示私有属性/方法，以便告诫使
>用者不要直接访问这些属性/方法。

```typescript
/**
 * 推荐
 */
errorCount          // 没有缩写。
dnsConnectionIndex  // 大多数人都知道“DNS”代表什么。
customerId          // “Id”无处不在，不太可能被误解。
```

```typescript
/**
 * 不推荐：
 */
n                     // 无意义.
nErr                  // 不明确的缩写.
nCompConns            // 不明确的缩写.
wgcConnections        // 只有你的团队知道是什么意思.
pcReader              // 很多单词都可缩写为"pc".
cstmrId               // 删除了中间单词.
fenYeShuLiang         // 不要使用拼音.
_i18n                 // 不要使用_前缀.
```


有时不止一种合理的方式可以将短语转换为CamelCase模式，例如首字母缩略词或不寻常的结构，如“IPv6”或“iOS”。为了提高可预测性，使用以下确定性方案。

从名称的文本形式开始：

1.将短语转换为纯ASCII并去掉所有撇号。例如，`Müller's algorithm`转换成`Muellers algorithm`.

2.将结果划分为单词，在空格和任何存在的标点符号（通常为连字符）处分隔。

    推荐：如果有任何词在常用的情况下已经具有常规的CamelCase外观，将其拆分为其组成部分
    （例如：`AdWords` 变为 `ad words`）。

3.将所有字母小写（包括缩写），再按照以下情况大写第一个字符：

    a.每个单词——upper camel case风格

    b.除了第一个单词外的每个单词——lower camel case风格

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

> **提示：** 一些单词在英语中有不明确的连字符：例如“nonempty”和“non-empty”都是正确的，所以方法名称`checkNonempty`和`checkNonEmpty`
>也都是正确的。


### 文件名命名规则


- 统一使用feature.type.ts的模式命名文件（明确标示类型component/service/enum/...）

```typescript
/* 不推荐*/
headModules.ts
head-component.ts
url.ts
  
/* 推荐*/
alarm.module.ts
header.component.ts
download.service.ts
url.const.ts
task.model.ts
task.enum.ts
join.pipe.ts
loading.directive.ts
topo.action.ts
topo.reducer.ts
```

- 使用点和横杠来分割文件名(用'-'来分割单词,用'.'来分割类型)

```typescript
/* 不推荐*/
linkSelector.component.ts
linkSelectorComponent.test.ts

/* 推荐*/
link-selector.component.ts
link-selector.component.spec.ts
```

- 相关功能的文件其根文件名部分保持一致

```typescript
/* 不推荐*/
column-selector2.component.html
style.component.scss
column-selector.component.ts
column-selector-test.component.spec.ts
selector.service.ts
selector-test.service.spec.ts

// column-selector.component.ts
@Component({
    selector: 'selector',
    templateUrl: './column-selector2.component.html',
    styleUrls: ['./style.component.scss']
})

/* 推荐*/
column-selector.component.html
column-selector.component.scss
column-selector.component.ts
column-selector.component.spec.ts
column-selector.service.ts
column-selector.service.spec.ts

// column-selector.component.ts
@Component({
    selector: 'column-selector',
    templateUrl: './column-selector.component.html',
    styleUrls: ['./column-selector.component.scss']
})
```

- 文件名与文件中的类名保持一致

```typescript
/* 不推荐*/
// 文件名为option-item.component.ts
export class LspOptionItemComponent {}
   
/* 推荐*/
// 文件名为lsp-option-item.component.ts
export class LspOptionItemComponent {}  
```


### 标识符规则



#### 类


class、interface、record和typedef命名遵循PascalCase风格。

class名通常是名词或名词短语。 例如`Request`, `ImmutableList`, `VisibilityMode`。

interface名称有时可以是形容词或形容词短语（例如，`Readable`）。

```typescript
/* 不推荐*/
export class parameterSettingComponent {}
export class parameter_setting_component {}

/* 推荐*/
export class ParameterSettingComponent {}
export class PeriodPipe implements PipeTransform {}
```


#### 方法


function命名遵循camelCase风格。

方法名称通常是动词或动词短语，例如，`sendMessage`或`stop`。


#### 属性

属性命名遵循camelCase风格。

```typescript
/* 不推荐*/
public _i18n: any;
public grid_data: GridDataResult;
public $gridData: GridDataResult;
private _flag: boolean;

/* 推荐*/
public i18n: any;
public gridData: GridDataResult;
private flag: boolean;
```


#### 枚举


enum命名遵循PascalCase风格，和类的命名相似，通常是单个名词。

枚举的key以camelCase风格命名。

枚举的value不推荐使用number。

```typescript
/* 不推荐*/
export enum ObjectType {
    NE = 1,
    LOGICAL_DOMAIN = 2,
}

/* 推荐*/
export enum ObjectType {
    ne = 'ne',
    logicalDomain = 'logicalDomain'
}
```


#### 常量


const命名遵循camelCase风格。

```typescript
/* 不推荐 */
const STORAGE_KEY_CONST: any = {
  SYSTEM_CONFIG: 'ls.sysConfig'
};

/* 推荐*/
const imageType = {
  type: 'png',
  suffix: '.png'
};
```


#### 参数


参数命名遵循camelCase风格。注意，参数为构造函数时也适用。

避免公共方法中的单字符参数名称。

**例外**：当在构造器中注入服务、或者第三方框架需要时，参数名称可以以$开头。 但不适用于任何其他标识符（例如局部变量或属性）。

```typescript
/* 不推荐 */
public topoLocate(ne_id: number, x: number, $port: number): void {};

/* 推荐*/
public topoLocate(neId: number, boardId: number, portNo: number): void {};
constructor($context: ContextService) {}
```

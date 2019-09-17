## 格式化规则

**术语说明**：块状结构（block-like construct）指的是一个类、函数、方法或代码块的主体。需要注意的是，数组或对象字面量可被选择性地视为块状结构。



### 大括号


#### 大括号被用于所有控制结构


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


#### 非空块：K & R 风格


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


### 块缩进：4个空格


每次打开新的块或块状结构时，缩进增加4个空格。当块结束时，缩进返回到上一缩进级别。缩进级别适用于整个块中的代码和注释。


#### 空块：可以简化


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


####  数组字面量：可被选择性地视为块状


任何数组字面量可被选择性地像块状结构一样格式化，例如下列都是有效的：

```javascript
const a = [
    0,
    1,
    2,
];

const b =
    [0, 1, 2];

const c = [0, 1, 2];

someMethod(foo, [
    0, 1, 2,
], bar);
```

其他组合也是允许的，特别当强调元素之间的语义而分组时，而不仅仅是为了减少较大数组的垂直大小。


#### 对象字面量：可被选择性地视为块状


任何对象字面量可被选择性地像块状结构一样格式化，例如下列都是有效的：

```javascript
const a = {
    a: 0,
    b: 1,
};

const b =
    {a: 0, b: 1};

const c = {a: 0, b: 1};

someMethod(foo, {
    a: 0, b: 1,
}, bar);
```


#### 函数表达式


当在函数调用的参数列表中声明一个匿名函数时，函数体的缩进比上一级缩进增加4个空格。

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


#### Switch语句


和其他块一样，switch块里的内容缩进增加4个空格。

在switch标签右括号后新起一行，该行缩进级别增加4个空格，就像块被打开一样。一个明确的块在语法层面上被用来划分变量的作用域。switch
标签左括号返回上一级缩进，就行块被关闭一样。

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


### 语句


#### 每行一个语句


每个语句后要换行。


#### 必须有分号


每个语句必须以分号结尾，不能依赖自动插入的分号。


### 换行


**术语说明：** 当单行表达式的代码被分为多行时，我们称之为换行。

我们并没有全面、确定性的准则来决定在每一种情况下如何换行。很多时候，对于同一段代码会有好几种有效的换行方式。

> **说明：** 虽然换行的典型原因是为了避免溢出列限制，但事实上符合列限制的代码也可以进行换行，这由代码作者自行决定。

> **提示：** 通过提取方法或局部变量可以解决不得不换行的问题。


#### 从哪里断开


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

1. 换行在运算符后断开。这条规则不适用于"."，它并不能称为真正的运算符。

2. 方法名或构造函数名与左括号`(`留在同一行。

3. 逗号`,`与其前面的内容留在同一行。

> 说明：换行的主要目的是为了使代码更为清晰，但对代码要求行数越少越好，则换行就没有必要了。


#### 换行缩进至少增加4个空格


换行时，第一行后面的每一行（每个连续行）从原始行缩进至少增加4个空格，除非它属于块缩进的规则。

当存在连续换行时，缩进可能会适当的变为不止4个空格，一般而言，较深语法级别的连续行以4的倍数增加缩进，两个连续行使用相同的缩进当且
仅当它们开始于同级语法元素。

不鼓励使用可变数目的空格将内容和前面的行对齐。


### 空白


#### 垂直空白


以下情况需要使用一个空行：

1. 在类或对象字面量的连续方法之间 

   **例外：** 两个连续字段之间的空行（在它们之间没有其他代码）是可选的。这样的空白行根据需要用于创建字段的逻辑分组。
   
2. 在方法体内，语句的逻辑分组间使用空行。函数体的开始和结束处不允许有空行。

3. 类内的第一个成员前或最后一个成员后的空行是可选的。（既不鼓励也不反对这样做，视个人喜好而定。）

4. 要满足本文档中其他节的空行要求。

多个连续的空行是允许的，但没有必要这样做(也不鼓励这样做)。


#### 水平空白


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


#### 水平对齐：不推荐


**术语说明：** 水平对齐指的是通过增加可变数量的空格来使某一行的字符与上一行的相应字符对齐。

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

> 提示：对齐可增加代码可读性，但它为日后的维护带来问题。考虑未来某个时候，我们需要修改一堆对齐的代码中的一行。这可能导致原本很
>漂亮的对齐代码变得错位，这是被允许的。很可能它会提示程序员（比如你）调整周围代码的空白来使这一堆代码重新水平对齐。一行改动现在
>引发了一个区域“爆炸”。这就会让你做许多的无用功，增加了reviewer的工作并且可能导致更多的合并冲突。


#### 函数参数

倾向于把所有函数参数和函数名放在同一行，如果这么做超过了120列的限制，参数必须在合适的地方换行。为了节省空间，你可能会尽可能的在接
近120的地方换行，或者将每个参数放一行以增强可读性。缩进应该为4个空格，可以和圆括号对齐，但不推荐。下面为参数换行的几种最常规模式：

```javascript
/* 
 * 参数从新的一行开始，缩进4个空格。当参数和函数名（或"function"关键字）
 * 在同一行不合适，但完全适合在第二行时，倾向这么写。在函数名很长、没有
 * 重复缩进的保留参数名、空间不足的情况下很有效。
 */
doSomething(
    descriptiveArgumentOne, descriptiveArgumentTwo, descriptiveArgumentThree) {
    // …
}

/*
 * 如果参数列表很长，在120个字符处换行，使用较少的垂直控件，但违反矩形
 * 规则因此不推荐。
 */
doSomething(veryDescriptiveArgumentNumberOne, veryDescriptiveArgumentTwo,
    tableModelEventHandlerProxy, artichokeDescriptorAdapterIterator) {
    // …
}

/*
 * 四个空格，每行一个参数，在函数名很长的时候使用这种写法，保留参数名，
 * 强调每个参数。
 */
doSomething(
    veryDescriptiveArgumentNumberOne,
    veryDescriptiveArgumentTwo,
    tableModelEventHandlerProxy,
    artichokeDescriptorAdapterIterator) {
    // …
}
```


### 用小括号来分组：推荐


除非作者和reviewer都认为去掉小括号也不会使代码被误解，或是去掉小括号能让代码更易于阅读，否则我们不应该去掉小括号。我们没有理由
假设读者能记住整个运算符优先级表。

不要给这些表达式加上多余的小括号：

`delete`, `typeof`, `void`, `return`, `throw`, `case`, `in`, `of`, or `yield`.

类型转换需要加上小括号：`/** @type {!Foo} */ (foo)` 。


### 列限制:120


Js代码的列限制为120个字符。除非下面的例外情况，否则超过此限制的任何行都必须被换行。

**例外：** 不可能满足列限制的行(例如，可被复制粘贴的，JSDoc中的一个长URL，或是import 一个class)。


### 单个文件只写一个类

单个文件只写一个类。（某个类只在本文件使用除外）
  
```typescript
/* 不推荐*/
import {Component, NgModule, OnInit} from '@angular/core';
import {BrowserModule} from '@angular/platform-browser';
import {platformBrowserDynamic} from '@angular/platform-browser-dynamic';

class Hero {
    id: number;
    name: string;
}

@Component({
    selector: 'my-app',
    template: `
        <h1>{{title}}</h1>
        <pre>{{heroes | json}}</pre>
      `,
    styleUrls: ['app/app.component.css']
})
class AppComponent implements OnInit {
    title = 'Tour of Heroes';

    heroes: Hero[] = [];

    ngOnInit() {
        getHeroes().then(heroes => (this.heroes = heroes));
    }
}

@NgModule({
    imports: [BrowserModule],
    declarations: [AppComponent],
    exports: [AppComponent],
    bootstrap: [AppComponent]
})
export class AppModule {
}

platformBrowserDynamic().bootstrapModule(AppModule);

const HEROES: Hero[] = [
    {id: 1, name: 'Bombasto'},
    {id: 2, name: 'Tornado'},
    {id: 3, name: 'Magneta'}
];

function getHeroes(): Promise<Hero[]> {
    return Promise.resolve(HEROES); // TODO: get hero data from the server;
}


/* 推荐*/
// main.ts
import {platformBrowserDynamic} from '@angular/platform-browser-dynamic';
import {AppModule} from './app/app.module';

platformBrowserDynamic().bootstrapModule(AppModule);

// app/app.module.ts
import {NgModule} from '@angular/core';
import {BrowserModule} from '@angular/platform-browser';
import {RouterModule} from '@angular/router';

import {AppComponent} from './app.component';
import {HeroesComponent} from './heroes/heroes.component';

@NgModule({
    imports: [
        BrowserModule,
    ],
    declarations: [
        AppComponent,
        HeroesComponent
    ],
    exports: [AppComponent],
    bootstrap: [AppComponent]
})
export class AppModule {
}

// app/app.component.ts
import {Component} from '@angular/core';
import {HeroService} from './heroes';

@Component({
    selector: 'toh-app',
    template: `
        <toh-heroes></toh-heroes>
      `,
    styleUrls: ['./app.component.css'],
    providers: [HeroService]
})
export class AppComponent {
}
  
// app/heroes/heroes.component.ts
import {Component, OnInit} from '@angular/core';
import {Hero, HeroService} from './shared';

@Component({
    selector: 'toh-heroes',
    template: `
        <pre>{{heroes | json}}</pre>
      `
})
export class HeroesComponent implements OnInit {
    heroes: Hero[] = [];

    constructor(private heroService: HeroService) {
    }

    ngOnInit() {
        this.heroService.getHeroes()
            .then(heroes => this.heroes = heroes);
    }
}

// app/heroes/shared/hero.service.ts
import {Injectable} from '@angular/core';
import {HEROES} from './mock-heroes';

@Injectable()
export class HeroService {
    getHeroes() {
        return Promise.resolve(HEROES);
    }
}

// app/heroes/shared/hero.model.ts
export class Hero {
    id: number;
    name: string;
}
  
// app/heroes/shared/mock-heroes.ts
import {Hero} from './hero.model';

export const HEROES: Hero[] = [
    {id: 1, name: 'Bombasto'},
    {id: 2, name: 'Tornado'},
    {id: 3, name: 'Magneta'}
];
```
  
  


### 单个函数不超过200行

当单个函数/方法超过200行时，代码的复杂度变高，可读性变低，应该对函数进行重构来降低复杂度。
  
```typescript
  /* 不推荐*/
  public handleDataTypeChange(event) {
      this.selectedDataType = event;
  
      // 省略：清空表格数据(10行)
      this.chartOptions.categories = [];
      this.chartOptions.series.splice(0, this.chartOptions.series.length);
  
      if (this.selectedObject.length === 0) {
          // 省略：设置chart title(60行)
  
      } else {
          this.loadChartData(this.selectedObject, (data) => {
              // 省略：预处理chart数据(100行)
              data.forEach(value => {
                  this.selectedObject.forEach((item, index) => {
                  });
              });
  
              // 省略：设置chart title(60行)
              this.chartOptions.title.text = title;
              this.chartOptions.title.detailText = detailTitle;
          });
      }
  
  }
  
  /* 推荐*/
  public handleDataTypeChange(event) {
      this.selectedDataType = event;
  
      this.clearChartData();
      if (this.selectedObject.length === 0) {
          this.setChartTitle();
      } else {
          this.loadChartData(this.selectedObject, (data) => {
              this.chartDataHandler(data, this.selectedObject);
              this.setChartTitle();
          });
      }
  
  }

  /**
   * 清空表格数据
   */
  private clearChartData(): void {}
  /**
   * 设置chart title
   */
  private setChartTitle(): void {}
  /**
   * 预处理chart数据
   */
  private chartDataHandler(): void {}
  
  ```
  
  
  


### 单个文件一般不超过1000行
  
```typescript
/* 不推荐*/

// 两个tab下分别有一个chart，在一个组件中一并处理;
// 数据处理部分也写在组件中(导致一个组件内容冗长)
@Component({
    selector: 'traffic-add-tab',
    templateUrl: './add-tab.component.html',
    styleUrls: ['./add-tab.component.scss']
})
export class AddTabComponent implements OnInit, OnDestroy {
    // ...
    // echart
    echart1: any;
    echart2: any;
    // 流速、带宽利用率切换
    chartFlag: boolean = true;

    /**
     * 数值变化
     * @param s
     */
    changeValue(s) {
        // ...

        if (this.chartFlag) {
            // ...
            CommonUtil.exportChart(this.echart1, this.exportImgName);
        } else {
            // ...
            CommonUtil.exportChart(this.echart2, this.exportImgName);
        }

        // ...
    }

    /**
     * 获取曲线图数据
     */
    readChartData(): void {
        // ...
        if (this.chartFlag) {
            this.processCondition1();
            this.readChartData1();
        } else {
            this.processCondition2();
            this.readChartData2();
        }
        // ...
    }
    
    // ...
}  
  
/* 推荐*/
// 每个组件独立;
// 组件只保留与模板交互部分的逻辑，其他复杂逻辑放入服务中
@Component({
    selector: 'otn-weight-setting',
    templateUrl: './weight-setting.component.html',
    styleUrls: ['./weight-setting.component.scss'],
    providers: [WeightSettingService]
})
export class WeightSettingComponent extends BaseComponent implements OnInit {
    public condition: QueryCondition = QueryConditionFactory.getInstance();
    public gridData: WeightSettingService;

    constructor(private weightSettingService: WeightSettingService) {
        super();
    }

    public ngOnInit(): void {
        this.gridData = this.weightSettingService;
        this.getGridData();
    }

    public dataStateChange(e: DataStateChangeEvent) {
        if (e.sort) {
            this.condition.advanced.sorter = e.sort;
        } else {
            this.condition.advanced.sorter = [];
        }

        this.getGridData();
    }

    // 保存
    public onSaveBtnClick() {
        this.weightSettingService.saveGridData();
    }

    // 刷新
    public onRefreshBtnClick() {
        this.condition = QueryConditionFactory.getInstance();
        this.getGridData();
    }

    // 获取数据
    private getGridData() {
        this.weightSettingService.getGridData(this.condition);
    }
}
```
  
  
  


### 删除无效代码
  
- 空代码块、未使用到的依赖、成员、函数、导入模块需要删除
  
```typescript
/* 不推荐*/
/**
 *  1. AfterViewInit import但未使用
 *  2. $LspOptionService注入了依赖但未使用
 *  3. targetNeOptionShow定义了属性但未使用
 */
import {AfterViewInit, Component, OnInit} from '@angular/core';
import {TransferSystemOptionService} from './transfer-system-option.service';
import {LspOptionService} from './lsp-option.service';

@Component({
    selector: 'vapp-lsp-option',
    templateUrl: './lsp-option.component.html',
    styleUrls: ['./lsp-option.component.scss'],
    providers: [LspOptionService, TransferSystemOptionService]
})
export class LspOptionComponent implements OnInit {
    targetNeOptionShow: boolean = false;

    constructor(private $LspOptionService: LspOptionService) {
    }

    handleSelectedChange(list: any[]) {
    }

}

/* 推荐*/
import {Component, OnInit} from '@angular/core';
import {TransferSystemOptionService} from './transfer-system-option.service';

@Component({
    selector: 'vapp-lsp-option',
    templateUrl: './lsp-option.component.html',
    styleUrls: ['./lsp-option.component.scss'],
    providers: [TransferSystemOptionService]
})
export class LspOptionComponent implements OnInit {
    constructor() {
    }
    
}
```

- 注释掉的无用代码需要删除（注释掉的有用代码需添加注释说明）
  
```typescript
/* 不推荐*/
export interface ColumnSelectorOption {
    gridId: string;
    prefixSkip?: number;
    // suffixSkip?: number;
    // defaultHidden?: string[];
}

/* 推荐*/
export interface ColumnSelectorOption {
    gridId: string;
    prefixSkip?: number;
    /* comment by ldzhu: 暂未实现该配置项 */
    // suffixSkip?: number;
}
```

- 断点和控制台输出语句需要删除
  
```typescript
/* 不推荐*/
debugger;
console.log(res.data);
```

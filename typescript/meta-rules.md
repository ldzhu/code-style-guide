## 元数据规则



### 注释


注释遵循[通用元数据规则](../general/general-meta-rules.md)定义的注释规则。

> Google编码规范推荐使用`JSDoc`风格的注释，但是`JSDoc`标准并不尽人意，特别是对于Typescript的支持较差。Microsoft正在制定`TSDoc`
>[标准](https://github.com/Microsoft/tsdoc)，在该标准正式发布之前，采用本节定义的注释规则。

- 配置模型需写明配置项职责、配置内容、默认值等

```typescript
/* 不推荐 */
export class Options {
    label: string;
    checked?: boolean;
    value: any;
    deletable?: boolean;
}
   
/* 推荐*/
/**
 * 列选择器配置model
 * gridId: 表格唯一ID，使用表格报表导出接口URL连接作为key（需要定制列的表格都有“导出”需求，为    
 * 了不再定义一套ID规则，所以使用导出接口URL）。
 * prefixSkip: 头部跳过的列；可不配置，默认为0，配置为1表示表格第一列不参与“自定义”，比如序号
 * 列、checkbox列等。
 * suffixSkip: 尾部跳过的列；可不配置，默认为0。配置为1表示表格最后一列不参与“自定义”，比如序
 * 尾部操作列等。
 * defaultHidden:默认隐藏列；可不配置，默认全部显示，将需要隐藏的列field配置在数组中，在初次
 * 查询表格时会隐藏该列。
 */
export interface ColumnSelectorOption {
    gridId: string;
    prefixSkip?: number;
    suffixSkip?: number;
    defaultHidden?: string[];
}
```

- 方法成员需写明方法职责、入参类型及描述、返回类型及描述，必要时添加注意事项、调用场景等

```typescript
/* 不推荐*/
/**
 * mini图中x轴time的显示处理
 */
xTime(data: any[]) {
    const length = data.length; // 96
    const indexArr = [
        0, 
        Math.floor((length / 3) - 1), 
        Math.floor(2 * (length / 3) - 1), 
        length - 1
    ];
    data.map((value, index) => {
        value.showTime = false;
        for (const i of indexArr) {
            if (i === index) {
                value.showTime = true;
            }
        }
    });
    return data;
}  
  
/* 推荐*/
/**
 * 排序算法
 * {string} grid 表格组件
 * {ColumnSelectorOption} columnOption 列配置
 * {ColumnSelectorInfo} columnInfo 列选择数据
 */
private reorder(grid: GridComponent, 
        columnOption: ColumnSelectorOption, 
        columnInfo: ColumnSelectorInfo) {
    const columns: QueryList<ColumnComponent | ColumnGroupComponent> =
        <QueryList<ColumnComponent | ColumnGroupComponent>>grid.columns;
    const prefix = columnOption.prefixSkip || 0;
    const showColumns = columnInfo.show;
    if (!_.isEmpty(showColumns)) {
        showColumns.forEach((showColumn, i) => {
            const column = this.getColumnByField(columns.toArray(), showColumn.value);
            if (column) {
                /**
                 * 将隐藏列设置为显示列，且排序在原先显示列之前，该方法报错,在每次reorder之                    
                 * 前，通过如下方法避免报错:
                 * 1.按照上次列设置信息，将原先隐藏列排在原先显示列后，表格显示全量列信息
                 * 2.通过列的hidden属性控制本次设置为隐藏的列隐藏
                 */
                grid.reorderColumn(column, i + prefix);
            }
        });
    }
}
```

- 属性成员需添加注释

```typescript
/* 不推荐*/
appConst = APP_CONST;
public reportData: GridDataResult;
public canSelectData: any = [];
public loading: boolean = false;
public pageSize: number = 20;
public selected: any = [];
public windowOpened: boolean = false;
public reportType: number = 1;
public isSetShow: boolean = false;
public cycleTime: number;


/* 推荐*/
// 依赖注入ZTree组件
@ViewChild(FZtreeComponent) fZTreeComponent: FZtreeComponent;
// 搜索框区域
@ViewChild('searchOperator') searchOperator: ElementRef;
// 搜索输入框
searchInput = new FormControl();
// 可观察对象：ems状态
private emsState$: Observable<EmsState>;
```

- 复杂逻辑代码需添加注释

```typescript
/* 不推荐*/

// 无注释
handleRowSelectedChange(flag: boolean, item: any) {
    item.$checked = flag;

    if (item.$checked) {
        this.rowSelectedList.push(item);
        if (item.$children) {
            item.$children.forEach(value => {
                value.$checked = item.$checked;
                this.rowSelectedList.push(value);
            });
        }
    } else {
        this.rowSelectedList = this.rowSelectedList.filter(
            row => row.lspId !== item.lspId
        );
        if (item.$children) {
            item.$children.forEach(value => {
                value.$checked = item.$checked;
                this.rowSelectedList = this.rowSelectedList.filter(
                    row => row.lspId !== value.lspId
                );
            });
        }
    }
}
  
/* 推荐*/
// 订阅搜索框输入值改变事件流
this.searchInput.valueChanges
    .debounceTime(1000)
    .startWith('')    // 赋初始值，保证为string
    .skip(1) // 跳过初始值
    .map((value: string) => {
        return value.trim(); // 字符串去掉首尾的空格
    })
    .distinctUntilChanged() // 去重
    .filter((value: string) => {
        // 空字符串时需关闭弹框
        if (_.isEmpty(value)) {
            this.isSearchResultHidden = true;
        }
        return !_.isEmpty(value); // 过滤空字符串
    })
    .switchMap((searchStr: string) => {
        const emsId = this.zTreeOptions.custom.params.emsId;
        this.searchLoading = true;
        this.isSearchResultHidden = false;
        return this.topoTreeService.queryTopoNe(searchStr, emsId);
    }) // 仅处理最后一次请求
    .subscribe((res: ResultModel<Array>) => {
        this.searchLoading = false;
        if (res.success) {
            this.searchResult = res.data;
        }
    });
```



### 遵守TSLint

使用项目配置的tslint.json, 并在IDE中配置TSLint，保存文件时格式化代码，并解决TSLint提示的问题。

## 风格规则



### 类型系统



#### 始终使用类型

始终在代码中使用ts类型。除非无法定义类型，或者使用第三方js库（例如 echarts options）。

```typescript
/* 不推荐*/
public monitorData: any;
public performance;

/* 推荐*/
public monitorData: MonitorModel;
public performance：number;
```


#### 能使用联合类型就不使用any

```typescript
/* 不推荐*/
getColumnByField(columns: any[], field: string): any {
    let column: any;

    for (let i = 0; i < columns.length; i++) {
        if (columns[i].isColumnGroup) {
        // 省略...
        } else {
            if (columns[i].field === field) {
            // 省略...
            }
        }
    }
    return column;
}

/* 推荐*/
getColumnByField(columns: (ColumnComponent | ColumnGroupComponent)[], 
        field: string): ColumnComponent | ColumnGroupComponent {
    let column: ColumnComponent | ColumnGroupComponent;

    for (let i = 0; i < columns.length; i++) {
        if (columns[i].isColumnGroup) {
            // 省略...
        } else {
            if ((<ColumnComponent>columns[i]).field === field) {
                // 省略...
            }
        }
    }
    return column;
}
```


#### 必须定义方法入参类型

```typescript
/* 不推荐*/
public transfromData2Model(data): NeStatusModel {
    const model: NeStatusModel = <NeStatusModel>{};
    for (let i = 0, len = data.length; i < len; i++) {
        const item = data[i];
        // ...
    }
    return model;
}

/* 推荐*/
public transfromData2Model(data: InspectItem[]): NeStatusModel {
    const model: NeStatusModel = <NeStatusModel>{};
    for (let i = 0, len = data.length; i < len; i++) {
        const item = data[i];
        // ...
    }
    return model;
}
```


#### 必须定义方法返回值类型

```typescript
/* 不推荐*/
fetchConvertData(viewData: any[], parent: any) {
    if (viewData) {
        return viewData.map(item => {
            return {
                isCache: false,
                $checked: false,
                $expanded: false,
            };
        });
    }
}

/* 推荐*/
analysisData2Model(params: InspectItem[]): InspectItem[] {
    for (let i = 0; i < params.length; i++) {
        const contentObj = JSON.parse(params[i].content);
        params[i] = Object.assign(params[i], contentObj);
    }
    return params;
}
```


### 严格使用访问修饰符

严格使用访问修饰符定义属性/方法。

public统一显示声明，不使用default形式。

```typescript
/* 不推荐*/
// 是否显示加载动画
loading: boolean = false;
// 内部标志位
flag： boolean = false;
// 是否显示弹窗
show: boolean = false;

onTabSelect(): void {}
processData(data:DataModel): NewDataModel {}


/* 推荐*/
// 是否显示加载动画
public loading: boolean = false;
// 是否显示弹窗
public show: boolean = false;
// 内部标志位
private flag： boolean = false;

public onTabSelect(): void {}
private processData(data:DataModel): NewDataModel {}
```


### 方法体有多行代码禁止使用箭头函数声明

```typescript
/* 不推荐*/
private checkGridData = (data: InspectItem[]) => {
    const changedItems = [];
    let valid = true;
    _.forEach(data, (group, groupIndex) => {
        _.forEach(group.items, (item, itemIndex) => {
            // ...
        });
    });
    return {changedItems, valid};
}

/* 推荐*/
private checkGridData(data: InspectItem[]): InspectItem[] {
    const changedItems = [];
    let valid = true;
    _.forEach(data, (group, groupIndex) => {
        _.forEach(group.items, (item, itemIndex) => {
            // ...
        });
    });
    return changedItems;
}
```


### 成员顺序


​成员按照统一的顺序排列：

- 按照属性成员/构造函数/钩子函数（多个钩子函数按照出现顺序排序）/事件处理函数/其他方法成员的顺序排列成员顺序

- 按照@Input/@Output/@viewChild.../普通属性成员的顺序排列属性成员顺序

- 公共成员放在前面，私有成员放在后面

- 相关属性、方法归为一组，按照层级分层聚合

```typescript
/* 不推荐*/
export class DemoComponent implements OnInit, OnDestroy, OnChanges {

    private defaults = {
        title: '',
        message: 'May the Force be with you'
    };
    @Output() public initBefore = new EventEmitter<any>();
    i18n: any;
    @Input() public height: number;
    echarts: ECharts;
    @Input() public theme: Object | string = 'default';
    public message: string;
    public title: string;
    private toastElement: any;
    @Input() public width: number;
    loading: boolean;
    @Input() public options: EChartOption;
    @Input() public geoJson: any;
    @Output() public initComplete = new EventEmitter<any>();


    ngOnDestroy() {
    }

    ngOnInit() {
    }

    ngOnChanges() {
    }

    private hide() {
    }

    public exportFile() {
    }

    public onSaveBtnClick() {
    }

    public onCancelBtnClick() {
    }

    private show() {
    }
}

/* 推荐*/
export class DemoComponent implements OnInit {
    // 输入属性定义在一起
    @Input() public options: EChartOption;
    @Input() public geoJson: any;
    @Input() public theme: Object | string = 'default';
    // 画板宽、高强关联属性定义在一起
    @Input() public width: number;
    @Input() public height: number;

    // 输出属性定义在一起
    @Output() initBefore = new EventEmitter<any>();
    @Output() initComplete = new EventEmitter<any>();

    // 公共属性定义在一起
    public message: string;
    public title: string;

    private defaults = {
        title: '',
        message: 'May the Force be with you'
    };
    private toastElement: any;

    ngOnChanges() {
    }

    ngOnInit() {
    }

    ngOnDestroy() {
    }

    public onSaveBtnClick() {
    }

    public onCancelBtnClick() {
    }

    public exportFile() {
    }

    private hide() {
    }

    private show() {
    }
}
```


### 禁止代码中直接出现中文词条

- html文件中

```html
<!-- 不推荐 -->
<div class="override left-button-group">
    <span class="vapp-button vapp-query-button" (click)="save()">保存</span>
</div>

<!-- 推荐 -->
<div class="override left-button-group">
    <span class="vapp-button vapp-query-button" (click)="save()">
        {{i18n.autoInspect.save}}
    </span>
</div>
```

- ts文件中

```typescript
/* 不推荐*/
protectTypeList = [{
    text: '无保护',
    value: ProtectTypeEnum.NONE
}, {
    text: '隧道间1:1保护',
    value: ProtectTypeEnum.TUNNEL1V1
}, {
    text: '隧道间1+1保护',
    value: ProtectTypeEnum.TUNNEL1P1
}, {
    text: '隧道内1:1保护',
    value: ProtectTypeEnum.LSP1V1
}, {
    text: '隧道内1+1保护',
    value: ProtectTypeEnum.LSP1P1
}];

/* 推荐*/
constructor(private $LocalService: LocalService) {
    this.i18n = this.$LocalService.i18n;
}
ngOnInit{
    this.storePeriodList = [{
        label: this.i18n.shared.component.onlyTimePeriod.week,
        value: TaskPeriodEnum.PERIOD_WEEK
    },{
        label: this.i18n.shared.component.onlyTimePeriod.month,
        value: TaskPeriodEnum.PERIOD_MONTH
    },{
        label: this.i18n.shared.component.onlyTimePeriod.quarterYear,
        value: TaskPeriodEnum.PERIOD_QUARTER
    },{
        label: this.i18n.shared.component.onlyTimePeriod.halfYear,
        value: TaskPeriodEnum.PERIOD_HALF_YEAR
    },{
        label: this.i18n.shared.component.onlyTimePeriod.year,
        value: TaskPeriodEnum.PERIOD_YEAR
    }];
}
```


### ajax交互

#### 界面参数传递错误不能引起后台异常

```typescript
/* 不推荐*/
getViewData() {
    // queryObject中各字段无值时必须配置空字符串，否则后台报错
    this.queryObject = {
        'sportKey': {'boardId': '', 'iNeId': '', 'strDisplayName': '', 'strKey': ''},
        'dportKey': {'boardId': '', 'iNeId': '', 'strDisplayName': '', 'strKey': ''},
        'name': '',
        'backUp': ''
    };
    this.$service.queryViewData(this.queryObject)
        .subscribe((res: ResultModel<Array>) => {
            // ...
        });
}

/* 推荐*/
getViewData() {
    // queryObject中各字段无值时不传向后台，后台自行判断保证不报错
    this.queryObject = {};
    this.$service.queryViewData(this.queryObject)
        .subscribe((res:ResultModel<Array>)=>{
            // ...
        });
}                                                                         
```


#### 后台返回data必须判空

```typescript
/* 不推荐*/
private getGridData() {
    this.$service.getGridData(this.condition).subscribe((res: ResultModel) => {
        if (res && res.success) {
            // ...
            this.otherInfo = {
                'alarmName': res.data[0].alarmName,
                // ...
            };
        }
    });
}

/* 推荐*/
private getGridData() {
    this.$service.getGridData(this.condition).subscribe((res: ResultModel<Array>) => {
        if (res && res.success) {
            // ...
            this.otherInfo = {};
            if (_.isArray(res.data) && res.data.length > 0) {
                this.otherInfo.alarmName = res.data[0].alarmName;
            } else {
                this.otherInfo.alarmName = '';
            }

        }
    });
}
```


#### 避免同一接口多次请求

```typescript
/* 不推荐*/
// 勾选表格前四条数据，调用四次loadChartData，获得的数据汇总后显示在chart上
private setChartData() {
    let [i, length] = [0, 4];
    const allChartDataPromises = [];

    if (this.gridData.length < 4) {
        length = this.gridData.length;
    }

    while (i < length) {
        allChartDataPromises.push(this.loadChartData({
            id: this.gridData[i][this.currentObject.idKey],
            name: this.gridData[i].objName,
        }));
        // ...
        i++;
    }

    Promise.all(allChartDataPromises).then(data => {});

}

/* 推荐*/
// 修改调用接口参数格式，将多次合并调用为一次
private setChartData() {
    let [i, length] = [0, 4];
    const objects = [];
    this.selectedPortObject = [];

    if (this.gridData.length < 4) {
        length = this.gridData.length;
    }

    while (i < length) {
        objects.push({
            id: this.gridData[i][this.currentObject.idKey],
            name: this.gridData[i].objName,
            alias: this.gridData[i].alternateName,
            notes: this.gridData[i].portAttribute
        });

        i++;
    }
 
    this.loadChartData(objects, (data) => {});
}
```


#### 避免异步回调函数中再进行异步请求

```typescript
/* 不推荐*/
public reset(): void {
    this.$service.reset().subscribe((result: ResultModel<DemoModel>) => {
        if (result && result.success) {
            // 重置成功后保存数据
            this.$service.saveData(this.neInspectItem)
                .subscribe((res: ResultModel<DemoModel>) => {
                    if (res && res.success) {
                        this.alert.success(this.i18n.autoInspect.saveSuccess);
                        // ...
                    }
                });
        }
    });
}
        
/* 推荐*/
// 后台修改接口，避免调用两次请求
public reset(): void {
    this.$service.reset().subscribe((result:ResultModel<DemoModel>) => {
        if (result && result.success) {
        }
    });
}
```


### 使用枚举代替常量

```typescript
/* 不推荐*/
private applyFilter(value: string, operator: string) {
    const o = operator ? operator : 'eq'; //直接使用常量
}

/* 推荐*/
import {Operator} from '../../enum/operator.enum';

@Component({
    selector: 'vapp-grid-date-filter-cell',
    templateUrl: './grid-date-filter-cell.component.html',
    styleUrls: ['./grid-date-filter-cell.component.scss']
})
export class GridDateFilterCellComponent extends BaseFilterCellComponent
    implements OnInit, AfterViewInit, OnChanges {
    ngOnInit() {
        this.listItems = [
            {
                'text': this.commonLocalService.i18n.gridDateFilterCell.gte,
                'value': Operator.gte
            },
            {
                'text': this.commonLocalService.i18n.gridDateFilterCell.eq,
                'value': Operator.eq
            },
            {
                'text': this.commonLocalService.i18n.gridDateFilterCell.lte,
                'value': Operator.lte
            }
        ];
    }
}

```


### 使用计算工具类处理加减乘除

```typescript
/* 不推荐*/
onNumTextBlur(): void {
    // 获取拓扑图中心点位置
    const coord = this.graph.viewportBounds.center;
    this.graph.centerTo(coord.x, coord.y, this.currentScale / 100);
}

/* 推荐*/
import {MathService} from '@nop_common_service/math';

export class DemoComponent {

    constructor(private $math: MathService) {
    }

    onNumTextBlur(): void {
        // 获取拓扑图中心点位置
        const coord = this.graph.viewportBounds.center;
        this.graph.centerTo(coord.x, coord.y,
            this.$math.accDiv(this.currentScale, 100));
    }

}
```


### 使用easyMock模拟数据

```typescript
/* 不推荐*/
// heroes.service.ts
...
import {Hero} from './hero';
import {HEROES} from './mock-heroes';

@Injectable()
export class HeroService {

    constructor() {
    }

    getHeroes(): Observable<Hero[]> {
        return of(HEROES); //本地模拟数据
    }
}

/* 推荐*/
// environment.ts
export const environment = {
    production: false,
    wsUrlHead: '10.168.17.68',
    // 配置easyMock服务器地址
    httpUrlHead: 'http://10.170.3.50:7300/mock/5b2c50000a3588615c59b171/otnm-inspect',
    wsUrlPort: 54013
};

// heroes.service.ts
@Injectable()
export class HeroService {

    constructor(private $http: HttpClient, private $urlService: UrlService) {
    }

    getHeroes(): Observable<Hero[]> {
        // 通过真实请求获取模拟数据
        const url = this.$urlService.getURL(URL_CONST.getHeroes);
        return this.$http.get<ResultModel<Array>>(url);
    }
}
```


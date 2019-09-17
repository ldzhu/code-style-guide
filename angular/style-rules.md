##### 为指令添加自定义前缀

  ```typescript
  /* 不推荐*/
  // TODO 放入Angular中描述
  @Directive({
      selector: '[loading]'
  })
  export class LoadingDirective implements OnInit {...}
  
  /* 推荐*/
  @Directive({
      selector: '[vappLoading]' //添加vapp前缀
  })
  export class LoadingDirective implements OnInit {...}
  ```
  
  
  ##### 事件处理方法以on开头
  
    ```typescript
    /* 不推荐*/
    public modify() {···}
    
    /* 推荐*/
    public onModifyBtnClick() {···}
    ```
    
    
##### 子组件中提供 @Input属性的默认值

```typescript
/* 不推荐 */
// 弹出框是否显示
@Input() public show: boolean;	

/* 推荐 */
// 是否禁用时间控件
@Input() public disabled: boolean = false;
// 展示时间格式
 @Input() public format: string = 'yyyy-MM-dd HH:mm:ss';
```

##### 通过 environment.ts 修改httpUrlHead

​	目前前端采用集中管理后台接口的方式存储在以`URL_CONST`命名的全局变量中，获取方式被封装到`UrlService`中

```typescript
/* 不推荐 */
saveViewData(body): Observable<ResultModel<any>> {
        const url = 'http://10.168.20.78:8083/common
  					/service/trafficThreshold/saveCoreLinkSetting';
        return this.$http.put<ResultModel<any>>(url, body);
}

/* 推荐 */
// environment配置
export const environment = {
    production: false,
    wsUrlHead: '10.168.17.68',
    httpUrlHead: 'http://10.170.160.80:8080',
    wsUrlPort: 54013
};
// 获取url
const url = this.$urlService.getURL(URL_CONST.autoInspect.queryTasks);
```

##### 使用router.navigate跳转，并使用param传参
> 系统内部跳转使用router.navigate跳转，打开新页面或者跳转另一系统使用window.open

```typescript
/* 不推荐 */
// 内部跳转
window.open('/index?areaId=2&year=2017');

/* 推荐 */
this.router.navigate(['XXX'], { queryParams: { name: 1 } });
```

##### 使用`Renderer2`操作DOM

​	`Renderer2`正是`Angular`为我们提供的一种内置渲染器，用于执行`UI`渲染操作，我们使用它进行`DOM`的操作，包括创建删除`dom`节点、添加移除样式、设置事件监听等。
```typescript
/* 不推荐 */
// 不要滥用Jquery，业务代码中尽量不要使用，最多用在公共组件中
$('XXX').css('font-size', '18px');	

/* 推荐 */
this.renderer.setStyle(this._headDiv.nativeElement, 'fontSize', '18px');
```

##### 方法调用参数应该严谨
（ Expected 0 arguments, but got 1. 报错）

​	方法调用如果参数个数不正确，虽然在开发模式打包条件下不会报错，但是会在生产模式下打包会报错表示参数个数异常，为了解决该类情况问题，请确保方法调用的参数个数和方法定义的参数个数匹配，如果确实少于定义参数个数，请将不必要的参数使用`?`可忽略表明。

```typescript
/* 不推荐 */
public changeCheck(type: string, e: any, level: string);
// 方法调用
this.changeCheck('ne');

/* 推荐 */
public changeCheck(type: string, e: any, level?: string): void {
        const checked = e.target.checked;
        if (level) {
            this.checkBoxGroup[type][level] = checked;
        } else {
            this.checkStatusForTMX[type] = checked;
        }
        this.emitData(true);
}
// 方法调用
this.changeCheck('ne', $event);
```

##### 在Service中处理逻辑

​	了解`MVC`模式的就应该了解这三者分别代表`model`模型层、`View`视图层、`Controller`控制层，虽然这是后端通常的架构模式，但是这也适用于目前`Angular+TypeScript`的前端开发模式，在代码中我们在`html`中控制页面布局显示、在组件类`TS`中控制与界面相关的操作逻辑等，而具体请求数据、数据处理等复杂逻辑应该在`service`中进行处理。如果都放在组件类中处理就使得代码结构更复杂、不易维护等。

```typescript
/* 不推荐 */
// 组件类函数过长、功能太多复杂等
private updateChartOption() {
        // 省略部分代码
        if (this.bizCondition.objectIndex === 1) {
            // 省略大量代码
        } else if (this.bizCondition.objectIndex === this.viewTypes.length) {
            // 省略大量代码
        } else {
            // 省略大量代码
            if (this.bizCondition.objectIndex === 2) {
                this.chartOptions.series = [bandwidthUtilizationAverage, bandwidthUtilizationPeak, bandwidthUtilizationBusyAverage];
            } else if (this.bizCondition.objectIndex === 3) {
                this.chartOptions.series = [bandwidthUtilizationPeak, bandwidthUtilizationAverage, bandwidthUtilizationBusyAverage];
            } else {
                this.chartOptions.series = [bandwidthUtilizationBusyAverage, bandwidthUtilizationPeak, bandwidthUtilizationAverage];
            }
            this.chartOptions.data.forEach(value => {
                this.chartOptions.series.map(value2 => {
                    if (this.type === RouteTypeEnum.PORT || this.type === RouteTypeEnum.LINK) {
                        value2.xField = 'alternateName';
                    }
                    if (typeof (value[value2.yField]) === 'number') {
                        value[value2.yField] = (value[value2.yField] * 100).toFixed(2);
                    }
                });
            });
        }
}

/* 推荐 */
// 组件类
this.netStatusModel = this.$netService.transfromData2Model(this.data);
// service类
public transfromModel2Data(model: NetStatusModel, group: FrameCheckGroup, parentCheck: LevelsCheckStauts): Array<InspectItem> {
        const inspectList: Array<InspectItem> = [];
        const checkParam: Array<LevelModel> = [];
        for (const t in parentCheck) {
            if (parentCheck.hasOwnProperty(t) && parentCheck[t] === true) {
                const temp: LevelModel = {levelName: t, frames: []};
                const childGroup = group[t];
                for (const child in childGroup) {
                    if (childGroup.hasOwnProperty(child) && childGroup[child]) {
                        temp.frames.push(child);
                    }
                }
                checkParam.push(temp);
            }
        }
        for (const item in model) {
            if (model.hasOwnProperty(item)) {
                if (model[item].inspectCode === InspectItemEnum.OAMFRAMESTATUS) {
                    model[item].levels = checkParam;
                }
                inspectList.push(model[item]);
            }
        }
        return inspectList;
}
```

##### 构造函数中注入服务以`##### 开头

​	服务命名使用`$`开头，流类型属性命名使用`$`结尾，以此在代码中容易识别变量作用。

```typescript
/* 不推荐 */
// 流类型属性
private login: Observable<StateBean>;
// 服务
private urlService: UrlService;
// 使用时不能直观了解属性作用
this.login.XXXX;
this.urlService.XXXX;

/* 推荐 */
// 流类型属性
private login$: Observable<StateBean>;
// 服务
private $urlService: UrlService;
```

##### 组件功能应该内聚

​	首先，我们来了解一下什么叫做前端组件？？

​	前端组件化开发，就是将页面的某一部分独立出来，将这一部分的 数据层（M）、视图层（V）和 控制层（C）用黑盒的形式全部封装到一个组件内，暴露出一些开箱即用的函数和属性供外部组件调用。

​	一个前端组件，包含了 HTML、CSS、JavaScript，包含了组件的模板、样式和交互等内容，基本上涵盖了组件的所有的内容，外部只要按照组件设定的属性、函数及事件处理等进行调用即可，完全不用考虑组件的内部实现逻辑，对外部来说，组件是一个完全的黑盒。

​	这里至少清楚了一点，一个组件是一界面部件显示、交互的集合，该组件内的交互操作都被完整的封装到该组件内部。这就是为什么我们需要将组件功能内聚的原因了，组件功能需内聚在自身内部，切勿将自身功能分散到别的组件。
> 例如：尽量减少`@ViewChild`的使用，多考虑`@Input`、`@Output`进行组件通信。

```typescript
/* 不推荐 */
// 某组件函数方法中将本组件无需考虑的操作涉及进来，实际这部分操作本组件无需考虑
public histoty() {
        this.loading = true;
        this.timers.forEach(item => {
            clearInterval(item);
        });
        this.timers = [];
        const me = this, id = this.addData.taskId;
  		// 下面这段代码无需考虑
        this.minuteMonitorService.getTaskName({id: id}).subscribe(res => {
            if (res.success) {
                this.loading = false;
                const getTaskName = res.data[0].taskName;
                const startTime = res.data[0].monitorStartDate;
                const endTime = res.data[0].monitorEndDate;
                const monitorPeriod = res.data[0].monitorPeriod;
                const monitorStatus = res.data[0].monitorStatus;
                // 跳转到历史页面
                this.$router.navigate(['/monitor/taskManage-monitor/detail'], {
                    queryParams: {
                        id: me.addData.taskId, objectId: me.addData.objectId, monitorStartDate: startTime,
                        monitorEndDate: endTime, monitorStatus: monitorStatus, taskName: getTaskName, monitorPeriod: monitorPeriod, checkTraffic: true
                    }
                });
            }
        });
}
// 完全使用@ViewChild进行组件通信
 @ViewChild('trendChart') trendChart: ChartComponent;
 @ViewChild('cycleChart') cycleChart: ChartComponent;
 @ViewChild('cycleGrid') cycleGrid: GridComponent;
 @ViewChild('trendGrid') trendGrid: GridComponent;
 @ViewChild('cycleDownloadEle') cycleDownloadEle: ElementRef;
 @ViewChild('trendDownloadEle') trendDownloadEle: ElementRef;
 @ViewChild('trendAnalysis') trendAnalysis: TabStripComponent;
 @ViewChild('trendGridMessages') trendGridMessages: CustomMessagesComponent;
 @ViewChild('cycleGridMessages') cycleGridMessages: CustomMessagesComponent;

/* 推荐 */
// common中有很多优秀的组件可以参考，例如mega-menu等
export class MegaMenuComponent implements OnInit {
    @Input() model: MenuItem[];

    @Input() style: any;

    @Input() styleClass: string;

    @Input() orientation: string = 'horizontal';

    @Input() autoZIndex: boolean = true;

    @Input() baseZIndex: number = 0;

    activeItem: any;

    hideTimeout: any;
    // XXX
}
```

##### 公共组件考虑扩展

​	一个组件在开发前，应先明确组件用途、是否需要抽取为公共组件等问题，而公共组件需要考虑的问题是已有的功能是否具备扩展性、是否需要考虑更前瞻性的功能等。
>  比如有一导出功能的组件，初期主要功能是能够导出4种格式报表（`csv`、`xlsx`、`html`、`pdf`），那么假如有个界面只需要导出三种格式呢？或者能够支持导出别的格式呢？或者导出的是图片不是报表呢？这就需要我们的组件能够具备一定扩展，也需要我们的开发人员能够具有一定的前瞻性。

```html
/* 不推荐 */
// 导出组件只固定导出4种格式报表文件
<span #formatPopup>
    <span>
        <span #anchor (click)="openPopup()"><i class="iconfont icon-style">&#xe607;</i></span>
    </span>
    <kendo-popup *ngIf="show" [anchor]="anchor" class="f-popup-content">
        <ul>
            <li (click)="setFileType(1)">csv</li>
            <li (click)="setFileType(2)">xlsx</li>
            <li (click)="setFileType(3)">pdf</li>
            <li (click)="setFileType(4)">html</li>
        </ul>
    </kendo-popup>
</span>

/* 推荐 */
// 具体导出列表是什么（方式、格式等）由具体使用该组件的父组件决定
<span #formatPopup>
    <span>
        <span #anchor (click)="openPopup()"><i class="iconfont icon-style">&#xe607;</i></span>
    </span>
    <kendo-popup *ngIf="show" [anchor]="anchor" class="f-popup-content">
        <ul>
            <li *ngFor="let item of items;let i of index" (click)="setFileType(i)">		       				{{item.type}}
      		</li>
        </ul>
    </kendo-popup>
</span>
```

##### 动态创建的组件在删除时应销毁该组件

​	对于需要动态生成的组件来说，内存与`DOM`的负载会随着动态组件的增加而增加，这就是为什么如果该动态组件删除或者不再使用时需要将其销毁的原因，如果一直保留在`DOM`中，会造成一定的性能问题，或者一定情况甚至出现异常问题。

```typescript
/* 不推荐 */
// 删除时未销毁该组件
public closeChart() {
        this.$alert.confirm(this._i18n_monitor.confirmCloseMonitor, () => {
            this.deleteTrafficImportanceMonitor(this.deleteObjectId);
            this.destoryFlag = false;
            clearInterval(this.timer1);
            clearInterval(this.timer2);
            // 这里只是移除DOM，实际组件依旧存在，钩子函数照常运行
            this.render.removeChild(this.render.parentNode(this.elementRef.nativeElement), this.elementRef.nativeElement);
            this.closeEvent.emit();
        }, () => {
            this.$alert.success(this._i18n_monitor.cancelSuccess);
        });
    };

/* 推荐 */
// 删除时销毁该组件
public closeChart() {
        this.$alert.confirm(this._i18n_monitor.confirmCloseMonitor, () => {
            this.deleteTrafficImportanceMonitor(this.deleteObjectId);
            this.destoryFlag = false;
            clearInterval(this.timer1);
            clearInterval(this.timer2);
          // 销毁DOM
      	  this.container.remove(this.componentRef)
            this.closeEvent.emit();
        }, () => {
            this.$alert.success(this._i18n_monitor.cancelSuccess);
        });
    };
```

##### construct中不放入初始化逻辑

​	在Angular组件类中，我们有几个默认的规范，构造函数中我们主要用来进行依赖注入，在`ngOnInit`钩子函数中进行属性的初始化。

```typescript
/* 不推荐 */
constructor(private $minuteMonitorService: MinuteMonitorService,
                private $router: Router,
                private $alert: AlertService,
                private $localService: LocalService,
                private $downService: DownloadFileService,
                private render: Renderer2,
                private elementRef: ElementRef) {
  this._i18n_timePickerSelector = this.$localService.i18n.timePickerSelector;
        this._i18n_monitor = this.$localService.i18n.monitor;
        this.listItems = [{text: this._i18n_monitor.exportImages, id: 0}, {text: this._i18n_monitor.exportReport, id: 1}];
}

/* 推荐 */
// 构造器中进行依赖注入
constructor(private $minuteMonitorService: MinuteMonitorService,
                private $router: Router,
                private $alert: AlertService,
                private $localService: LocalService,
                private $downService: DownloadFileService,
                private render: Renderer2,
                private elementRef: ElementRef) {
}
// ngOnInit进行属性初始化
ngOnInit() {
        this._i18n_timePickerSelector = this.$localService.i18n.timePickerSelector;
        this._i18n_monitor = this.$localService.i18n.monitor;
        this.listItems = [{text: this._i18n_monitor.exportImages, id: 0}, {text: this._i18n_monitor.exportReport, id: 1}];
}
```
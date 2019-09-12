## TS开发指南

本指南不是狭义的Typescript规范，而是基于TS的、专项目前使用的技术栈的规范合集。

#### 通用


##### 成员顺序

​	成员按照统一的顺序排列：

* 按照属性成员/构造函数/钩子函数（多个钩子函数按照出现顺序排序）/事件处理函数/其他方法成员的顺序排列成员顺序

* 按照@Input/@Output/@viewChild.../普通属性成员的顺序排列属性成员顺序

* 公共成员放在前面，私有成员放在后面

* 相关属性、方法归为一组，按照层级分层聚合

  ```typescript
  /* 不推荐*/
  export class demoComponent implements OnInit, OnDestroy, OnChanges {
   
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
    ...
    ngOnDestroy() {...}
    ngOnInit() {...}
    ngOnChanges() {...}
   
    private hide() {...}
    public exportFile() {...}
    public onSaveBtnClick() {...}
    public onCancelBtnClick() {...}
    private show() {...}
  }
  
  /* 推荐*/
  export class demoComponent implements OnInit {
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
   
    ngOnChanges() {...}
    ngOnInit() {...}
    ngOnDestroy() {...}
      
    public onSaveBtnClick() {...}
    public onCancelBtnClick() {...}
    public exportFile() {...}
    
    private hide() {...}
    private show() {...}
  }
  ```



##### 注释

* 配置模型需写明配置项职责、配置内容、默认值等

  ```typescript
  /* 不推荐*/
  export class Options {
      label: string;
      checked?: boolean;
      value: any;
      deletable?: boolean;
  }
   
  /* 推荐*/
  /**
   * 列选择器配置model
   * gridId: 表格唯一ID，使用表格报表导出接口URL连接作为key（需要定制列的表格都有“导出”需求，为    *	 了不再定义一套ID规则，所以使用导出接口URL）。
   * prefixSkip: 头部跳过的列；可不配置，默认为0，配置为1表示表格第一列不参与“自定义”，比如序号    *    	列、checkbox列等。
   * suffixSkip: 尾部跳过的列；可不配置，默认为0。配置为1表示表格最后一列不参与“自定义”，比如序    *		尾部操作列等。
   * defaultHidden:默认隐藏列；可不配置，默认全部显示，将需要隐藏的列field配置在数组中，在初次
   * 	 查询表格时会隐藏该列。
   */
  export interface ColumnSelectorOption {
      gridId: string;
      prefixSkip?: number;
      suffixSkip?: number;
      defaultHidden?: string[];
  }
  ```

* 方法成员需写明方法职责、入参类型及描述、返回类型及描述，必要时添加注意事项、调用场景等

  ```typescript
  /* 不推荐*/
  /**
   * mini图中x轴time的显示处理
  */
  xTime(data: any[]) {
      const length = data.length; // 96
      const indexArr = [0, Math.floor((length / 3) - 1), Math.floor(2 * (length / 3) - 1), length - 1]; // 找到需要显示的数据下标
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
  private reorder(grid: GridComponent, columnOption: ColumnSelectorOption, columnInfo: ColumnSelectorInfo) {
      const columns: QueryList<ColumnComponent | ColumnGroupComponent> = <QueryList<ColumnComponent | ColumnGroupComponent>>grid.columns;
      const prefix = columnOption.prefixSkip || 0;
      const showColumns = columnInfo.show;
      if (!_.isEmpty(showColumns)) {
          showColumns.forEach((showColumn, i) => {
              const column = this.getColumnByField(columns.toArray(), showColumn.value);
              if (column) {
                  /**
                   * 将隐藏列设置为显示列，且排序在原先显示列之前，该方法报错,在每次reorder之					* 前，通过如下方法避免报错:
                   * 1.按照上次列设置信息，将原先隐藏列排在原先显示列后，表格显示全量列信息
                   * 2.通过列的hidden属性控制本次设置为隐藏的列隐藏
                   */
                  grid.reorderColumn(column, i + prefix);
              }
          });
      }
  }
  ```

* 属性成员需添加注释

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
  // 搜索结果
  searchResult: TopoSearchResultItem[] = [];
  // 搜索加载提示
  searchLoading: boolean = false;
  // 可观察对象：ems状态
  private emsState$: Observable<EmsState>;
  ```

* 复杂逻辑代码需添加注释

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
          this.rowSelectedList = this.rowSelectedList.filter(row => row.lspId !== item.lspId);
          if (item.$children) {
              item.$children.forEach(value => {
                  value.$checked = item.$checked;
                  this.rowSelectedList = this.rowSelectedList.filter(row => row.lspId !== value.lspId);
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


##### 单个文件只写一个类

（某个类只在本文件使用除外）

```typescript
/* 不推荐*/
import { Component, NgModule, OnInit } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
 
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
export class AppModule {}
 
platformBrowserDynamic().bootstrapModule(AppModule);
 
const HEROES: Hero[] = [
  { id: 1, name: 'Bombasto' },
  { id: 2, name: 'Tornado' },
  { id: 3, name: 'Magneta' }
];
 
function getHeroes(): Promise<Hero[]> {
  return Promise.resolve(HEROES); // TODO: get hero data from the server;
}

/* 推荐*/
// main.ts
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule } from './app/app.module';

platformBrowserDynamic().bootstrapModule(AppModule);

// app/app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { RouterModule } from '@angular/router';
 
import { AppComponent } from './app.component';
import { HeroesComponent } from './heroes/heroes.component';
 
@NgModule({
  imports: [
    BrowserModule,
  ],
  declarations: [
    AppComponent,
    HeroesComponent
  ],
  exports: [ AppComponent ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }

// app/app.component.ts
import { Component } from '@angular/core';
import { HeroService } from './heroes';
 
@Component({
  selector: 'toh-app',
  template: `
      <toh-heroes></toh-heroes>
    `,
  styleUrls: ['./app.component.css'],
  providers: [HeroService]
})
export class AppComponent {}

// app/heroes/heroes.component.ts
import { Component, OnInit } from '@angular/core';
import { Hero, HeroService } from './shared';
 
@Component({
  selector: 'toh-heroes',
  template: `
      <pre>{{heroes | json}}</pre>
    `
})
export class HeroesComponent implements OnInit {
  heroes: Hero[] = [];
 
  constructor(private heroService: HeroService) {}
 
  ngOnInit() {
    this.heroService.getHeroes()
      .then(heroes => this.heroes = heroes);
  }
}

// app/heroes/shared/hero.service.ts
import { Injectable } from '@angular/core';
import { HEROES } from './mock-heroes';

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
import { Hero } from './hero.model';

export const HEROES: Hero[] = [
  { id: 1, name: 'Bombasto' },
  { id: 2, name: 'Tornado' },
  { id: 3, name: 'Magneta' }
];
```

##### 单个函数一般不超过200行

```typescript
/* 不推荐*/
public handleDataTypeChange(event) {
    this.selectedDataType = event;

    // 清空表格数据(10行)
    this.chartOptions.categories = [];
    this.chartOptions.series.splice(0, this.chartOptions.series.length);
    ...
    
    if (this.selectedObject.length === 0) {
        // 设置chart title(60行)
        ...
        this.chartOptions.title.text = title;
        this.chartOptions.title.detailText = detailTitle;
        ...
    } else {
        this.loadChartData(this.selectedObject, (data) => {
            // 预处理chart数据(100行)
            data.forEach(value => {
                this.selectedObject.forEach((item, index) => {
                    ...
                });
            });
            
            // 设置chart title(60行)
            ...
            this.chartOptions.title.text = title;
            this.chartOptions.title.detailText = detailTitle;
            ...
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
private clearChartData(): void {...}
/**
 * 设置chart title
 */
private setChartTitle(): void {...}
/**
 * 预处理chart数据
 */
private chartDataHandler(...): void {...}

```



##### 单个文件一般不超过1000行

```typescript
/* 不推荐*/
// 两个tab下分别有一个chart，在一个组件中一并处理，数据处理部分也写在组件中(导致一个组件内容冗长)
@Component({
    selector: 'traffic-add-tab',
    templateUrl: './add-tab.component.html',
    styleUrls: ['./add-tab.component.scss']
})
export class AddTabComponent implements OnInit, OnDestroy {
    ...
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
        ...
        
        if (this.chartFlag) {
            ...
            CommonUtil.exportChart(this.echart1, this.exportImgName);
        } else {
            ...
            CommonUtil.exportChart(this.echart2, this.exportImgName);
        }
      
        ...
    }
        
    /**
     * 获取曲线图数据
    */
    readChartData(): void {
        ...
        if (this.chartFlag) {
            this.processCondition1();
            this.readChartData1();
        } else {
            this.processCondition2();
            this.readChartData2();
        }
       	...
    }    
}

/* 推荐*/
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



##### 删除无效代码

* 空代码块、未使用到的依赖、成员、函数、导入模块需要删除

  ```typescript
  /* 不推荐*/
  /** 
   *  1. AfterViewInit import但未使用
   *  2. $LspOptionService注入了依赖但未使用
   *  3. targetNeOptionShow定义了属性但未使用
   */
  import {AfterViewInit, Component, OnInit} from '@angular/core';
  import {TransferSystemOptionService} from '../transfer-system-option/transfer-system-option.service';
  import {LspOptionService} from './lsp-option.service';
  
  @Component({
      selector: 'vapp-lsp-option',
      templateUrl: './lsp-option.component.html',
      styleUrls: ['./lsp-option.component.scss'],
      providers: [LspOptionService, TransferSystemOptionService]
  })
  export class LspOptionComponent implements OnInit {
    targetNeOptionShow: boolean = false;
  ...
    constructor(private $LspOptionService: LspOptionService) {}
  ...
    handleSelectedChange(list: any[]) {}
  ...
  }
  
  /* 推荐*/
  import {Component, OnInit} from '@angular/core';
  import {TransferSystemOptionService} from '../transfer-system-option/transfer-system-option.service';
  
  @Component({
      selector: 'vapp-lsp-option',
      templateUrl: './lsp-option.component.html',
      styleUrls: ['./lsp-option.component.scss'],
      providers: [TransferSystemOptionService]
  })
  export class LspOptionComponent implements OnInit {
  ...
    constructor() {}
  ...
  }
  ```

* 注释掉的无用代码需要删除
  （注释掉的有用代码需添加注释说明）

  ```typescript
  /* 不推荐*/
  ...
    // handleSelectedChange(list: any[]) {}
  ...
  
  /* 推荐*/
  ...
  ...
  ```

* 断点和控制台输出语句需要删除

  ```typescript
  /* 不推荐*/
  ...
     debugger;
     console.log(res.data);
  ...
  
  /* 推荐*/
  ...
  ...
  ```

##### 使用TODO

```typescript
/* 不推荐*/
// app.component.ts中存在如下代码方便开发时自动登录
this.$http.post('http://10.170.160.80:8080/common-service/auth/login', {userName: 'admin', password: 'admin'}).subscribe((result: any) => {
     this.sysConfigSrv.readSysConfig().subscribe(sysConfigResponse => {
         if (sysConfigResponse.success && _.isObject(sysConfigResponse)) {
             this.authSrv.setRole(result.data);
             this.sysConfigSrv.setAllSysConfig(sysConfigResponse.data);

         }
     });
 });

/* 推荐*/
/**
 *  TODO 开发时方便自动登录，上传svn时需删除
 */
...
```



##### 杜绝代码中直接出现中文词条

* html文件中

  ```html
  <!-- 不推荐 -->
  <div class="override left-button-group">
          <div class="vapp-button vapp-query-button" (click)="save()">
              保存
          </div>
  </div>
  
  <!-- 推荐 -->
  <div class="override left-button-group">
          <div class="vapp-button vapp-query-button" (click)="save()">
              {{i18n.autoInspect.save}}
          </div>
  </div>
  ```

* ts文件中

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



##### Ajax交互

###### 界面参数传递错误不能引起后台异常

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
    this.$service.queryViewData(this.queryObject).subscribe((res:ResultModel<Array>) => {
        ...
    });
}

/* 推荐*/
private getViewData() {
    // queryObject中各字段无值时不传向后台，后台自行判断保证不报错
    this.queryObject = {};
    this.$service.queryViewData(this.queryObject).subscribe((res:ResultModel<Array>)=>{
        ...
    });
}                                                                         
```

###### 后台返回data必须判空

```typescript
/* 不推荐*/
private getGridData() {
     this.$service.getGridData(this.condition).subscribe((res:ResultModel) => {
         if (res && res.success) {
             ...
             this.otherInfo = {
                 'alarmName': res.data[0].alarmName,
                 ...
             };
         }
     });
 }

/* 推荐*/
private getGridData() {
	this.$service.getGridData(this.condition).subscribe((res:ResultModel<Array>) => {
        if (res && res.success) {
            ...
            this.otherInfo = {...};
            if(_.isArray(res.data) && res.data.length > 0){
                this.otherInfo.alarmName = res.data[0].alarmName;
            }else{
                this.otherInfo.alarmName = '';                 
            }
          
         }
    });
}
```

###### ResultModel数据格式

​	后台返回的数据格式统一为ResultModel数据格式

```typescript
/* 不推荐*/
// 后台返回数据未使用ResultModel格式
private getGridData() {
    this.$service.getGridData(this.condition).subscribe((res) => {
        ...
        this.gridData = {
            data: res.result.items,
            total: res.result.totalCount
        };

    });
}

/* 推荐*/
// result.model.ts
export abstract class ResultModel<T = any> {
    /**
     * result-data with type T
     */
    data: T;

    /**
     * error code
     */
    errorCode: string;

    /**
     * error level
     */
    errorLevel: string;

    /**
     * error msg
     */
    errorMsg: string;

    /**
     * is result success
     */
    success: boolean;

}
                                  
// system-config.service.ts
import {ResultModel} from '../../model/result.model';

readSysConfig(): Observable<ResultModel<Array>> {
    const url = this.urlSrv.getURL(URL_CONST.getSysConfig);
    return this.http.get<ResultModel<any>>(url);
}

// login.component.ts
private login(): void {
    ...
    // 登录后获取系统配置信息
    this.sysConfigSrv.readSysConfig().subscribe((res:ResultModel<Array>) => {...}
    ...
};
```

###### 前端拦截器统一处理 success为false时的弹框提示

```typescript
/* 不推荐*/
private getGridData() {
    this.$service.getGridData(this.condition).subscribe((res:ResultModel<Array>) => {
        if (res && res.success) {
            ...
        }else{
            this.$alertService.error(res.errorMsg);
        }
    });
}
                                                        
/* 推荐*/
private getGridData() {
    this.$service.getGridData(this.condition).subscribe((res:ResultModel<Array>) => {
        if (res && res.success) {
            ...
        }
        // else 分支由拦截器统一处理显示弹框
    });
}
```

###### 避免同一接口多次请求

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
        ...
        i++;
    }

    Promise.all(allChartDataPromises).then(data => {...});

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
 
    this.loadChartData(objects, (data) => {...});
}
```

###### 避免异步回调函数中再进行异步请求

```typescript
/* 不推荐*/
public reset(): void {
    this.$service.reset().subscribe((result:ResultModel<any>) => {
        if (result && result.success) {
            // 重置成功后保存数据
            this.$service.saveData(this.neInspectItem).subscribe((res:ResultModel<any>) => {
                if (res && res.success) {
                    this.alert.success(this.i18n.autoInspect.saveSuccess);
                    ...
                }
            });
        }
    });
}
        
/* 推荐*/
// 后台修改接口，避免调用两次请求
public reset(): void {
    this.$service.reset().subscribe((result:ResultModel<Array>) => {
        if (result && result.success) {
            ...
        }
    });
}
```



##### 使用枚举代替常量

```typescript
/* 不推荐*/
private _applyFilter(value: string, operator: string) {
    const operation = this.switchToEn(operator);
    this.applyFilter(
        value === null ? 
        this.removeFilter(this.valueField) : 
        this.updateFilter({
            field: this.valueField,
            operator: operation ? operation : 'eq',//直接使用常量
            value: value
        })
    );
}

/* 推荐*/
...
import {Operator} from '../../enum/operator.enum';
...

@Component({
    selector: 'vapp-grid-date-filter-cell',
    templateUrl: './grid-date-filter-cell.component.html',
    styleUrls: ['./grid-date-filter-cell.component.scss']
})
export class GridDateFilterCellComponent extends BaseFilterCellComponent implements OnInit, AfterViewInit, OnChanges {
    ...
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
	...
}

```



##### 使用枚举定义颜色

```typescript
/* 不推荐*/
public compareChartOption = {
    title: {
        text: '   ',
        font: 'bold 14px Microsoft YaHei',
        color: 'rgb(49, 65, 89)',// 直接定义色值
        margin: {
            bottom: 20
        }
    },
    ...
};

/* 推荐*/
import {ChartColorEnum} from '../../enum/colors.enum';
...
public compareChartOption = {
    title: {
        text: '   ',
        font: 'bold 14px Microsoft YaHei',
        color: ChartColorEnum.Title,
        margin: {
            bottom: 20
        }
    },
    ...
};
```



##### 使用常量代替魔法数字

```typescript
/* 不推荐*/
// 表格导出
public exportExcel() {
    ...
    this.exportTimer = setTimeout(() => {...}, 8000);
}

/* 推荐*/
public exportExcel() {
    ...
    this.exportTimer = setTimeout(() => {...}, appConst.exportTimeout);// 八秒超时
}                                  
```



##### 使用计算工具类处理加减乘除

```typescript
/* 不推荐*/
// 数值输入框失去焦点处理
onNumTextBlur(): void {
    // 获取拓扑图中心点位置
    const coord = this.graph.viewportBounds.center;
    this.graph.centerTo(coord.x, coord.y, this.currentScale/100));
}

/* 推荐*/
import {CalculateUtil} from '@vapp/common2';

public onNumTextBlur(): void {
    ...
    this.graph.centerTo(coord.x, coord.y, CalculateUtil.accDiv(this.currentScale, 100));
}

```



##### 使用日期工具类处理日期时间计算

```typescript
/* 不推荐*/
cloneOrModifyTask(task: Task, isClone: boolean): Task {
    let newTask;

    // 克隆时taskName根据算法得到
    if (isClone) {
        // 克隆任务只克隆任务基本必要属性，避免多余属性传给后台造成错误
        newTask = {
            activeTime: task.activeTime,
            ...
        };
        ...
    }
        ...

        // 将后台string类型的时间转为Date类型
        if (newTask.activeTime) {
            newTask.activeTime = new Date(newTask.activeTime);
        }

        return newTask;
}
    
/* 推荐*/
// date-util.ts
export class DateUtil {
    ...
    // 解决new Date() 在IE、Firefox上的兼容性问题
    public static newDate(strDate: String): Date {
        return new Date(strDate.replace('-', '/'));
    }
}
// task-edit.service.ts
import {DateUtil} from '@vapp/common2';

private cloneOrModifyTask(task: Task, isClone: boolean): Task {
    let newTask;

    // 克隆时taskName根据算法得到
    if (isClone) {
        // 克隆任务只克隆任务基本必要属性，避免多余属性传给后台造成错误
        newTask = {
            activeTime: task.activeTime,
            ...
        };
        ...
    }
        ...
        // 将后台string类型的时间转为Date类型
        if (newTask.activeTime) {
            newTask.activeTime = DateUtil.newDate(newTask.activeTime);
        }

        return newTask;
}
```



##### if-else分支大于三个时使用switch-case

```typescript
/* 不推荐*/
...
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
...

/* 推荐*/
...
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



##### 使用easyMock模拟数据

```typescript
/* 不推荐*/
// heroes.service.ts
...
import { Hero } from './hero';
import { HEROES } from './mock-heroes';
 
@Injectable()
export class HeroService {
 
  constructor() { }
 
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
 
  constructor(private $http: HttpClient, private $urlService: UrlService) { }
 
  getHeroes(): Observable<Hero[]> {
    // 通过真实请求获取模拟数据
    const url = this.$urlService.getURL(URL_CONST.getHeroes);
    return this.$http.get<ResultModel<Array>>(url);
  }
}
```




#### Typescript

##### let和const

​	变量必须使用let声明，常量必须使用const声明

```typescript
/* 不推荐*/
public checkGroupGridData(originalData: any[], currentData: any[], checkFields: string[]): CheckSettingResult {
    validationResult: CheckSettingResult = {...};
    currentData.forEach((group, groupIndex) => {
        group.items.forEach((item, itemIndex) => {
            let itemCopy = originalData[groupIndex].items[itemIndex];
            let result: CheckDataItemResult = this.checkSettingItem(item, itemCopy, checkFields);
            ...
            if (validationResult.valid && !result.equal) {
                let changedItem = _.cloneDeep(item);
                ...
            }
        });
    });
    return validationResult;
}

/* 推荐*/
public checkGroupGridData(originalData: any[], currentData: any[], checkFields: string[]): CheckSettingResult {
    const validationResult: CheckSettingResult = { ... };
    currentData.forEach((group, groupIndex) => {
        group.items.forEach((item, itemIndex) => {
            const itemCopy = originalData[groupIndex].items[itemIndex];
            const result: CheckDataItemResult = this.checkSettingItem(item, itemCopy, checkFields);
            ...
            if (validationResult.valid && !result.equal) {
                const changedItem = _.cloneDeep(item);
                ...
            }
        });
    });
    return validationResult;
}
```



##### 始终使用类型

```typescript
/* 不推荐*/
public monitorData: any;
public performance;

/* 推荐*/
public monitorData: monitorModel;
public performance：number;
```



##### 能使用联合类型就不使用any

```typescript
/* 不推荐*/
getColumnByField(columns: any[], field: string): any {
    let column: any;

    for (let i = 0; i < columns.length; i++) {
        if (columns[i].isColumnGroup) {
            ...
        } else {
            if (columns[i].field === field) {
                ...
            }
        }
    }
    return column;
}

/* 推荐*/
private getColumnByField(columns: (ColumnComponent | ColumnGroupComponent)[], field: string): ColumnComponent | ColumnGroupComponent {
    let column: ColumnComponent | ColumnGroupComponent;

    for (let i = 0; i < columns.length; i++) {
        if (columns[i].isColumnGroup) {
            ...
        } else {
            if ((<ColumnComponent>columns[i]).field === field) {
                ...
            }
        }
    }
    return column;
}
```



##### 严格使用访问修饰符定义属性/方法

（public统一显示声明，不使用default形式）

```typescript
/* 不推荐*/
// 是否显示加载动画
loading: boolean = false;
// 内部标志位
flag ：boolean = false;
// 是否显示弹窗
private show: boolean = false;

onTabSelect(): void {...}
processData(data:DataModel):NewDataModel{...}


/* 推荐*/
// 默认为public
public loading: boolean = false;
// 默认为public
public show: boolean = false;
// 显示声明为private
private flag ：boolean = false;

// 默认为public
public onTabSelect(): void {...}
// 仅在内部使用的方法需定义为private
private processData(data:DataModel):NewDataModel{...}
```



##### 必须定义方法入参类型

```typescript
/* 不推荐*/
public transfromData2Model(data): NeStatusModel {
    const model: NeStatusModel = <NeStatusModel>{};
    for (let i = 0, len = data.length; i < len; i++) {
        const item = data[i];
        ...
    }
    return model;
}

/* 推荐*/
public transfromData2Model(data: Array<InspectItem>): NeStatusModel {
    const model: NeStatusModel = <NeStatusModel>{};
    for (let i = 0, len = data.length; i < len; i++) {
        const item = data[i];
        ...
    }
    return model;
}
```



##### 必须定义方法返回值类型

```typescript
/* 不推荐*/
fetchConvertData(viewData: any[], parent: any) {
    if (viewData) {
        return viewData.map(item => {
            return {
                isCache: false,
                $checked: false,
                $expanded: false,
                node: item.node,
                type: item.type,
                nodeName: item.nodeName || '--',
                items: item.items,
                parent: parent,
                $id: CommonUtil.getUUID(),
                nodeId: item.nodeId
            };
        });
    }
}

/* 推荐*/
public analysisData2Model(params: Array<InspectItem>): Array<InspectItem> {
    for (let i = 0, len = params.length; i < len; i++) {
        const contentObj = JSON.parse(params[i].content);
        params[i] = Object.assign(params[i], contentObj);
    }
    return params;
}
```




##### 方法体有多行代码禁止使用箭头函数声明

```typescript
/* 不推荐*/
private checkGridData = (dataCopy, data) => {
    const changedItems = [];
    let valid = true;
    _.forEach(data, (group, groupIndex) => {
        _.forEach(group.items, (item, itemIndex) => {
            const itemCopy = dataCopy[groupIndex].items[itemIndex];
            const result = this.checkSettingItem(item, itemCopy);
            valid = valid && result.valid;
            if (valid && !result.equal) {
                changedItems.push(item);
            }
        });
    });
    return {changedItems, valid};
}

/* 推荐*/
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



##### 使用模板字符串

```typescript
/* 不推荐*/
let a = (charLength + 2) + 'em';

/* 推荐*/
let a = `${charLength + 2}em`;
```



#### RxJS

​	`RxJS`是一个库，它通过使用observable序列来编写异步和基于事件的程序。
> 目前前端`RxJs`使用文档请参考：http://10.78.13.218/svn/webas2/专项管理/知识管理/12月提交/响应式编程RxJS（周婷）

  ​目前，前端项目中最常使用到`RxJS`的地方有两处，分别是`http`请求和`Redux`状态管理。

  ​1、 `http`请求

  ​`Angular`在封装`HttpClient`时已将`RxJs`使用在其中完成异步处理，所有请求类型均返回`Observable`对象

```typescript
/* 不推荐 */
getTasks(focus?: boolean) {
    const url = this.$urlService.getURL(URL_CONST.autoInspect.queryTasks);
    return this.http.post(url, {focus: focus});
}

/* 推荐 */
public getTasks(focus?: boolean): Observable<ResultModel> {
        const url = this.$urlService.getURL(URL_CONST.autoInspect.queryTasks);
        return this.http.post(url, {focus: focus});
}
// 数据请求处理：使用subscribe函数订阅消息(ResultModel 为common2定义的model)
this.$taskManageSrv.getTasks().subscribe((res: ResultModel) => {
            if (res && res.success) {
                this.loading = false;
                this.gridData = {
                    data: _.cloneDeep(<Task[]>res.data),
                    total: (<Task[]>res.data).length
                };
            }
});
```

​	2、`Redux`状态管理

​	相关`Redux`状态管理使用、编码规范、状态定义已有专题文档，请前往浏览，SVN地址：http://10.78.13.218/svn/webas2/专项管理/知识共享/Web前端/redux。	

#### Angular

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

#### Kendo UI
##### dataStatusChange事件统一处理分页、过滤、排序

​	大多数情况下，目前前端报表主要是后端分页、过滤、排序，故三者若在后台处理时实际处理函数应该是一个，这就意味着三者处理都可直接使用`dataStatusChange`一个事件代替`filterChange`、`sortChange`、`pageChange`使用。

​	而如果表格不是后端进行分页、过滤、排序呢？这个也就要分场景考虑。

* 如果业务不需要单独区分分页、过滤、排序操作，那么我们可以统一到`dataStatusChange`处理
* 如果确实单独处理不同业务需求情况，需要用上述三者中的事件单独处理，因为`dataStatusChange`事件是无法区分这三者类型的

```typescript
/* 不推荐 */
// 后台处理，却区分处理
// 表格 排序事件
public handleSortChange($event) {
    this.getGridData();
}
// 表格 过滤事件
public handleFilterChange($event) {
   this.getGridData();
}
// 表格 分页事件
public handlePageChange($event) {
  this.getGridData();
}

/* 推荐 */
// 前端处理三者，无分开处理业务需求
public dataStateChange(e: DataStateChangeEvent) {
        if (e.filter) {
            this.condition.filter.filters = e.filter.filters;
        } else {
            this.condition.filter.filters = [];
        }

        if (e.sort) {
            this.condition.sort = e.sort;
        } else {
            this.condition.sort = [];
        }
        this.pageSize = e.take;
        this.skip = e.skip;
        this.condition = {
            filter: this.$taskManageSrv.convert2UTCTime(this.condition.filter, ['lastTime', 'createTime']),
            sort: this.condition.sort,
            skip: this.skip,
            take: this.pageSize
        };
        this.gridData = this.$taskManageSrv.pageFilterHandler(this.originalGridData.data, this.condition);
}
```

##### 使用MessageService进行国际化处理

​	由于`kendo UI`有存在国际化的问题前提，故目前架构中，我们将国际化自行封装为`KendoMessageService`服务来代替`kendo`自身的`MessageService`服务，今后使用到`kendo`组件考虑国际化问题时就需要使用该服务来进行。

```typescript
/* 不推荐 */
<ng-template kendoDropDownListNoDataTemplate>
       <span>{{i18n.common.noRecords}}</span>
</ng-template>

/* 推荐 */
// module中使用kendoMessageService替换MessageService
providers: [
        /* 提供kendo国际化服务 */
        {
            provide: MessageService,
            useClass: KendoMessageService
        }
]
// 模板中使用，除kendo UI DropDowns系列组件时需配置如下国际化，其余组件无需再配置
<ng-template kendoDropDownListNoDataTemplate>
         <span>{{i18n.common.noRecords}}</span>
</ng-template>

```

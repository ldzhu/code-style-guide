* 为指令添加自定义前缀

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
  
  
  * 事件处理方法以on开头
  
    ```typescript
    /* 不推荐*/
    public modify() {···}
    
    /* 推荐*/
    public onModifyBtnClick() {···}
    ```
## 通用格式化规则


### 缩进


每次缩进使用四个空格。

不使用TAB键或者混合使用TAB键和空格进行缩进。

```html
<ul>
    <li>Fantastic
    <li>Great
</ul>
```
  
```css
.example {
    color: blue;
}
```

```typescript
export class ExampleComponent implements OnInit {

    constructor() {
    }

    ngOnInit() {
    }

}
```


### 尾部的空格


删除尾部的空格。

尾部的空格是多余的，不删除则形成无意义的文件差异。

```html
<!-- 不推荐 -->
<p>What?_ 
  
<!-- 推荐 -->
<p>Yes please.
```

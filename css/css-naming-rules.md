## 命名



###  使用小写字母和破折号命名

仅使用小写字母和`-`命名。

选择器中的词语和缩写中不要使用除了连字符以外的任何字符（包括空字符），以提高可理解性和可读性。

不要使用驼峰、下换线、$符号。

```css
/* 不推荐 */
.LSP_searchName {
}
.advancedQuery {
}
.statementadd {
}
#highCheck {
}
.net_search {
}

/* 推荐 */
.kendo-grid-container {
}
.ne-select-content {
}
.ads-sample {}
```


### 命名尽可能简短


ID和class命名要尽可能简短，但必要的话就别怕长。

尽可能简洁地传达id或者class名称的含义。

使用简洁的id或者class名称有助于提高可读性和代码效率。

```css
/* 不推荐：应该使用通用的缩写 */
#navigation {}
.information {}

/* 不推荐：缩写不能表达含义 */
.au {}

/* 推荐：使用通用缩写 */
#nav {}
.info {}

/* 推荐：准确表达含义 */
.author {}
```


### 使用"$+驼峰"命名scss变量

使用$前缀命名scss的变量。

```scss
/* 不推荐 */
fontSize: 12px;

/* 推荐 */
$cTabviewBorder: #a1a1a1;
$cTabviewItem: #949494;
$cTabviewItemTitle: #333333;
$cTabviewItemHover: $blue;
$cTabviewItemSelected: $blue;
```


### 使用小写字母和破折号命名mixin

使用小写字母和破折号命名mixin，使用时就像引用普通的css属性一样。

```scss
/* 不推荐 */
@mixin NormalFont { 
    font: normal $sFontNormal $sFontFamily;
    color: #666;
}

/* 推荐 */
@mixin background-default {
    background-color: $cBackground;
}
```


### 语义话的名称


使用有意义的或者通用的id和class名称

用能反映出元素目的或者通用的id、class名称，代替那些很表象的、难懂的名称。

如果名称需要是易懂的，或不容易被修改，应该首选特定的或者能反映出元素目的的名称。

使用功能性或者通用的名称，可减少不必要的文档或者模板变化。

```scss
/* 不推荐：无意义 */
.yee-1901 {
}

/* 不推荐：表象 */
.button-green {}


/* 推荐：具体的 */
.login {}
.video {}
.btn-group {
}
.btn-cancel {
}

/* 推荐：通用的 */
.nav {}
.alt {}
```

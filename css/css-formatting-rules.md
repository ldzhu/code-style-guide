## CSS格式化规则



### 声明顺序

相关的属性声明应当归为一组。由于定位（positioning）可以从正常的文档流中移除元素，并且还能覆盖盒模型（box model）相关的样式，因此排在
首位。盒模型排在第二位，因为它决定了组件的尺寸和位置。其他属性只是影响组件的内部（inside）或者是不影响前两组属性，因此排在后面。

通俗的讲，定位决定“位置”；盒模型决定“大小”；其他属性决定“长相”。所以按照重要程度来排列顺序。

第一梯队属性：`display, position, left, top, right, overflow, float, clear etc.` 

第二梯队属性：`margin, padding, border etc.`

第三梯队属性：`background, font, text etc.`


```scss
/* 不推荐 */
.example {
    font-family: 'Arial', sans-serif;
    border: 3px solid #dddddd;
    left: 30%;
    position: absolute;
    text-transform: uppercase;
    background-color: #eeeeee;
    right: 30%;
    display: block;
    font-size: 1.5rem;
    overflow: hidden;
    padding: 1em;
    margin: 1em;
}

/* 推荐 */
.example {
    display: block;
    position: absolute;
    left: 30%;
    right: 30%;
    overflow: hidden;
    margin: 1em;
    padding: 1em;
    border: 3px solid #dddddd;
    background-color: #eeeeee;
    font-family: 'Arial', sans-serif;
    font-size: 1.5rem;
    text-transform: uppercase;
}
```


### 使用SCSS嵌套


使用SCSS的语法来写SCSS，而不是CSS。

```scss
/* 不推荐 */
.ant-steps-vertical .ant-steps-item {
    margin-bottom: 2rem;
    padding: 0;
}

.ant-steps-vertical .ant-steps-item .ant-steps-item-tail {
    top: 100%;
    padding: 0 10px;
}

/* 推荐 */
.ant-steps-vertical {

    .ant-steps-item {
        margin-bottom: 2rem;
        padding: 0;

        .ant-steps-item-tail {
            top: 100%;
            padding: 0 10px;
        }
    }
}
```


### 块内容的缩进


缩进块内容。

将包括嵌套及声明的[块内容](http://www.w3.org/TR/CSS21/syndata.html#block) 进行缩进，以体现层次并提高可读性。

```css
@media screen, projection {
    html {
      background: #fff;
      color: #444;
    }
}
```


### 声明结束


以分号结束每个属性，提高一致性和可扩展性。

每个声明换行。

```css
/* 不推荐 */
.test {
    display: block; height: 100px
}

/* 推荐 */
.test {
    display: block;
    height: 100px;
}
```


### 属性名结束


属性名称的冒号后有一个空格。

为保证一致性，在属性名与属性值之间添加一个空格（但是属性名和冒号间没有空格）。

```css
/* 不推荐 */
h3 {
    font-weight:bold;
}

/* 推荐 */
h3 {
    font-weight: bold;
}
```


### 声明块间隔


在选择器和后面的声明块之间使用一个空格。

最后一个选择器与表示[声名块](http://www.w3.org/TR/CSS21/syndata.html#rule-sets) 开始的左大花括号在同行，中间有一个字符空格。
   
表示开始的左大花括号和选择器在同行。

```css
/* 不推荐：缺少空间 */
#video{
    margin-top: 1em;
}


/* 不推荐：不必要的换行符 */
#video
{
    margin-top: 1em;
}

/* 推荐 */
#video {
    margin-top: 1em;
}
```


### 选择器和声明分离


每个选择器和属性声明总是使用新的一行。用于明显区分 祖先->后代选择器。

```scss
/* 不推荐 */
.k-widget, .k-block, .k-panel {
    background: #fefbfe;
}

/* 推荐 */
.k-widget,
.k-block,
.k-panel {
    background: #fefbfe;
}
```


### 代码块分离


使用新空行分离规则。

始终把一个空行（换行符）放在代码块规则之间。

```css
/* 不推荐 */
html {
    background: #fff;
}
body {
    margin: auto;
    width: 50%;
}
.k-panel {
    background: #fefbfe;
}

/* 推荐 */
html {
    background: #fff;
}

body {
    margin: auto;
    width: 50%;
}

.k-panel {
    background: #fefbfe;
}
```


### 分段规则


组的分段由一段注释完成（可选）。

尽可能地用注释来将css分段，段与段之间采用新行。

```css
/* Header */
#adw-header {}

/* Footer */
#adw-footer {}

/* Gallery */
.adw-gallery {}
```


### 引号


属性选择器和属性值中使用单引号。
   
在属性选择器及属性值中使用单引号（''）而不是双引号（""）。在 `url（）` 中不要使用引号。

特例：如果你确实需要定义 ``@charset`` ，由于 [`不允许使用单引号](http://www.w3.org/TR/CSS21/syndata.html#charset) ，故请使用
双引号。

```css
/* 不推荐 */
@import url("//www.google.com/css/maia.css");

html {
    font-family: "open sans", arial, sans-serif;
}

/* 推荐 */
@import url(//www.google.com/css/maia.css);

html {
    font-family: 'open sans', arial, sans-serif;
}
```


### 使用块注释


建议使用块注释代替行注释。建议注释独占一行。避免行末注释。

```scss
/* 不推荐：行末注释 */
@mixin H1Title { // 一级标题 15px
    font: bold $sFontBigTitle $sFontFamily;
    color: #000;
}

/* 不推荐：行注释 */
// loading加载动画颜色
$cLoading: #ff6757;

/* 推荐 */
/* loading加载动画颜色 */
$cLoading: #ff6757;
```

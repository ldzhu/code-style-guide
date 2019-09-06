## CSS风格规则



### CSS有效性


尽可能使用有效的CSS。

使用有效的CSS代码，除非在处理css验证器bug或者是专有的语法时。

使用诸如[W3C CSS validator](http://jigsaw.w3.org/css-validator/) 等工具验证测试。

使用有效的CSS代码是一个可衡量CSS代码质量的指标，可帮你找出不起作用可被删除的CSS代码，从而确保CSS的合理使用。


### 避免使用ID


一般情况下ID不应该被应用于样式。

ID的样式不能被复用并且每个页面中你只能使用一次ID。

使用ID唯一有效的是确定网页或整个站点中的位置。

尽管如此，你应该始终考虑使用class，而不是id，除非只使用一次。


### 避免元素(标签)名


当构建选择器时应该使用清晰， 准确和有语义的class(类)名。不要使用标签选择器。 如果你只关心你的class(类)名，而不是你的代码元素，这样会更容易维护。

从分离的角度考虑,在表现层中不应该分配html标记/语义。它可能是一个有序列表需要被改成一个无序列表，或者一个h2将被转换成h3。

如果你只使用具有实际意义的class(类)名，并且不使用元素选择器，那么你只需要改变你的html标记，而不用改动你的CSS。

```scss
/* 不推荐 */
div.content > header.content-header > h2.title {
  font-size: 2em;
}

/* 推荐 */
.content > .content-header > .title {
  font-size: 2em;
}
```
  

### 简写属性


尽可能使用简写的属性书写方式。

CSS提供了多种属性 [简写](http://www.w3.org/TR/CSS21/about.html#shorthand>) 的方式（如 `font` ），即使只显式设置一个值，也应该
尽可能地使用。

使用简写属性有助于提高代码效率及可读性。

```css
/* 不推荐 */
.example {
    border-top-style: none;
    font-family: palatino, georgia, serif;
    font-size: 100%;
    line-height: 1.6;
    padding-bottom: 2em;
    padding-left: 1em;
    padding-right: 1em;
    padding-top: 0;
}

/* 推荐 */
.example {
    border-top: 0;
    font: 100%/1.6 palatino, georgia, serif;
    padding: 0 1em 2em;
}
```


### 0与单位


省略“0”后的单位。

除非必需，否则0后不要加单位。

```css
.example {
    margin: 0;
    padding: 0;
}
```


### 前导0


省略前导“0”值。

在-1至1之间的值无需保留整数位的0。

```css
.example {
    /* 不推荐 */
    font-size: 0.8em;
    
    /* 推荐 */
    font-size: .8em;
}
```


### 使用em,rem代替px


使用em, rem单位代替px。

```scss
.example {
    /* 不推荐 */
    font-size: 16px;
    
    /* 推荐 */
    font-size: 1em;
}
```


### 十六进制表示法


使用6个字符的十六进制表示法。

十六进制值应该全部小写，例如，#ffffff。在扫描文档时，小写字符易于分辨，因为他们的形式更易于区分。

```css
.example {
    /* 不推荐：使用大写字符 */
    background: #F47A28;
    
    /* 不推荐：使用rgb表示颜色 */
    color:rgb(255, 255, 255);
    
    /* 不推荐：使用3个字符表示 */
    color: #ebc;
    
    /* 推荐 */
    color: #eebbcc;
}
```


### 前缀选择器


加特定应用前缀

大型项目中以及嵌入在其它项目或外部网站上的代码需要给id和class添加前缀（命名空间）。使用短的、独特的标识符，并在其后跟一个破折号。
使用命名空间有助于防止命名冲突，可以让维护变得简单，例如在搜索和替换操作时。

```css
.ant-form-item {}
.ant-form-item-label {}
.nop-portal-desktop {}
```


### 尽可能精确


尽可能精确使用选择器，避免无心d的选择范围扩大化。

```html
<article class="content news-content">
    <span class="title">News event</span>
    <div class="content-body">
        <div class="title content-title">
            Check this out
        </div>
     
        <p>This is a news article content</p>
     
        <div class="teaser">
            <div class="title">Buy this</div>
            <div class="teaser-content">Yey!</div>
        </div>
    </div>
</article>
```

```scss
/* 不推荐：祖先->后代选择器，选择的范围太大 */
.content .title {
  font-size: 2rem;
}

.content-body .title {
  font-size: 1rem;
}

/* 推荐 */
.content > .title {
  font-size: 2rem;
}
```

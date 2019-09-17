## 通用风格规则


### 协议


尽可能使用HTTPS协议引用嵌入式资源。

除非相应的文件不能通过HTTPS使用，否则请始终对图片和其他媒体文件，样式表和脚本使用HTTPS（`https :`)。

```html
<!-- 不推荐： 省略了协议 -->
<script src="//ajax.googleapis.com/ajax/libs/jquery/3.4.0/jquery.min.js"></script>
<!-- 不推荐： 使用HTTP协议 -->
<script src="http://ajax.googleapis.com/ajax/libs/jquery/3.4.0/jquery.min.js"></script>

<!-- 推荐 -->
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.0/jquery.min.js"></script>
```

  
```css
<!-- 不推荐： 省略了协议 -->
@import '//fonts.googleapis.com/css?family=Open+Sans';
<!-- 不推荐： 使用HTTP协议 -->
@import 'http://fonts.googleapis.com/css?family=Open+Sans';

<!-- 推荐 -->
@import 'https://fonts.googleapis.com/css?family=Open+Sans';
```

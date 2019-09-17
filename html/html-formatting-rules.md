## HTML格式规则


### 常规格式化


对每个块级元素、列表元素、表格元素都另起一行，每个子元素都缩进。

每个块级元素、列表元素或表格元素另起一行，而不必考虑元素的样式（因CSS可以改变元素的 `display` 属性）。

同样的，如果他们是块、列表或者表格元素的子元素，则将之缩进。

```html
<blockquote>
    <p><em>Space</em>, the final frontier.</p>
</blockquote>
  
<ul>
    <li>Moe</li>
    <li>Larry</li>
    <li>Curly</li>
</ul>
  
<table>
    <thead>
        <tr>
            <th scope="col">Income</th>
            <th scope="col">Taxes</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>$ 5.00</td>
            <td>$ 4.50</td>
        </tr>
    </tbody>
</table>
```


### 大小写


只使用小写字母。

所有的代码都使用小写字母：适用于HTML元素、属性、属性值（除了`text/CDATA`）。

```html
<!-- 不推荐 -->
<A HREF="/">Home</A>
  
<!-- 推荐 -->
<img src="google.png" alt="Google">
```


### HTML引号


当引用属性值时，使用双引号。

使用双引号而不是单引号来包裹属性值。

```html
<!-- 不推荐 -->
<a class='maia-button maia-button-secondary'>Sign in</a>
  
<!-- 推荐 -->
<a class="maia-button maia-button-secondary">Sign in</a>
```

### 列限制


每行长度不能超过120个字符。

### 行限制


每个文件不能超过1000行。

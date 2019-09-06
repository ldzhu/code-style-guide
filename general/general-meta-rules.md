## 通用元数据规则


### 编码


使用UTF-8无BOM编码。

确保你的编辑器使用无BOM的UTF-8编码。

在HTML模板和文档中使用 `<meta charset="utf-8">` 指定编码。

不需要为CSS文件指定编码，它默认是UTF-8。

> 想了解更多关于应该何时并如何指定编码，请查看[Handling character encodings in HTML and CSS](http://www.w3.org/International/tutorials/tutorial-char-enc/)


### 注释


在需要时尽可能去解释你的代码。

用注释去解释你的代码，包括它的应用范围、用途、此方案的选择理由等。

> 注释会增重代码，过度注释会让人无所适从，优秀的代码甚至不需要注释就能很好的理解。没必要为每个文件写上详细的注释，是否需要注释主要取决于代码的复杂度。


### TODO


用TODO标记待办事宜和处理内容。

只用TODO来高亮标记待办事项，不要使用其他格式，例如@@。

在括号里添加联系方式（姓名或邮箱），格式为`TODO（联系方式）`。

在冒号后面添加处理内容，格式为`TODO：处理内容`。

```html
<!-- TODO(ldzhu): center元素已废弃，使用css重新处理居中 -->
<center>Test</center>
```

```typescript
// TODO(ldzhu@fiberhome.com): 算法有bug，未考虑除数为0的情况
divide: number = (x: number, y: number) => {return x / y}
```  

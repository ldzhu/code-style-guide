## 附录


###### 声明变量尽可能地靠近第一次使用的地方

​	尽管`var`声明的作用域从函数左括号开始，为了可读性，`var`声明仍需尽量靠近第一次使用的地方。但是，如果变量在块外被引用，不要在块内声明这个变量。例如：

```javascript
function sillyFunction() {
  var count = 0;
  for (var x in y) {
    // "count" could be declared here, but don't do that.
    count++;
  }
  console.log(count + ' items in y');
}
```

##### 不要在块作用域内进行函数声明

不要像下面这样：

```javascript
if (x) {
  function foo() {}
}
```

​	虽然大多数JS编译器在ES6之前已支持块内函数声明，但这并不规范。在实现上彼此不一致，和现在标准的ES6块内声明函数表现也不一致。ES5及之前版本只允许在脚本根部进行函数声明，在严格模式下明确禁止函数声明出现在块作用域内。

​	为了保持一致性，用函数表达式初始化的方式替代块内函数声明。

```javascript
if (x) {
  var foo = function() {};
}
```

## CSS元规则



### 避免在Html style中声明样式


遵守[关注点分离](../html/html-style-rules.md#关注点分离)规则，不在html中直接写样式。

```html
<!-- 不推荐：在html中写style -->
<div style="font-size: 0; border: 1px solid red;">
```


#### 使用SCSS


优先使用scss，而不是css编码。


### 使用iconfont


优先使用iconfont字体图标，而不是使用.png .svg 等图片。


#### Mixins与函数


多使用Mixins及函数处理通用样式规则，避免代码堆叠。

```scss
/* 不推荐 */
.edit-container {
    width: 1000px;
    .edit-container-left {
        width: 49%;
        float: left;
        .edit-container-content-main {
            position:relative;
            height: 300px;
            .edit-c-title {
                position: absolute;
                padding: 5px 5px;
                top: -16px;
                left: 10px;
                background: #fff;
            }
        }

    }
    .edit-container-right {
        width: 49%;
        float: right;
        .edit-container-content-main {
            position:relative;
            height: 300px;
            .edit-c-title {
                position: absolute;
                padding: 5px 5px;
                top: -16px;
                left: 10px;
                background: #fff;
            }
        }
    }
}

/* 推荐 */
@mixin normalFont {
    font: normal $sFontNormal $sFontFamily;
    color: #666666;
}
@mixin linkFont {
    font: normal $smallFont $sFontFamily;
    color: #588ced;
}
```


### 使用class覆盖第三方组件样式


使用第三方组件时尽量避免直接重写原标签样式

使用不同类名区分不同场景样式需求

嵌套使用相同第三方组件时，确保样式达到效果的同时，考虑外层样式权重尽量小于内层样式权重，方便内层样式定位

override 一般放置在shared中，若有特殊样式视情况在本组件中重写第三方样式

```scss
/* 不推荐 */
.kendo-dialog {
    .k-widget {
        backgroud: #d1e1f1;
    }
}
/* 推荐 */
.override.kendo-dialog {
    .k-widget {
        backgroud: #d1e1f1;
    }
}
```


### Hacks


请先尝试其他的方法，避免用户代理检测以及CSS的“hacks”。

进行用户代理检测或使用特殊的CSS选择器及hacks看起来是处理样式差异的捷径。但为了实现和保持高效性以及代码的可维护性，这两种方案应该放到最
后考虑。换句话说，用户代理检测和使用hacks会增大项目推进的阻力，所以从项目的长远利益考虑应尽力避免。一旦允许并无顾忌地使用用户代理检测和
hacks便很容易滥用，最终一发而不可收。


### 遵循editorconfig


使用项目根目录下配置的.editorconfig, 保存文件时 Ctrl+Alt+L 格式化代码。

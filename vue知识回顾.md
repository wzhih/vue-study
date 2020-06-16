# Vue 知识回顾

## 指令

| 指令 | 缩写 | 描述 |
|---|---|---|
| `v-bind` | `:` | 绑定自定义属性，传值给组件 |
| `v-on` | `@` | 监听组件触发事件 |
| `v-model` |  | `v-bind`、`v-on`在输入标签的语法糖 |
| `v-html` |  | 输出真正的`html` |
| `v-once` |  | 只渲染时绑定一次 |
| `v-if` |  | 根据参数取决组件是否渲染显示 |
| `v-else-if` |  | 用于`v-if`之后 |
| `v-else` |  | 用于`v-if`或`v-else-if`之后 |
| `v-show` |  | 切换组件的`display` |
| `v-for` |  | 循环输出所在组件 |


## class 和 style 绑定

**这两个特殊的属性，Vue专门做了增强，在`v-bind`绑定数据到属性上的时候，可以使用`字符串`、`数组`、`对象`**

**下面只介绍只使用`对象`的写法，也是觉得比较好的写法**

**由于 JavaScript 的限制，Vue不能检测数组和对象的变化，所以对于数组可以通过`数组更新检测方法`修改、对于对象属性可以通过`vm.$set`方法动态添加**

### class

Html
```html
<div v-bind:class="attrClass"></div>
```

Js
```js
{
    data: () => {
        return {
            attrClass: {
                active: true,
                //有中划线，需要加引号
                "text-dange": false,
                small: true
            }
        }
    }
}
```
通过`v-bind`绑定`attrClass`对象到`div`元素的`class`属性上

通过对`attrClass`对象属性的值的布尔值的切换，可以增减`div`元素的`class`属性的值。最终结果如下：
```html
<div class="active small"></div>
```

### style

Html
```html
<div v-bind:style="attrStyle"></div>
```

Js
```js
{
    data: () => {
        return {
            attrStyle: {
                color: "blue",
                "font-size": "2vw"
            }
        }
    }
}
```
通过`v-bind`绑定`attrStyle`对象到`div`元素的`style`属性上

通过对`attrStyle`对象属性的修改，可以修改`div`元素的`style`属性的值。最终结果如下：
```html
<div style='color: blue;font-size: 2vw;'></div>
```

## 计算属性

对比：

* 方法
  * 计算属性相比较方法，在最终的结果是完全一样的，不同的是，计算属性是基于`响应式依赖`进行`结果缓存`的

  * 计算属性，只有在`第一次使用`或`依赖数据发生变动`之后，才会生成一份缓存

* 侦听属性

  * 侦听属性用来观察与响应Vue实例上的属性变化，用于监听某些属性变化，随着属性变化，去执行某些操作

  * 计算属性可以分成`getter`方法和`setter`方法


## 属性绑定

`v-bind`、`v-model`(本质上是`v-bind`和`v-on`的语法糖)

`v-bind`用的比较多的地方就是标签属性，有时候组件通信使用`v-bind`和`.sync`标识符更好

`v-model`用于输入框、组件等地方

#### 例子：

* `input`
  * `<input type='text' v-model="message" />`
  * `<input type='radio' v-model="message" />`
  * `<input type='checkbox' v-model="message" />` 
    复选框`checkbox`分两种情况：1. 单个复选框绑定的值是布尔值 2. 多个复选框绑定的是数组
* `select`
  * `<select v-model="selected">`
    下拉框也分两种情况：1. 单选：绑定字符串 2. 多选：绑定数组
* `textarea`
  * `<textarea v-model="message"></textarea>`

#### 修饰符

* `.lazy`：`v-model`的触发事件从`input`改变为`change`，是在输入完成且输入框失去焦点时，进行数据同步
* `.number`：自动将用户输入转为数值类型
* `.trim`：自动过滤用户输入的首尾空白字符串


## 事件绑定

Vue使用`v-on`监听事件，触发并运行一些`js`代码

经常用到的就是`click`点击事件

事件修饰符

* `.stop`：阻止事件冒泡
* `.prevent`：阻止默认行为
* `.self`：只有在事件是元素本身(而不是子元素)触发时，才会触发函数操作
* `.capture`：模式事件捕获模式，即，子元素触发的事件，优先在此处理，然后再交由子元素处理
* `.once`：只触发一次
* `.passive`

经常用到的也就前两个

按键修饰符、系统修饰符基本没有使用

## 可复用性 & 组合


### 混入

类似于面向对象中的`trait`，一个混入对象可以包含组件实例包含的任何选项

当使用混入时，所有混入对象中的选项，都会被"混合"到组件本身中

当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行"合并"


### 其他

自定义指令、插件、过滤器用的比较少




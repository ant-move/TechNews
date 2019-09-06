
## Meta 
* version： 1.0.0
* 更新时间：2019-09-06

# 跨端小程序开发风格指南


* 微信小程序
* 支付宝小程序
* 百度智能小程序

这是 Antmove 团队推出的小程序风格指南，该指南结合了微信小程序、支付宝小程序、百度小程序三大小程序框架特性沉淀而来的开发实践指南，旨在于建立一份适用于各小程序平台的开发规范，希望对众多小程序开发者们有所帮助。

## 规则归类

### 先级 A：必要的

这些规则会帮你规避错误，所以学习并接受它们带来的全部代价吧。这里面可能存在例外，但应该非常少，且只有你同时精通 JavaScript 和 各小程序平台开发规范才可以这样做。

### 优先级 B：强烈推荐

这些规则能够在绝大多数工程中改善可读性和开发体验。即使你违反了，代码还是能照常运行，但例外应该尽可能少且有合理的理由。

### 优先级 C：推荐

当存在多个同样好的选项，选任意一个都可以确保一致性。在这些规则里，我们描述了每个选项并建议一个默认的选择。也就是说只要保持一致且理由充分，你可以随意在你的代码库中做出不同的选择。请务必给出一个好的理由！通过接受社区的标准，你将会：

* 训练你的思维，以便更容易的处理社区共享的代码；
* 不做修改就可以直接复制粘贴社区的代码示例；
* 能够经常招聘到和你编码习惯相同的新人，至少跟小程序相关的东西是这样的

### 优先级 D：谨慎使用

有些小程序的特性的存在是为了照顾极端情况或帮助老代码的平稳迁移。当被过度使用时，这些特性会让你的代码难于维护甚至变成 bug 的来源。这些规则是为了给有潜在风险的特性敲个警钟，并说明它们什么时候不应该使用以及为什么。

## 优先级 A 的规则：必要的 (规避错误)

### 组件名为多个单词，采用横杠分割

这样可以避免和现有的以及未来的小程序基础组件相冲突，因为基础组件一版都是单个单词的。

支付宝小程序平台组件命名不支持驼峰形式，横杠形式的命名是都支持的。

#### 反例

```
"usingComponents": {
    "Todo": "../../dist/todo/index"
}
```

#### 好例子

```
"usingComponents": {
    "todo-item": "../../dist/todo/index"
}
```


### 组件属性

组件的 `props` 需定义类型和默认值。

在支付宝小程序中，即使没有在 `props` 中声明的属性，组件也是可以接收到的，但是微信小程序和百度小程序是必须在组件中声明的属性才能接收到。

如果传值类型不同组件也是接收不到的，所以请给一个明确的数据类型。

### 为 for 设置键值

在组件上总是必须用 key 配合 v-for，以便维护内部组件及其子树的状态。

#### 反例

```html
<view>
  <view a:for="{{ todos }}">
    {{ todo.text }}
  </view>
</view>
```

#### 好的例子

```html
<view>
  <view a:for="{{ todos }}" a:key="item.id">
    {{ item.text }}
  </view>
</view>
```

### 避免 a:if 和 a:for 用在一起

请不要把 a:if 和 a:for 同时用在同一个元素上。

一般我们在两种常见的情况下会倾向于这样做：

* 为了过滤一个列表中的项目 (比如 a:for="{{ users }}" a:if="item.isActive")。在这种情形下，请将 users 替换为一个过滤后的数据字段 (比如 activeUsers)，让其返回过滤后的列表。

* 为了避免渲染本应该被隐藏的列表 (比如 a:for="{{ users }}" a:if="{{ shouldShowUsers }}")。这种情形下，请将 a:if 移动至容器元素上。

#### 反例

```html
<view>
  <view
    a:for="{{ users }}"
    a:if="{{ item.isActive }}"
    a:key="{{ item.id }}"
  >
    {{ item.name }}
  </view>
</view>
```

#### 好的例子

```html
<view>
  <view
    a:for="{{ activeUsers }}"
    a:key="{{ item.id }}"
  >
    {{ item.name }}
  </view>
</view>
```

#### 组件样式设置作用域

对于应用来说，顶级 App 组件和布局组件中的样式可以是全局的，但是其它所有组件都应该是有作用域的。

微信小程序默认是组件样式隔离的，但我们推荐通过 CSS Modules，那是一个基于 class 的类似 [BEM](http://getbem.com/) 的策略，当然你也可以使用其它的库或约定。

这让覆写内部样式更容易：使用了常人可理解的 class 名称且没有太高的选择器优先级，而且不太会导致冲突。

> 支付宝小程序目前组件还没有作用域隔离功能，具笔者了解该功能即将发布。

## 优先级 B 的规则：强烈推荐 (增强可读性)

### 变量命名规避关键词

变量的命名在 js 中是不能以关键词及保留字命名的，会直接报错，但在小程序中没有这个严格的限制，但在这里强烈建议不要以小程序的关键字命名。

#### 小程序关键字示例：

* App
* Page
* Component
* props
* properties
* data
* globalData
* methods
* getApp
* onInit
* didMount
* didUpdate
* created
* detached
* onShow
* onHide

小程序并没有严格的规定不能以上述等关键字命名，但编码时应该避免，提升代码的可读性，当在组件中出现该类型的变量时，我们可以有一个直观的理解。比如 `this.data` 就是获取组件内部数据的，`gloabel.Data` 就是获取应用数据的。

### 组件文件

请给每一个组件一个单独的目录，而不是将多个组件混杂在一个目录中。

#### 反例

```
components/
|- Todo.js
|- Todo.acss
|- Todo.axml
|- Todo.json
|- TodoItem.js
|- TodoItem.acss
|- TodoItem.axml
|- TodoItem.json
```

#### 好的例子

```
components/
|- Todo
  |- Todo.js
  |- Todo.acss
  |- Todo.axml
  |- Todo.json
|- TodoItem
  |- TodoItem.js
  |- TodoItem.acss
  |- TodoItem.axml
  |- TodoItem.json
```

### 组件文件命名

组件文件命名推荐一下两种，请在项目中统一使用。

* 驼峰命名，单词大写开头 (PascalCase)
* 横杠命名，单词小写  (kebab-case)

混用文件命名方式有的时候会导致大小写不敏感的文件系统的问题，所以更为横线连接命名。

#### 反例
```
components/
|- mycomponent.axml
```

```
components/
|- myComponent.axml
```

#### 好的例子

```
components/
|- Mycomponent.axml
```

```
components/
|- my-component.axml
```

### 基础组件名

应用特定样式和约定的基础组件 (也就是展示类的、无逻辑的或无状态的组件) 应该全部以一个特定的前缀开头，比如 Base。

#### 反例

```
components/
|- MyButton.axml
|- VueTable.axml
|- Icon.axml
```

#### 好的例子

```
components/
|- BaseButton.axml
|- BaseTable.axml
|- BaseIcon.axml
```

### 单例组件名

只应该拥有单个活跃实例的组件应该以 The 前缀命名，以示其唯一性。

这不意味着组件只可用于一个单页面，而是每个页面只使用一次。这些组件永远不接受任何 prop，因为它们是为你的应用定制的，而不是它们在你的应用中的上下文。如果你发现有必要添加 props，那就表明这实际上是一个可复用的组件，只是目前在每个页面里只使用一次。

#### 反例

```
components/
|- Heading.axml
|- MySidebar.axml
```

#### 好的例子

```
components/
|- TheHeading.axml
|- TheSidebar.axml
```

### 紧密耦合的组件名

和父组件紧密耦合的子组件应该以父组件名作为前缀命名。

如果一个组件只在某个父组件的场景下有意义，这层关系应该体现在其名字上。因为编辑器通常会按字母顺序组织文件，所以这样做可以把相关联的文件排在一起。

#### 反例

```
components/
|- TodoList
  |- index.axml
|- TodoItem
  |- index.axml
```

#### 好的例子

```
components/
|- TodoList
  |- index.axml
|- TodoListItem
  |- index.axml
```

### 组件名中的单词顺序

组件名应该以高级别的 (通常是一般化描述的) 单词开头，以描述性的修饰词结尾。

#### 反例

```
components/
|- ClearSearchButton.axml
|- RunSearchButton.axml
```

#### 好的例子

```
components/
|- SearchButtonClear.axml
|- SearchButtonRun.axml
```

### 完整单词的组件名

组件名应该倾向于完整单词而不是缩写。

编辑器中的自动补全已经让书写长命名的代价非常之低了，而其带来的明确性却是非常宝贵的。不常用的缩写尤其应该避免。

#### 反例

```
components/
|- SdSettings.axml
|- UProfOpts.axml
```

#### 好的例子

```
components/
|- StudentDashboardSettings.axml
|- UserProfileOptions.axml
```

### 多个属性的元素

多个特性的元素应该分多行撰写，每个属性一行。

在 JavaScript 中，用多行分隔对象的多个属性是很常见的最佳实践，因为这样更易读。

#### 反例

```
<my-component foo="a" bar="b" baz="c"/>
```

#### 好的例子

```
<my-component 
  foo="a" 
  bar="b" 
  baz="c"/>
```

### 模板中简单的表达式

#### 组件模板应该只包含简单的表达式，复杂的表达式则应该重构为计算属性或方法。

复杂表达式会让你的模板变得不那么声明式。我们应该尽量描述应该出现的是什么，而非如何计算那个值。而且计算函数和方法使得代码可以重用。

#### 反例

```js
{{
  fullName.split(' ').map(function (word) {
    return word[0].toUpperCase() + word.slice(1)
  }).join(' ')
}}
```

#### 好的例子

```html
<!-- 在模板中 -->
{{ normalizedFullName }}
```

```js
methods: {
  normalizedFullName: function () {
    let normalizedFullName =  this.fullName.split(' ').map(function (word) {
      return word[0].toUpperCase() + word.slice(1)
    }).join(' ')
    
    this.setData({
    normalizedFullName
    })
  }
}
```

### 属性名作用域

小程序提供了组件间代码公用的机制，微信和百度小程序提供 `behavior` 的功能，支付宝小程序提供 `mixin` 的功能，这里建议给公用的代码加上作用域区分。

#### 反例

```js
var myGreatMixin = {
  // ...
  methods: {
    update: function () {
      // ...
    }
  }
}
```

#### 好的例子

```
var myGreatMixin = {
  // ...
  methods: {
    myGreatMixinUpdate: function () {
      // ...
    }
  }
}
```

## 优先级 C 的规则：推荐 (将选择和认知成本最小化)

### 组件/实例的选项顺序

 组件/实例的选项应该有统一的顺序.
 
这是我们推荐的组件选项默认顺序。它们被划分为几大类，所以你也能知道从组件里添加的新属性应该放到哪里。

* 组合 (向选项里合并属性)
  * extends
  * mixins

* 接口 (组件的接口)
  * externalClass
  * props/properties
* 本地状态
  * data
  * 组件私有数据
* 事件 (通过响应式事件触发的回调)
  * 生命周期钩子
    * `onInit`
    * `deriveDataFromProps`
    * `didMount`
    * `didUpdate`
    * `didUnmoun`
* 非响应式的属性 (不依赖响应系统的实例属性)
  * methods

## 优先级 D 的规则：谨慎使用 (有潜在危险的模式)

### 隐性的父子组件通信

应该优先通过 prop 和事件进行父子组件之间的通信，而不是 this.$parent 调用。

一个理想的小程序应用是 prop 向下传递，事件向上传递的。遵循这一约定会让你的组件更易于理解。然而，在一些边界情况下通过获取父子组件的实例来进行变更能够简化两个深度耦合的组件。

问题在于，这种做法在很多简单的场景下可能会更方便。但请当心，不要为了一时方便 (少写代码) 而牺牲数据流向的简洁性 (易于理解)。

> 注： 微信小程序中可通过 `relation` 功能获取父子组件，支付宝小程序可通过 `ref` 来获取，请谨慎使用。
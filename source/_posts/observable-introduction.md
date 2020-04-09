---
title: Observable 基础介绍
date: 2020-04-09 22:57:50
tags: Visualization
category: Visualization
---

> 直接在 Observable 上阅读本文，点击链接 👇
> [https://observablehq.com/@zqlu/observable](https://observablehq.com/@zqlu/observable)

## Observable 是什么？

按照官方的说法, Observable 是用来探索数据和用代码思考的 notebook。如果你知道 Jupter，那么可以理解 Observable 是可视化领域的 notebook。

那可以用 Observable 来做什么呢？

- 搭建真实数据的可视化原型
- 连接到其他的 Web API
- 分享和复用其他作者的技术和组件
- 共享和发布你的作品到社区

Observable notebook 由一个个单元格 cell 组成，每个单元格是用 JavaScript 代码定义的。单元格会在 notebook 展示它的视图，展开单元格的源代码就可以对源代码进行编辑修改，运行单元格代码后，单元格运行结果就会展示在 notebook 上。

## Observable 是怎样运行的？

首先，每个单元格都是一段 JavaScript 代码，运行单元格时，即输出 JavaScript 代码的执行结果。如下面单元格代码：

```js
// 单元格运行时将显示 23
2 + 3 \* 7
```

```js
// 多条语句的代码块使用 {} 包起来
// 单元格运行时显示代码块最后返回结果：4950
{
  let sum = 0;
  for (let i = 0; i <= 100; ++i) {
    sum += i;
  }
  return sum;
}
```

另外，单格格可以命名，使用 `name = {}` 方式来命名：

```js
color = "red";
```

命名单元格可以被其他单元格饮用，如

```js
// 这里英勇了上面单元格 color
// 运行时将显示
// My favorite color is red.
`My favorite color is ${color}.`;
```

**注意**，这里就有 Observable notebook 的一个特性了，当被引用的单元格更新时，引用了它的单元格也会被重新执行。

了解了单元格的定义和运行后，我们可以看以下，单元格运行返回的指都可以时哪些类型？上面我们看到的单元格都只是返回了简单的 number string 等。

- 单元格可以生成和返回 DOM 元素

  简单的说，因为单元格可以执行 JavaScript 代码，所以当然可以在单元格中使用 JavaScript 代码创建 DOM 元素，并返回 DOM 元素，这个时候，单元格运行的时候就会渲染返回的 DOM 元素，如：

```js
        {
        	const elem = document.createElement('span');
        	elem.appendChild(document.createTextNode("Hello from Cell");

        	return elem;
        }
```

- 单元格可以返回 Markdown 文档

  实际了，到这里，你已经看到了很对返回 Markdown 文档的单元格了，比如当前这个单元格：使用内置的 md 标签即可创建 Markdown 文档了：

```js
md`My favorite color is **${color}**.`;
```

看到这里，大概可以明白 Observable notebook 单元格的运行了，返回 JavaScript 的单元格，会在单元格上显示返回值，其他特别的有返回 HTML 和 Markdown 文档的单元格；同时单元格可以命名和被其他单元格引用，引用后的单元格会自动重新执行。

此外，Observable 单元格和普通的 JavaScript 代码还用一下区别：

- 各个单元格是相关独立的代码块：即一个单元格的错误不行阻碍其他单元格代码执行；同时单元格内部变量作用范围也仅在当前单元格
- 单元格是按照拓扑结构顺序运行的：这一点也是 Observable 比较特别的地方，普通的 JavaScript 代码是从上忘下运行的，Observable 中的单元格不是，是按照单元格之前的依赖关系来确定运行顺序的，比如单元格 a 引用了单元格 b，那就会先运行单元格 b。同时，因为这一点，单元格之间不允许存在循环依赖。
- 单元格会自动隐式的等待 Promise resolve，即 await Promise：比如一个单元格返回的是一个 Promise，那单元格运行式会等待 Promise，显示 Promise resolve/reject 的结果：

```js
new Promise((resolve) => {
  setTimeout(() => {
    resolve("Resolve from Promise");
  }, 3000);
});
```

```js
new Promise((resolve, reject) => {
  setTimeout(() => {
    reject("Rejected from Promise");
  }, 3000);
});
```

- 单元格会隐式地遍历 Generators

```js
  c = {
  let cnt = 0;
  while(cnt <= 100) {
  yield cnt;
  cnt += 1;
  }
  }
```

## Observable 中的视图 View

视图 view 是 Observable 中另一个重要的概念，在前面提到了单元格可以返回 DOM 元素，即单元格可以有视图，此外，单元格也有 value，为了让单元格的 value 和视图绑定，Observable 提供了内置的 viewof 操作符。

参见下面的例子: 注意 UI 视图和 input 值之间的关联

```js
    viewof input = html`<input type=range></input>`
```

这背后的原因其实是把值 input 和视图 DOM 元素的 value 属性进行了绑定，注意在不使用 viewof 的时候，即使上也可以读取到 DOM 元素 input2 上的 value，只不过这里就没有绑定了

```js
input2 = html`<input type=range></input>`;
```

```js
input2.value;
```

## 在 Observable 中实现组件

知道了 viewof 的使用后，其实就可以在 Observable 中实现组件化的视图了

```js
    slider = {
      return function(props) {
        const {min = 0, max = 100, initialValue = 50, step = 1, prefix = '', suffix = ''} = props;
        const container = document.createElement('div');
        const prefixNode = document.createElement('span');
        const suffixNode = document.createElement('span');
        const inputNode = document.createElement('input');
        suffixNode.appendChild(document.createTextNode(suffix));
        prefixNode.appendChild(document.createTextNode(prefix));
        inputNode.setAttribute('type', 'range');
        inputNode.setAttribute('min', min);
        inputNode.setAttribute('max', max);
        inputNode.setAttribute('step', step);
        inputNode.setAttribute('value', initialValue);
        container.appendChild(prefixNode);
        container.appendChild(inputNode);
        container.appendChild(suffixNode);

        // 设置视图的初始值
        container.value = Number(initialValue);
        inputNode.addEventListener('input', evt => {
          container.value = Number(evt.target.value);
          // 视图值发生变化，发出事件
          container.dispatchEvent(new CustomEvent("input"));
        });

        return container;
      }
    }
```

```js
    viewof cnt = slider({prefix: 'Start', suffix: 'End'})
```

```js
    viewof timeline = slider({prefix: '20200301', suffix: '20200330', min: 1, max: 30, initialValue: 1})
```

上面的例子，就展示了如何实现一个完整的 Observable 组件了：一个组件包括了：

- 视图：使用 DOM API 创建的 DOM 元素
- 值：值是值直接挂在 DOM 元素上的 value，value 这里虽然是一个简单的 number 值，实际上 value 可以是任意类型的，如复杂的 object 等，注意 value 需要更新变化的时候，需要发事件出来

## 总结

总结一下，到这里，我们从前端开发者的视角介绍了 Observable：

- Observable 的定位和功能
- Observable 运行相关问题
- Observable 中的组件编写

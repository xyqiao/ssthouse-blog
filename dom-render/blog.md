# 可视化讲解 DOM 构建过程

## 前言

最近在看 *Secrets of the JavaScript Ninja*, 书中第二章讲到 DOM 的构建流程. 

记得我之前也为理解 DOM 构建流程查阅过数次资料, 虽然每次查阅完都觉得 DOM 构建流程很简单, 看完便懂, 但是懂了又忘还是让人有些头疼.

为了给自己加深印象, 也为了为大家提供一个可视化的理解 DOM 构建过程的方式, 笔者制作了一个简单的网页来动态演示 DOM 构建过程. 希望能给大家带来一些帮助.

## 效果

### 前进, 后退

网页展示了一个简单的 HTML 页面的 DOM 渲染过程. 用户点击**前进**,**后退**按钮时, 页面左侧会显示出当前的 HTML 代码, 右侧则会显示出实时的 DOM 结构图:

![forward and backword](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/forward_and_backword.gif)

### 自动播放

点击 `Auto Play` 按钮, 页面会自动播放页面的整个构建过程:

![auto play](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/autoplay.gif)

## DOM 构建过程

### DOM 元素的作用 & 基本构建过程:

这里直接引用一下原文:

> The goal of this page-building phase is to set up the UI of a web application, and this is done in two distinct steps:
> 1 Parsing the HTML and building the Document Object Model (DOM)
> 2 Executing JavaScript code
> Step 1 is performed when the browser is processing HTML nodes, and step 2 is > performed whenever a special type of HTML element—the script element (that > contains or
> refers to JavaScript code)—is encountered. During the page-building phase, the browser
> can switch between these two steps as many times as necessary.

浏览器 **页面构建** 步骤的目的是为 UI 渲染做准备, **页面构建**是由下面两部分购成的:

- 解析 HTML 节点, 并且构建 DOM 元素
- 执行 JavaScript 代码

其中第一步在浏览器解析到 HTML 节点时执行, 第二步在解析到 **script** 标签时执行. 在页面构建的过程中, 以上两步可以无数次的交替执行.

> It’s important to emphasize that, although the HTML and the DOM are closely
> linked, with the DOM being constructed from HTML, they aren’t one and the same.
> You should think of the HTML code as a blueprint the browser follows when > constructing the initial DOM—the UI—of the page. The browser can even fix > problems that it finds with this blueprint in order to create a valid DOM.

需要注意的是, 虽然 HTML 和 DOM 两者关系紧密(DOM 是由 HTML 文件构建而来), 但他们并不是相同的. 你应该将 HTML 看作是浏览器用来渲染 DOM 元素(页面 UI) 的蓝图. 浏览器甚至可以可以修复这个蓝图(HTML)中的问题, 并构建出有效的 DOM.

### 下面用可视化讲解中的步骤依次讲解:

#### 首先看看我们想要渲染的 HTML 代码:

```html
<!DOCTYPE html>
<html>
    <head>
      <title>Web app lifecycle</title>
      <style>
          #list { color: green;}
          #second { color: red;}
      </style>
    </head>
    <body>
        <h1>head one</h1>
        <ul id="list"></ul>
        <script>
            var liElement = document.createElement("li");
            liElement.textContent = 'I am a li';
            document.getElementById('list').appendChild(liElement);
        </script>
    </body>
</html>
```

#### 接下来按照浏览器的构建顺序来看:

首先浏览器遇到下面这段代码, 解析出 **html** 节点作为 DOM 的根节点:

```html
<!DOCTYPE html>
<html>
```

![step 1](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/step1.png)

接下来是 `<head>` 标签, 将其放置在 **html** 节点下:

![step 2](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/step2.png)

继续解析, 遇到 `<title>` 标签, 因为其是 `<head>` 的子标签, 故将其放置在 **head** 节点下.

![step 3](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/step3.png)

然后是 `<style>` 标签, 类似的, 放在 **head** 节点下:

```html
<style>
    #list { color: green;}
    #second { color: red;}
</style>
```

![step 4](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/step4.png)

接下来解析到 `<body>` 标签, 因其为 `<html>` 的子标签, 故将其放置在 **html** 节点下:

![step 5](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/step5.png)

然后是 `<h1>` 标签, 放置在 **body** 节点下:

![step 6](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/step6.png)

继续, `<ul>` 标签, 同样的, 放置在 **body** 节点下:

![step 7](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/step7.png)

接下来, 浏览器遇到了 `<script>` 标签, 根据前面的知识我们知道, 浏览器会停下来并执行`<script>` 中的代码. 所以下面这段代码会被立即执行:

```html
<script>
    var liElement = document.createElement("li");
    liElement.textContent = 'I am a li';
    document.getElementById('list').appendChild(liElement);
</script>
```

这段代码的逻辑是: 向 **id** 为 **list** 的 DOM 节点添加一个 **li** 作为子元素, 故执行完成后 DOM 树会是这样:

![step 7](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/step8.png)

最后, 浏览器会解析到 `<body/></html>` 等标签, 结束解析过程. 最终我们得到的 DOM 结构如图:

![step 9](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/step9.png)

####
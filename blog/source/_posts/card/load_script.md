---
title: 在html中通过<script>标签引入外部JS的最佳实践
categories:
  - card
---

现代的网站中，脚本往往比 HTML 更“重”：它们的大小通常更大，处理时间也更长。

当浏览器加载 HTML 时遇到 ```<script>...</script>``` 标签，浏览器就不能继续构建 DOM。它必须立刻执行此脚本。对于外部脚本 ```<script src="..."></script>``` 也是一样的：浏览器必须等脚本下载完，并执行结束，之后才能继续处理剩余的页面。

这会导致两个重要的问题：

脚本不能访问到位于它们下面的 DOM 元素，因此，脚本无法给它们添加处理程序等。
如果页面顶部有一个笨重的脚本，它会“阻塞页面”。在该脚本下载并执行结束前，用户都不能看到页面内容：

```html
<p>...content before script...</p>

<script src="https://javascript.info/article/script-async-defer/long.js?speed=1"></script>

<!-- This isn't visible until the script loads -->
<p>...content after script...</p>
```

这里有一些解决办法。例如，我们可以把脚本放在页面底部。此时，它可以访问到它上面的元素，并且不会阻塞页面显示内容：

```html
<body>
  ...all content is above the script...

  <script src="https://javascript.info/article/script-async-defer/long.js?speed=1"></script>
</body>
```

但是这种解决方案远非完美。例如，浏览器只有在下载了完整的 HTML 文档之后才会注意到该脚本（并且可以开始下载它）。对于长的 HTML 文档来说，这样可能会造成明显的延迟。

这对于使用高速连接的人来说，这不值一提，他们不会感受到这种延迟。但是这个世界上仍然有很多地区的人们所使用的网络速度很慢，并且使用的是远非完美的移动互联网连接。

幸运的是，这里有两个 ```<script>``` 特性（attribute）可以为我们解决这个问题：```defer```和```async```

## defer
defer 特性告诉浏览器不要等待脚本。相反，浏览器将继续处理 HTML，构建 DOM。脚本会“在后台”下载，然后等 DOM 构建完成后，脚本才会执行。

这是与上面那个相同的示例，但是带有 defer 特性：

```html
<p>...content before script...</p>

<script defer src="https://javascript.info/article/script-async-defer/long.js?speed=1"></script>

<!-- 立即可见 -->
<p>...content after script...</p>
```

换句话说：
* 具有 defer 特性的脚本不会阻塞页面。
* 具有 defer 特性的脚本总是要等到 DOM 解析完毕，但在 DOMContentLoaded 事件之前执行。

**具有 defer 特性的脚本保持其相对顺序，就像常规脚本一样。**

**defer 特性仅适用于外部脚本 如果 ```<script>``` 脚本没有 src，则会忽略 defer 特性。**


## async

```async``` 特性与 ```defer``` 有些类似。它也能够让脚本不阻塞页面。但是，在行为上二者有着重要的区别。

```async``` 特性意味着脚本是完全独立的：

* 浏览器不会因 async 脚本而阻塞（与 defer 类似）。
* 其他脚本不会等待 async 脚本加载完成，同样，async 脚本也不会等待其他脚本。
* DOMContentLoaded 和异步脚本不会彼此等待：
  * DOMContentLoaded 可能会发生在异步脚本之前（如果异步脚本在页面完成后才加载完成）
  * DOMContentLoaded 也可能发生在异步脚本之后（如果异步脚本很短，或者是从 HTTP 缓存中加载的）
  
换句话说，```async``` 脚本会在后台加载，并在加载就绪时运行。DOM 和其他脚本不会等待它们，它们也不会等待其它的东西。```async``` 脚本就是一个会在加载完成时执行的完全独立的脚本。

## 动态脚本

```js
let script = document.createElement('script');
script.src = "/article/script-async-defer/long.js";
document.body.append(script); // (*)
```

**默认情况下，动态脚本的行为是“异步”的。**
如果我们显式地设置了 ```script.async=false```，则可以改变这个规则。然后脚本将按照脚本在文档中的顺序执行，就像 ```defer``` 那样。

## 模块脚本

```html
<script type="module" src="..."></script>
```

**模块脚本默认是```defer```**

## 总结

在开发中，我们要根据不同脚本的实际作用来决定该采用defer还是async还是都设为false。
例如一些外部的统计脚本，那么当我们在引入时，应该设置为```async```，这样当这个脚本加载失败时不至于阻塞页面渲染和其他```defer```的脚本运行。



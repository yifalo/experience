---
title: MutationObserver介绍及最佳实践
categories:
  - card
---

```MutationObserver``` 是一个内建对象，它观察 DOM 元素，并在检测到更改时触发回调。
## 一. 语法

首先，需要**创建** 一个 MutationObserver 实例，并传入一个回调函数，当被观察的 DOM 发生变化时，这个回调函数会被调用。
```js
const observer = new MutationObserver((mutationsList, observer) => {
  for (const mutation of mutationsList) {
    console.log(mutation);
  }
});
```

使用 observe 方法开始**观察**目标节点，并传入目标节点和配置对象。配置对象用来指定你要观察哪些类型的变化。

```js
const config = { attributes: true, childList: true, subtree: true };

const targetNode = document.getElementById('some-id');
observer.observe(targetNode, config);
```

```config``` 是一个具有布尔选项的对象，该布尔选项表示“将对哪些更改做出反应”：

* ```childList``` —— ```node``` 的直接子节点的更改
* ```subtree``` —— ```node``` 的所有后代的更改
* ```attributes``` —— ```node``` 的特性（attribute）
* ```attributeFilter``` —— 特性名称数组，只观察选定的特性
* ```characterData``` —— 是否观察 ```node.data```（文本内容）
  
其他几个选项：

* ```attributeOldValue``` —— 如果为 ```true```，则将特性的旧值和新值都传递给回调（参见下文），否则只传新值（需要 ```attributes``` 选项）
* ```characterDataOldValue``` —— 如果为 ```true```，则将 ```node.data``` 的旧值和新值都传递给回调（参见下文），否则只传新值（需要 ```characterData``` 选项）

然后，在发生任何更改后，将执行“回调”：更改被作为一个 MutationRecord 对象列表传入第一个参数，而观察器自身作为第二个参数。

```MutationRecord``` 对象具有以下属性：

* ```type``` —— 变动类型，以下类型之一：
  * ```"attributes"```：特性被修改了
  * ```"characterData"```：数据被修改了，用于文本节点
  * ```"childList"```：添加/删除了子元素
* ```target``` —— 更改发生在何处："attributes" 所在的元素，或 "characterData" 所在的文本节点，或 "childList" 变动所在的元素
* ```addedNodes/removedNodes``` —— 添加/删除的节点
* ```previousSibling/nextSibling``` —— 添加/删除的节点的上一个/下一个兄弟节点，
* ```attributeName/attributeNamespace``` —— 被更改的特性的名称/命名空间（用于 XML）
* ```oldValue``` —— 之前的值，仅适用于特性或文本更改，如果设置了相应选项```attributeOldValue/characterDataOldValue```
  
## 二. 最佳实践

### 1. 明确监听需求，避免性能开销
MutationObserver 可以监听多种类型的 DOM 变化，在使用时应根据具体需求设置合适的配置项，避免不必要的性能开销。例如，如果只需要监听子节点的变化，就不要配置监听属性变化。

```js
const config = { childList: true, subtree: false };
```

### 2. 防止重复触发
在某些情况下，DOM 变化可能会触发多次观察回调，导致重复处理。可以使用标志位或其他逻辑避免重复处理

```js
let isHandling = false;

const observer = new MutationObserver((mutationsList, observer) => {
  if (isHandling) return;
  isHandling = true;

  // 处理 DOM 变化
  for (const mutation of mutationsList) {
    console.log(mutation);
  }

  isHandling = false;
});
```

### 3. 使用 ```attributeFilter``` 优化属性观察
如果只需要观察特定的属性变化，可以使用 attributeFilter 选项来指定需要观察的属性列表。

```js
const config = { attributes: true, attributeFilter: ['class', 'style'] };
observer.observe(targetNode, config);
```

### 4. 处理变更记录
```MutationObserver``` 的回调函数会接收一个变更记录列表。每个变更记录包含了变更的详细信息，根据这些信息可以更精细地处理 DOM 变化。

```js
const observer = new MutationObserver((mutationsList, observer) => {
  for (const mutation of mutationsList) {
    switch (mutation.type) {
      case 'attributes':
        console.log(`Attribute ${mutation.attributeName} was modified.`);
        break;
      case 'childList':
        if (mutation.addedNodes.length) {
          console.log('A child node has been added.');
        }
        if (mutation.removedNodes.length) {
          console.log('A child node has been removed.');
        }
        break;
      case 'subtree':
        console.log('Subtree has been modified.');
        break;
    }
  }
});
```

## 三. 其他方法

有一个方法可以停止观察节点：

* ```observer.disconnect()``` —— 停止观察。
  
当我们停止观察时，观察器可能尚未处理某些更改。在种情况下，我们使用：

* ```observer.takeRecords()``` —— 获取尚未处理的变动记录列表，表中记录的是已经发生，但回调暂未处理的变动。

**observer.takeRecords() 返回的记录被从处理队列中移除**

## 四. 垃圾回收

观察器在内部对节点使用弱引用。也就是说，如果一个节点被从 DOM 中移除了，并且该节点变得不可访问，那么它就可以被垃圾回收。

观察到 DOM 节点这一事实并不能阻止垃圾回收。
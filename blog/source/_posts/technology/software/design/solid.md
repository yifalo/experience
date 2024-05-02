---
categories:
  - technology
  - software
  - design
---

## SOLID 设计模式及实践（剥豆豆Game模块重构以及题型重构）

### SOLID

* 单一职责原则 (SRP Single Responsibility Principle)
  
```
"There should never be more than one reason for a class to change." In other words, every class should have only one responsibility.
一个Class的改变最多只有一个原因。换句话说，每个Class只有一个职责。
```

* 开放封闭原则 (OCP Open Closed Principle)

```
Software entities ... should be open for extension, but closed for modification.
抽象不应该依赖于细节，细节应当依赖于抽象。换言之，要针对接口编程，而不是针对实现编程。
```

* 里氏替换原则 (LSP Liskov Substitution Principle)

```
Functions that use pointers or references to base classes must be able to use objects of derived classes without knowing it.
所有引用基类的地方必须能透明地使用其子类的对象。简单地说：所有父类能出现的地方，子类就可以出现，并且替换了也不会出现任何错误
```

* 接口隔离原则 (ISP Interface Segregation Principle)

```
Clients should not be forced to depend upon interfaces that they do not use.
不能强迫客户端去依赖那些他们不使用的接口
```

* 依赖倒置原则 (DIP Dependence Inversion Principle)

```
Depend upon abstractions, [not] concretes.
依赖抽象而不是细节
```

### 实践 —— 后端游戏模式重构

* 单一职责原则
  
```
每一种游戏模式都有自己的实现类去负责自己的游戏逻辑。一个游戏模式内部的逻辑改变不影响其他游戏模式。
```

* 开放封闭原则
  
```
当有新的模式产生时，只需要新增新模式的实现类，而不动原有的所有其他模式的实现类。
```

* 里氏替换原则
  
```
没有专门使用基类的场景。
```

* 接口隔离原则
  
```
每个游戏模式都依赖自己的接口，不存在被迫实现本模式不需要的接口。
```

* 依赖倒置原则

```
所有高层调用游戏模式的逻辑全部依赖游戏模式的接口，而不是具体实现。
```

### 实践 —— 前端(Vue3)题型模块重构

* 单一职责原则
  
```
按题型场景设计题型组件。如题型编辑器，题型题目内容展示，题型游戏内显示，题型概览报告显示和题型报告内显示。每个组件负责自己的显示逻辑。
```

* 开放封闭原则
  
```
当有新的题型产生时，只需实现新题型的不同场景组件。不用修改原有题型组件。
```

* 组件是可扩展的
* 组件的边界
* 组件参数的抽象，源于业务高于业务（不一定是跟业务一一对应）
* 组件中提取子组件，通用函数；消除重复代码，特别是逻辑的重复代码不应该出现
* 随时调整抽象结构，简化逻辑
* 面向对象 SOLID 原则中的单一职责、开闭原则

#### 示例

```vue
<script setup lang="ts">
const props = withDefaults(defineProps<{

	// 题目 或 题目统计
	item: Item | ItemResultDetailDto;

	// 展示场景（普通，报告，回顾）
	mode?: 'normal' | 'report' | 'review';

	// 是否展示选项
	showAnswers?: boolean;

	// 是否展示正确答案
	showCorrectAnswers?: boolean;

	// 是否展示题目中的图片
	showTitleImg?: boolean;

	// 报告展示方式
	reportDisplayType?: ReportDisplayType;
}>(), {
	mode: 'normal',
	showAnswers: true,
	showCorrectAnswers: true,
	showTitleImg: true,
	reportDisplayType: 'both',
});

const displayMap: Map<ItemTypeName, any> = new Map<ItemTypeName, any>();
displayMap.set(ItemTypeNameEnum.CHOICE, ChoiceDisplay);
displayMap.set(ItemTypeNameEnum.FILL, FillDisplay);
displayMap.set(ItemTypeNameEnum.MULTIPLE_CHOICE, MultipleChoiceDisplay);
displayMap.set(ItemTypeNameEnum.VOTE, VoteDisplay);
displayMap.set(ItemTypeNameEnum.MULTIPLE_VOTE, VoteDisplay);
displayMap.set(ItemTypeNameEnum.TRUE_OR_FALSE, TrueOrFalseDisplay);
displayMap.set(ItemTypeNameEnum.SORT, SortDisplay);
displayMap.set(ItemTypeNameEnum.MATCH, MatchDisplay);
displayMap.set(ItemTypeNameEnum.ESSAY, EssayDisplay);
displayMap.set(ItemTypeNameEnum.WORD_CLOUD, WordCloudDisplay);

const display = computed(() => displayMap.get(props.item.type));

const item = toRef(props, 'item');

onMounted(() => {
	useKatexRender(item);
})
</script>

<template>
	<component v-if="item.type" :is="display" :item="item" :mode="mode" :show-answers="showAnswers" :show-correct-answers="showCorrectAnswers" :show-title-img="showTitleImg" :report-display-type="reportDisplayType"/>
</template>

```

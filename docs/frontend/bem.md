---
title: Bem 规范及在项目中的使用
---

## BEM 定义
> BEM代表块（Block），元素（Element），修饰符（Modifier）

```javascript
.block{}
.block__element{}
.block--modifier{}
```


- block 代表了更高级别的抽象或组件
- block__element 代表 block 的后代，用于形成一个完整的 block 的整体
- block--modifier代表 block 的不同状态或不同版本
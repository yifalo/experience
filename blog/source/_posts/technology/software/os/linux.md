---
title: Linux命令合集
categories:
  - technology
  - software
  - os
---

### more

```
把内容全部加载完成后展示
```

* ⬇️ 或 回车 下一行
* ⬆️ 上一行
* 空格 下一页
* B 上一页
  
```bash
more xxx.conf
history | more
```

### less (比more更强大)

```
加载完成后展示
```

* ⬇️ 或 回车 下一行
* ⬆️ 上一行
* 空格 下一页
* B 上一页
* /hello：向下搜索字符串“hello”
* ?hello：向上搜索字符串“hello”
  
```bash
less xxx.conf
history | less
```
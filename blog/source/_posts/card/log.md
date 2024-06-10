---
title: 常见日志操作的最佳实践
categories:
  - card
---

### 查看最新内容
1. 基础用法
```bash
tail test.log               #默认10行
```
2. 指定行数
```sh
tail -n 1000 test.log
```
3. 实时查看
**添加 -f 参数**
```bash
tail -f test.log
```
4. 实时查看（每隔3s刷新一次）
**添加 -f 参数**
```bash
tail -f test.log
```

### 分页查看大文件
1. 基础用法
```bash
less test.log
```

* 上一行: 方向键⬆
* 下一行: 方向键⬆或回车
* 上一页: B
* 下一页: 空格
* 搜索: / + 要搜索的内容
* 退出: q

2. 搭配管道
```bash
tail -n 10000 | less
```
### 搜索特定关键词
1. 基础用法

* 查找服务器异常日志
```bash
docker compose logs server --tail 100 -f |grep Exception -C 20    #-C 显示匹配内容及前后20行内容
```

* 分页查找Nginx日志中IP为x.x.x.x的用户的请求记录
```bash
cat bodoudou-web_access.log |grep x.x.x.x | less
```

2. 多条件可以连用grep
```bash
cat bodoudou-web_access.log |grep x.x.x.x |grep "\"status\":\"500\"" | less
```

3. 配合正则表达式，查找所有4xx请求
```bash
cat bodoudou-web_access.log |grep "\"status\":\"4[0-9][0-9]\"" | less
```

### 权限管理：只有授权用户可以查看、编辑日志
```bash
sudo chown root:adm /var/log/syslog
sudo chmod 640 /var/log/syslog
```
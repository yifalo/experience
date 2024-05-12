---
title: 'Nginx 配置更改后重载'
categories:
  - technology
  - software
  - nginx
---

```bash
sudo service nginx configtest
sudo service nginx reload
```

```
reload 不会中断Nginx服务
restart 会中断Nginx服务
```

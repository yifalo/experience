---
title: 'Mysqldump 操作'
categories:
  - technology
  - software
  - database
  - mysql
---

#### Mysqldump 导出服务器中某张表的数据（不含表结构）到本地
```bash
mysqldump -uroot -p bodoudou_prod paper --no-create-info > /tmp/paper.sql
mysql -uroot -p -D bodoudou_dev < /var/lib/data/paper.sql
```

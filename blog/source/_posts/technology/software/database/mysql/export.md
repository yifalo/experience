---
title: 'Mysql导出数据'
categories:
  - technology
  - software
  - database
  - mysql
---

```
1. echo "select mobile from bodoudou_prod.user where login_at < 1678291200000 order by login_at desc limit 1000" | mysql -uroot -p > /tmp/bodoudou_mobile.xlsx
2. scp work@124.160.104.74:/tmp/bodoudou_mobile.xlsx .
```
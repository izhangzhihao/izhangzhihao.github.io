---
layout: post
title: 解决MySQL sql_mode=only_full_group_by
categories: MySql
description: 解决sql_mode=only_full_group_by
---
# 错误信息
---

[2016-06-29 09:05:10] [42000][1055] Expression #1 of SELECT list is not in GROUP BY clause and
contains nonaggregated column 'SpringMVCSeedProject.user0_.userName'
which is not functionally dependent on columns in GROUP BY clause;
this is incompatible with sql_mode=only_full_group_by

---

# 在mysql里运行以下命令即可！但是这样重启后问题依旧，治标不治本！

---
``` Sql

 set global sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';

 set session sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';

```
---

# 彻底根除的办法！打开 etc/mysql/mysql.conf.d/mysqld.cnf 在 [mysqld] 下添加：

---

sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION

---

# 完美解决！

# 错误信息 
---

[2016-06-29 09:05:10] [42000][1055] Expression #1 of SELECT list is not in GROUP BY clause and
contains nonaggregated column 'SpringMVCSeedProject.user0_.userName'
which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by

---

# 在mysql里运行以下命令即可！

---

 set global sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
 
 set session sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
 
---

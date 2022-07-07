---
created: [2022-05-30 11:03]
aliases: []
tags : [3.자기계발/B.SQL/2.MySQL/내장함수]
---

# Today I Learned
## Created : [[2022-05-30]]
## 내용
- `LAST_INSERT_ID()` : 가장 최근에 성공적으로 수행된 INSERT 구문의 첫번째 AUTO_INCREMENT 컬럼의 값을 반환한다.

```MySQL
USE test;
Database changed
CREATE TABLE t (
	id INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
    name VARCHAR(10) NOT NULL
);
Query OK, 0 rows affected (0.09 sec)

INSERT INTO t VALUES (NULL, 'Bob');
Query OK, 1 row affected (0.01 sec)

SELECT * FROM t;
+----+------+
| id | name |
+----+------+
|  1 | Bob  |
+----+------+
1 row in set (0.01 sec)

SELECT LAST_INSERT_ID();
+------------------+
| LAST_INSERT_ID() |
+------------------+
|                1 |
+------------------+
1 row in set (0.00 sec)

INSERT INTO t VALUES (NULL, 'Mary'), (NULL, 'Jane'), (NULL, 'Lisa');
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

SELECT * FROM t;
+----+------+
| id | name |
+----+------+
|  1 | Bob  |
|  2 | Mary |
|  3 | Jane |
|  4 | Lisa |
+----+------+
4 rows in set (0.01 sec)

SELECT LAST_INSERT_ID();
+------------------+
| LAST_INSERT_ID() |
+------------------+
|                2 |
+------------------+
1 row in set (0.00 sec)
```
## ⏱히스토리
	- 2022-05-30 11:03 최초 작성


📙URL :
# 技术笔记(5)-表联接Join



数据库的常用表联接主要有inner join、left join、right join三种。
 
例表A

| Aid | Adate |
| :---: | :---: |
| 1 | a1 |
| 2 | a2 |
| 3 | a3 |

例表B

| Bid | Bdate |
| :---: | :---: |
| 1 | b1 |
| 2 | b2 |
| 4 | b4 |

1、inner join：取出id相同的字段，仅返回两表匹配的数据

```
select * from A inner join B on A.Aid = B.Bid
```

此时的取出的是:

| Aid | Adate | Bid | Bdate |
| :---: | :---: | :---: | :---: |
| 1 | a1 | 1 | b1 |
| 2 | a2 | 2 | b2 |

2、left join：先取出“左表”A表中所有数据，然后再加上与A、B匹配的的数据。不管匹配与否，“左表”表A的全部数据将全部返回

```
select * from a left join b on a.aid = b.bid
```

此时的取出的是:

| Aid | Adate | Bid | Bdate |
| :---: | :---: | :---: | :---: |
| 1 | a1 | 1 | b1 |
| 2 | a2 | 2 | b2 |
| 3 | a3 |   |    |

3、right join：先取出“右表”B表中所有数据，然后再加上与A、B匹配的的数据。不管匹配与否，“右表”B表的全部数据将全部被返回

```
select * from a right join b on a.aid = b.bid
```

此时的取出的是:

| Aid | Adate | Bid | Bdate |
| :---: | :---: | :---: | :---: |
| 1 | a1 | 1 | b1 |
| 2 | a2 | 2 | b2 |
|   |    | 4 | b4 |

4、小结

若 A join B，那么 A 为“左表”，B为“右表”。此时，
用 left join，则取左表（A）全部数据，和右表（B）匹配数据
用 right join，则取右表（B）全部数据，和左表（A）匹配数据
用 inner join，则仅取两表同时匹配数据

由此，可以推断

```
select * from A left join B on A.Aid=B.Bid
```

等价于(表的内容一样，只是字段的顺序不同)

```
select * from B right join A on A.Aid=B.Bid
```


# HAVING_NULL
## NULLを含まない集合を探す
COUNT(*)とCOUNT(列名)には以下の違いある
1. パフォーマンス
2. COUNT(*)はNULLを数えるが、COUNT(列名)はNULLを除外して集計する

|col_1|
|:----|
|NULL|
|NULL|
|NULL|

``` SQL
SELECT COUNT(*) AS col_1, COUNT(col) AS col_2 FROM NullTbl

-- result
col_1 col_2
3 0
```

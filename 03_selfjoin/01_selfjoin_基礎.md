# selfjoin
同一のテーブルを対象に行う結合のこと
## 重複順列・順列・組み合わせ
- 順序対: 並び順を意識した組み合わせ -> <1.2> ≠ <2.1>
- 非順序対: 並び順を意識しない組み合わせ -> {1,2} = {2,1}  
※ CROSS JOINは結合条件が存在せず、総当たりで全レコードの組み合わせを列挙するため、コストが高い
``` sql
-- 順序対(重複あり)
SELECT
	p1.name as name_1
	,p2.name as name_2
FROM
	Chapter3Products as p1
CROSS JOIN
	Chapter3Products as p2

-- result
name_1	name_2
りんご	りんご
みかん	りんご
バナナ	りんご
りんご	みかん
みかん	みかん
バナナ	みかん
りんご	バナナ
みかん	バナナ
バナナ	バナナ
```
<りんご,りんご>のような冗長を削除する  
**別名(エイリアス)が与えられたなら、同一テーブルでもSQLでは異なるテーブル(集合)とみなされる**
``` sql
-- 順序対(重複なし)
SELECT
	p1.name as name_1
	,p2.name as name_2
FROM
	Chapter3Products as p1
INNER JOIN
	Chapter3Products as p2
ON
	p1.name <> p2.name

-- result
name_1	name_2
みかん	りんご
バナナ	りんご
りんご	みかん
バナナ	みかん
りんご	バナナ
みかん	バナナ
```

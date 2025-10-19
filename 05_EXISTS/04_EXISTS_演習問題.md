# EXISTS_演習問題
Q.オール1を探す  
Aは全てNULL、Bはindex1だけ3、Cは全て1で各キーのindexは10まである(下記テーブルは省略系)
|key_value|index_num|val|
|:----|:----|:----|
|A|1|NULL|
|A|2|NULL|
|A|3|NULL|
|B|1|3|
|B|2|NULL|
|B|3|NULL|
|C|1|1|
|C|2|1|
|C|3|1|

サブクエリの結果がunknownの場合はSELECTは行を返さない  
**行を返さない条件はfalseかunknownの2通りあるにも関わらず、NOT EXISTSは行を返さない = trueに集約してしまう**
``` sql
SELECT * FROM Chapter5ArrayTbl2 AS a1
WHERE
	NOT EXISTS(
		SELECT * FROM Chapter5ArrayTbl2 AS a2
		WHERE
			a1.key_value = a2.key_value
		AND
			COALESCE(a2.val,0) <> 1);

-- result
key_value
C

-- ALL
SELECT DISTINCT key_value FROM Chapter5ArrayTbl2 as a1
WHERE
	1 = ALL(
		SELECT val FROM Chapter5ArrayTbl2 as a2
		WHERE
			a1.key_value = a2.key_value
		)

-- HAVING
SELECT key_value FROM Chapter5ArrayTbl2
GROUP BY
	key_value
HAVING
	10 = SUM(
		CASE
			WHEN val = 1 THEN 1
			ELSE 0
		END)

-- HAVING + 極値関数
-- 1が1つでもあったらNULLばかりでもヒットする
SELECT key_value FROM Chapter5ArrayTbl2
GROUP BY
	key_value
HAVING
	MAX(val) = 1
AND
	MIN(val) = 1
```




|A|4|NULL|
|A|5|NULL|
|A|6|NULL|
|A|7|NULL|
|A|8|NULL|
|A|9|NULL|
|A|10|NULL|

|B|4|NULL|
|B|5|NULL|
|B|6|NULL|
|B|7|NULL|
|B|8|NULL|
|B|9|NULL|
|B|10|NULL|

|C|4|1|
|C|5|1|
|C|6|1|
|C|7|1|
|C|8|1|
|C|9|1|
|C|10|1|

# NULL_実践編続
## CASE式とNULL
以下は「×」を絶対に返さない  
-> WHEN NULL THEN '×'は「col_1 = NULL」の省略だから  
**※NULLは値ではない**
``` sql
CASE col_1
  WHEN 1 THEN '〇'
  WHEN NULL THEN '×'
END
```
よって以下のようにしないといけない
``` sql
CASE
  WHEN col_1 = 1 THEN '〇'
  WHEN col_1 IS NULL THEN '×'
END
```
## NOT INとNOT EXISTSは同値ではない
INをEXISTSに書き換えることは問題ないチューニングである  
ただ、NOT INとNOT EXISTSは必ずしも結果が一致するわけではない  
Class_A
|name|age|city|
|:----|:----|:----|
|ブラウン|22|東京|
|ラリー|19|埼玉|
|ボギー|21|千葉|

Class_B
|name|age|city|
|:----|:----|:----|
|斎藤|22|東京|
|田尻|23|東京|
|山田|NULL|東京|
|和泉|18|千葉|
|武田|20|千葉|
|石川|19|神奈川|

EX) Bクラスの東京在住の生徒と年齢が一致しないAクラスの生徒を抽出する  
NOT INの場合は1件も表示されない  
``` SQL
SELECT * FROM Chapter4Class_A
WHERE
	age NOT IN (
		SELECT age FROM Chapter4Class_B
		WHERE
			city = '東京')

-- result
name	age	city
```
条件部分は以下の様に考えられる  
1. 「age NOT IN(22, 23, NULL)」  
1. NOTとINに同値変換「NOT age IN(22, 23, NULL)」
1. ORに同値変換「NOT((age = 22) OR (age = 23) OR (age = NULL))」
1. ドモルガンの法則で同値変換「NOT(age = 22) AND NOT(age = 23) AND NOT(age = NULL)」
1. NOTと=を<>で同値変換「(age <> 22) AND (age <> 23) AND (age <> NULL)」
1. NULLに<>を適用すると「(age <> 22) AND (age <> 23) AND (age <> unknown)」
1. AND演算にunknownがあるとtrueにならない

**NOT INのサブクエリで使用されるテーブルの選択列にNULLがあるとSQLの結果は常に空になる** 

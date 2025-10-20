# HAVING_NULL_特性関数
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

EX) 所属する全学生がレポートを提出している学部を求める
|student_id|dpt|sbmt_date|
|:----|:----|:----|
|100|理学部|2018-10-10|
|101|理学部|2018-09-22|
|102|文学部|NULL|
|103|文学部|2018-09-10|
|200|文学部|2018-09-02|
|201|工学部|NULL|
|202|経済学部|2018-09-25|

NULLを含んだ数と含まない数を比較すればよい  
HAVING句は集合の性質を調べる道具として使える
``` sql
-- COUNT(列名)
SELECT dpt FROM Chapter6Students
GROUP BY
	dpt
HAVING
	COUNT(*) = COUNT(sbmt_date)

-- CASE式での記述
SELECT
	dpt
FROM Chapter6Students
GROUP BY
	dpt
HAVING
	COUNT(*) = SUM(
		CASE
			WHEN sbmt_date IS NOT NULL THEN 1
			ELSE
				0
		END)

-- result
dpt
経済学部
理学部
```

### 特性関数の応用
CASE式は各要素(行)が特定の条件を満たす集合かを決める関数 = 特性関数の1つである  
|student_id|class|sex|score|
|:----|:----|:----|:----|
|001|A|男|100|
|002|A|女|100|
|003|A|女|49|
|004|A|男|30|
|005|B|女|100|
|006|B|男|92|
|007|B|男|80|
|008|B|男|80|
|009|B|女|10|
|010|C|男|92|
|011|C|男|80|
|012|C|女|21|
|013|D|女|100|
|014|D|女|0|
|015|D|女|0|

EX) クラスの75%が80点以上のクラスを求める
``` sql
SELECT class FROM Chapter6TestResults
GROUP BY
	class
HAVING
	COUNT(*) * 0.75 <= SUM(
		CASE
			WHEN 80 <= score THEN 1
			ELSE 0
		END)

-- result
class
B
```
EX) 男子の数が女子の数より多いクラスを求める
``` sql
SELECT class FROM Chapter6TestResults
GROUP BY
	class
HAVING
	SUM(
		CASE
			WHEN sex = '女' THEN 1
			ELSE 0
		END) <
	SUM(
		CASE
			WHEN sex = '男' THEN 1
			ELSE 0
		END)

-- result
class
B
C
```
EX) 女子の平均点が男子の平均点より高いクラスを求める
``` sql
SELECT class FROM Chapter6TestResults
GROUP BY
	class
HAVING
	AVG(
		CASE 
			WHEN sex = '男' THEN score
			ELSE NULL
		END) <
	AVG(
		CASE
			WHEN sex = '女' THEN score
			ELSE NULL
		END)

-- result
class
A
```
ELSEが0でなくNULLの理由
Dクラスは女子しかいないため、男子の平均点は常に0となってしまう  
-> 男子は平均点が算出できない、つまり空集合を0で代用するのではなく未定義とするのが適切
集約関数であるAVGは空集合を適用した場合、NULLを返す仕様である  

集合の性質に着目することは個々の要素を無視するということである  
例題はクラスの集団としての特徴や傾向性に目を向け、個人が何点取ったかは選択しない

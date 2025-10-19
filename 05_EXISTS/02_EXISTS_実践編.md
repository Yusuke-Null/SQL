# EXISTS_実践編
## テーブルに存在しないデータを探す
EX) EXISTSで欠席者一覧を作成する(データが存在するか)
|meeting|person|
|:----|:----|
|第1回|伊藤|
|第1回|水島|
|第1回|坂東|
|第2回|伊藤|
|第2回|宮田|
|第3回|坂東|
|第3回|水島|
|第3回|宮田|

実際の出席者の集合から全員が全ての階に出席していた集合に存在しないケースを探せばよい  
``` SQL
SELECT
	DISTINCT m1.meeting, m2.person
FROM
	Chapter5Meetings as m1
CROSS JOIN
	Chapter5Meetings as m2
WHERE
	NOT EXISTS(
		SELECT * FROM Chapter5Meetings as m3
		WHERE
			m1.meeting = m3.meeting
		AND
			m2.person = m3.person);

-- result
meeting	person
第1回	宮田
第2回	坂東
第2回	水島
第3回	伊藤
```
上記は素直に書いたSQLであり、差集合演算でやると以下になる  
-> EXISTSには差集合演算としての機能がある
``` SQL
SELECT
	 m1.meeting, m2.person
FROM
	Chapter5Meetings as m1
CROSS JOIN
	Chapter5Meetings as m2
EXCEPT
	SELECT meeting, person FROM Chapter5Meetings
```

## 全称量化-肯定と二重変換
「全ての行について~」を「~でない行が1つもない」に置き換える  
|student_id|subject|score|
|:----|:----|:----|
|100|算数|100|
|100|国語|80|
|100|理科|80|
|200|算数|80|
|200|国語|95|
|300|算数|40|
|300|国語|90|
|300|社会|55|
|300|社会|55|
|400|算数|80|

EX) 全教科50点以上の生徒  
「全ての教科が50点以上である」-> 「50点未満の教科が1つもない」
``` sql
SELECT DISTINCT student_id FROM Chapter5TestScores as t1
WHERE
	NOT EXISTS(
		SELECT * FROM Chapter5TestScores as t2
		WHERE
			t1.student_id = t2.student_id
		AND
			t2.score < 50)

-- result
student_id
100
200
400
```

EX2) 算数が80点以上かつ国語が50点以上  
ある学生の全行について、算数なら80点以上かつ、国語なら50点以上という全称量化であり  
同一の集合内の行での条件分岐はCASE式で表現できる
``` sql
SELECT DISTINCT student_id FROM Chapter5TestScores as t1
WHERE
	NOT EXISTS(
		SELECT * FROM Chapter5TestScores as t2
		WHERE
			t1.student_id = t2.student_id
		AND
			1 = CASE
					WHEN t2.subject = '算数' AND t2.score < 80 THEN 1
					WHEN t2.subject = '国語' AND t2.score < 50 THEN 1
				ELSE
					0
				END);

-- result
student_id
100
200
400
```

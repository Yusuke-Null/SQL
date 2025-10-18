# NULLと集約関数
入力テーブルが空の場合はCOUNT関数以外の集約関数でも同じ  
EX) 東京在住の生徒の平均年齢より若いAクラスの生徒を選択するSQL  
``` sql
SELECT * FROM Chapter4Class_A
WHERE
	age	< (
		SELECT AVG(age) FROM Chapter4Class_B
		WHERE
			Chapter4Class_B.city = '東京')

-- result
name	age	city
ラリー	19	埼玉
ボギー	21	千葉

-- Chapter4Class_Bのみ埼玉がいない場合
SELECT * FROM Chapter4Class_A
WHERE
	age	< (
		SELECT AVG(age) FROM Chapter4Class_B
		WHERE
			Chapter4Class_B.city = '埼玉');

-- result
name	age	city
```
AVG関数はNULLを返す = age < unknownになるため

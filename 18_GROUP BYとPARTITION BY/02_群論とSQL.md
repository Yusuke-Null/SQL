# 群論とSQL
群では分類の仕方に応じて類に色々な名前が付けられている  
その1つとして「剰余類」というものがある  
-> 整数を余りで分類した類  
EX) 3で割った余りで自然数(N)を分類する
- 余り0: M1 = {0,3,6,9...}
- 余り1: M1 = {1,4,7,10...}
- 余り2: M1 = {2,5,8,11...}

類の第二の性質(同時に複数の類に所属しない)ことから  
数式では「M1 + M2 + M3 = N」で、SQLでも剰余の関数MODが実装されている  
※ 標準SQLではないが、ほとんどのDBMSで使える(SQL Serverは%演算子がある)  
``` sql
-- 1~10までを3で割った時の剰余類の分類
-- 他のDBMSならMOD(num,3)
SELECT num % 3 AS modulo, num
FROM Chapter18Natural
ORDER BY modulo,num

-- result
modulo	num
0	3
0	6
0	9
1	1
1	4
1	7
1	10
2	2
2	5
2	8
```
剰余類は色々応用が利き、大量のデータから特定の抽出率でサンプリングするときに便利  
-> もとの自然数の集合を等しいサイズに類に分割するため  
Q. データ量を無作為に(ほぼ)1/5に減らす
```
SELECT * FROM Chapter18Natural
WHERE
	num % 5 = 0

-- result
num
5
10

-- 連番がない場合はROW_NUM()でOK
SELECT seq FROM
(SELECT
	num
	,ROW_NUMBER() OVER(ORDER BY num) AS seq
 FROM
	Chapter18Natural) AS tmp
WHERE
	seq % 5 = 0

-- result
seq
5
10
```
実際のテーブルの行数が5の倍数丁度になるとは限らないため、剰余類同士が完全に同じサイズになることはまれだが  
データを無作為にサンプリングする要件は十分に満たせる


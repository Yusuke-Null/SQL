# CASE
注意点
- 返すデータ型は全て一致させる
- ELSE句は必ず書くこと。書かない場合は暗黙的にELSE NULLの扱いになる  
-> エラーにならないのに結果が異なるバグになるし、ELSE NULLを明示的にした方がミスを減らせる
## 
```SQL
-- 単純CASE式
SELECT
	CASE sex
		WHEN '1' THEN '男'
		WHEN '2' THEN '女'
		ELSE 'その他'
	END
FROM
	test;

-- 検索CASE式
SELECT
	CASE
		WHEN sex = '1' THEN '男'
		WHEN sex = '2' THEN '女'
		ELSE 'その他'
	END
FROM
	test
```
CASEはデータの読み替えを行うラベルの機能であり、集計時に進化を発揮する
```
SELECT
	CASE pref_name
		WHEN '徳島' THEN '四国'
		WHEN '香川' THEN '四国'
		WHEN '福岡' THEN '九州'
		WHEN '佐賀' THEN '九州'
		ELSE 'その他'
	END
	,SUM([population]) 
FROM
	Pop
GROUP BY
	CASE pref_name
		WHEN '徳島' THEN '四国'
		WHEN '香川' THEN '四国'
		WHEN '福岡' THEN '九州'
		WHEN '佐賀' THEN '九州'
		ELSE 'その他'
	END
```
異なる条件の式を集計する際も有効  
UNIONでもできるが、二度SQLを発行するのと実質コストが同じの為、パフォーマンスは変わらない
``` SQL
SELECT
	pref_name
	,SUM(CASE
			WHEN sex = '1' THEN population
			ELSE 0
		END) as man
	,SUM(CASE
			WHEN sex = '2' THEN population
			ELSE 0
		END) as woman
FROM
	Pop
GROUP BY
	pref_name
```

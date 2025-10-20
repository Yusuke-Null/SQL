# HAVING_全称量化
|member|team_id|status|
|:----|:----|:----|
|アレン|5|出動中|
|カレン|2|出動中|
|キース|2|休暇|
|ケーガン|5|待機|
|ケン|1|出動中|
|ジャン|3|待機|
|ジョー|1|待機|
|ディック|3|待機|
|ハート|3|待機|
|ベス|4|待機|
|ミック|1|待機|
|ロバート|5|休暇|

EX) 出動可能なメンバーを求める(チーム全員が待機状態にあること)  
「チーム全員が待機状態」<=>「チーム内に待機状態でないメンバーが一人もいない」  
``` sql
SELECT team_id,member FROM Chapter6Teams AS t1
WHERE
	NOT EXISTS(
		SELECT * FROM Chapter6Teams AS t2
		WHERE
			t1.team_id = t2.team_id
		AND
			t2.status <> '待機')

-- result
team_id	member
3	ジャン
3	ディック
3	ハート
4	ベス
```
上記はパフォーマンスはいいが二重否定分で分かりにくい、しかしHAVING句を使うと肯定文で簡単に書ける
``` sql
SELECT team_id FROM Chapter6Teams AS t1
GROUP BY
	team_id
HAVING
	COUNT(*) = SUM(
		CASE
			WHEN status = '待機' THEN 1
			ELSE 0
		END)

-- result
team_id
3
4
```
インデックスが利用できる極値関数でパフォーマンスがより良い別解が以下である  
全員が同じステータスなら最大の要素と最小の要素が必ず一致するため
``` sql
SELECT team_id FROM Chapter6Teams AS t1
GROUP BY
	team_id
HAVING
	MAX(status) = '待機'
AND
	MIN(status) = '待機'
```
さらにCASE式を使って、チームごとの状態を一覧化することもできる
``` sql
SELECT
	team_id
	,CASE
		WHEN MAX(status) = '待機'
		AND MIN(status) = '待機'THEN 'スタンバイ'
		ELSE
			'メンバーが足りません'
	END current_status
FROM
	Chapter6Teams AS t1
GROUP BY
	team_id

-- result
team_id	current_status
1	メンバーが足りません
2	メンバーが足りません
3	スタンバイ
4	スタンバイ
5	メンバーが足りません
```

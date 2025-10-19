# EXISTS_実践編2
## 全称量化-集合 vs 述語
EXISTSとHAVINGは集合レベルの操作を行う点で似ており、けっこう互換性がある  
プロジェクトの工程管理があり、各工程は完了、前工程の終了待ちなら待機というステータスがあるとする  
EX) 1番の工程まで完了しているプロジェクトを選択する
|project_id|step_nbr|status|
|:----|:----|:----|
|AA100|0|完了|
|AA100|1|待機|
|AA100|2|待機|
|B200|0|待機|
|B200|1|待機|
|CS300|0|完了|
|CS300|1|完了|
|CS300|2|待機|
|CS300|3|待機|
|DY400|0|完了|
|DY400|1|完了|
|DY400|2|完了|

``` sql
-- HAVING
SELECT project_id FROM Chapter5Projects
GROUP BY
	project_id
HAVING
	COUNT(*) = SUM(
		CASE
			WHEN step_nbr <= 1 AND status = '完了' THEN 1
			WHEN 1 < step_nbr AND status = '待機' THEN 1
		ELSE
			0
		END)

-- result
project_id
CS300
```
例題を全称命題とするなら以下のようになる  
プロジェクト内全行について、行程番号が1以下ならば完了であり、1より大きければ待機である  
HAVINGと比べて直感的に分かりにくくなるのがNOT EXISTSだが、以下のメリットがある
- 1つでも条件不一致な行があれば検索を打ち切る + インデックスを利用できるためパフォーマンスがよい
- HAVINGは集約されてしまうが、project_id以外の列も取れるため、結果の情報量が多い
``` sql
SELECT * FROM Chapter5Projects as p1
WHERE
	NOT EXISTS(
		SELECT * FROM Chapter5Projects as p2
		WHERE
			p1.project_id = p2.project_id
		AND
			p2.status <> CASE
							WHEN p2.step_nbr <= 1 THEN '完了'
							ELSE '待機'
						 END)

-- result
project_id	step_nbr	status
CS300	0	完了
CS300	1	完了
CS300	2	待機
CS300	3	待機
```

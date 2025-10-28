# 集約関数
集約関数の中にもCASE式を書くことができる = HAVINGを使わなくてもよくなる
``` SQL
SELECT std_id, MAX(club_id) FROM StudentClub GROUP BY std_id HAVING COUNT(*) = 1;
SELECT std_id, club_id FROM StudentClub WHERE main_club_flg = 'Y';

-- 上2行をCASEを使って1つのSQLに
SELECT
	std_id
	,CASE WHEN COUNT(*) = 1 THEN MAX(club_id)
	 ELSE MAX(CASE WHEN main_club_flg = 'Y' THEN club_id ELSE NULL END)
	 END
FROM
	StudentClub
GROUP BY
	std_id

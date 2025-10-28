# CHECK制約
[CHECK制約とは](https://learn.microsoft.com/ja-jp/sql/relational-databases/tables/unique-constraints-and-check-constraints?view=sql-server-ver17)
> CHECK 制約は、1 つ以上の列に格納できる値を制限することによってドメインの整合性を強制します。 論理演算子に基づいて CHECK または TRUE を返す論理 (ブール) 式を使って FALSE 制約を作成できます。
CASEはCHECK制約と相性がよい
- 条件法 = PならばQ(P → Q)
- 論理積 = PかつQ(P ^ Q)
``` SQL
-- 条件法
ALTER TABLE test
ADD	CONSTRAINT check_salary CHECK
	(CASE
		WHEN sex = '2' THEN
		CASE
			WHEN salary <= 200000 THEN 1
			ELSE 0
		END
		ELSE 1
	 END = 1)

-- 論理積
ALTER TABLE test
ADD	CONSTRAINT check_salary CHECK
	(sex = '2' AND salary <= '200000')
```
上記2つのSQLでは意味が異なる  
-> 論理積だとsexが2でないとそもそもデータが登録できない制約を付けることになる  
- 条件法 => P(sexが2であること)が真ならばQ(20万以下のサラリー)も真である、またはPが偽または不明であればTRUE
- 論理積 => P(sexが2であること)とQ(20万以下のサラリー)共に真であること、またはPとQどちらかが不明かつどちらかが真であること
-> 片方が偽になる場合はもう一方が不明でもFALSE

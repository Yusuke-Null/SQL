# CHECK制約
CHECK制約とは
> CHECK 制約は、1 つ以上の列に格納できる値を制限することによってドメインの整合性を強制します。 論理演算子に基づいて CHECK または TRUE を返す論理 (ブール) 式を使って FALSE 制約を作成できます。
CASEはCHECK制約と相性がよい
``` SQL
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
```

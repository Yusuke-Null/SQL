# WHERE句に書ける条件をHAVING句に書かない
``` sql
-- HAVING句
SELECT
	sale_date
	,SUM(quantity)
FROM
	Chapter8SalesHistory
GROUP BY
	sale_date
HAVING
	sale_date = '2018-10-01'

-- WHERE句
SELECT
	sale_date
	,SUM(quantity)
FROM
	Chapter8SalesHistory
WHERE
	sale_date = '2018-10-01'
GROUP BY
	sale_date

-- result
sale_date	(列名なし)
2018-10-01	17
```
上記の2つのクエリが返す結果は同じであるが、WHERE句で条件指定した方が効率よく動作する  
1. GROUP BY句による集約はソートやハッシュの演算を行うため、できるだけ事前に行数を絞り込むとソートの負荷が軽減される
2. うまくいけば、WHERE句の条件でインデックスが利用できる  
-> 集約後のビューは元テーブルのインデックスまで引き継がないケースが多い

### GROUP BY句とORDER BY句でインデックスを使う
GROUP BY句とORDER BY句は普通、並べ替えのソートを行うが  
インデックスの存在する列をキーに指定することでソートの為の検索を高速化できる  
特に、ユニークインデックスを持つ列を指定した場合、ソート自体をスキップできる実装もある  
-> 使っている実装がこの機能をサポートしているかどうか確認するとよい

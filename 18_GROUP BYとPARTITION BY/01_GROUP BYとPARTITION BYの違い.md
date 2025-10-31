# GROUP BYとPARTITION BYの違い
両者はテーブルを指定されたキーで分割する働きをする  
違いはGROUP BYの場合、分割後に集約して1行にまとめる操作が入ることだけ  
Q. 下記のテーブルをteamで分割し、年齢でソートしてみる
|memger|team|age|
|:----|:----|:----|
|大木|A|28|
|逸見|A|19|
|新藤|A|23|
|山田|B|40|
|久本|B|29|
|橋田|C|30|
|野々宮|D|28|
|鬼塚|D|28|
|加藤|D|24|
|新城|D|22|

PARTITION BY
``` sql
SELECT memger,team,age
	,RANK() OVER(PARTITION BY team
		ORDER BY age DESC) AS rn
	,DENSE_RANK() OVER(PARTITION BY team
		ORDER BY age DESC) AS dense_rn
	,ROW_NUMBER() OVER(PARTITION BY team
		ORDER BY age DESC) AS row_num
FROM
	Chapter18Teams
ORDER BY
	team,rn

-- result
memger	team	age	rn	dense_rn	row_num
大木	A	28	1	1	1
新藤	A	23	2	2	2
逸見	A	19	3	3	3
山田	B	40	1	1	1
久本	B	29	2	2	2
橋田	C	30	1	1	1
野々宮	D	28	1	1	1
鬼塚	D	28	1	1	2
加藤	D	24	3	2	3
新城	D	22	4	3	4
```
パーティションカットのイメージで作成した部分集合には以下の性質がある
- いずれも空集合ではない
※ NULLだけの部分集合もありえるが空集合とは別物、またNULLを含んでいたらNULLも類別のキーになる  
- すべての部分集合の和が、分割する前の集合と一致する  
- 互いに異なる任意の2つの部分集合が共通部分を持たない  

列のキーで分割したのだから、分割後に行方不明になるデータはなく  
2つの部分集合に同時に属するコウモリのようなデータはなく、必ず1つの集合に割り当てられる  
**つまり、GROUP BYやPARTITION BYは各メンバーをチームに割り当てる関数ということもできる**
数学では上記3つの性質を持つ集合の1つ1つを「類(partition)」と呼ぶ  
また、元の集合を小さな類へカットする操作を「類別」と呼ぶ

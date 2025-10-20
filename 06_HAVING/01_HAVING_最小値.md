# HAVING_最小値
## データの歯抜けを探す
EX) 下記のseqの歯抜けとなった歯抜けを探す
|seq|name|
|:----|:----|
|1|ディック|
|2|アン|
|3|ライル|
|5|カー|
|6|マリー|
|8|ベン|

テーブルがファイルであるとして、手続き型言語の考えで探すと下記の手順となる
- 連番の昇順または降順でソート
- ソートキーの昇順(降順)にループさせ、1行ずつ次の行のseqと比較する

ファイルのレコードは順序をもつが、テーブルの行は順序を持たなければソートの演算子も持たない  
-> ORDER BYはSQLの演算子でなく、カーソルの定義の一部  
よってテーブル全体を1つの集合として解決する
``` sql
SELECT '歯抜けあり' FROM Chapter6SeqTbl
HAVING COUNT(*) <> MAX(seq)

-- result
(列名なし)
歯抜けあり
```
COUNTと連番の最大値が同じなら抜けなくカウントアップできたことになるから
-> **集合論的には自然数の酒豪とSeqTbl集合の愛大に一対一対応(全単射)があるかをテストしている**  
- MAX(seq): seqの最大値までの歯抜けのない連番(自然数)の集合の要素数をカウント
- COUNT(*): SeqTblに実際に含まれている要素数(行数)をカウント

GROUP BYがない場合はテーブル全体が1行に集約される  
-> SELECTで元テーブルの列が参照できないため、定数かCOUNT(*)のような集約関数を使う必要がある

## 歯抜けの最小値を探す
前述の「HAVING COUNT(*) <> MAX(seq)」はseqが1から始まるという前提だから成り立つため不十分  
-> 3,4,5,6,7,8といった連番では「6 <> 8」と比較され、連番であるが歯抜けと判定される
よってseq + 1の内、seqに含まれない番号の最小値を探せばよい
|seq|name|
|:----|:----|
|3|ディック|
|4|アン|
|7|ライル|
|8|カー|
|10|マリー|
|11|ベン|

``` sql
SELECT MIN(seq + 1) as min_value FROM Chapter6SeqTbl2
WHERE
	(seq + 1) NOT IN (SELECT seq FROM Chapter6SeqTbl2)

-- result
min_value
5
```
しかし、本来は1を歯抜けの最小値であるため、まだ完全ではない

### 最小値の完全版
下記すべてに対応するSQLが必要
- 1から歯抜けなしの連番
- 1から始まり、歯抜けありの連番
- 1より大きい数から始まる連番
- 1より大きい数から始まり、歯抜けありの連番

まずは条件を緩くして、下限の値は問わないパターンを考える  
-> 最大値最大値 - 最小値 + 1で解決できる
``` sql
SELECT
	CASE
		WHEN COUNT(*) = 0 THEN '空'
		WHEN COUNT(*) <> MAX(seq) - MIN(seq) + 1 THEN '歯抜けあり'
		ELSE
			'連番'
	END AS min_value FROM Chapter6SeqTbl2
HAVING
	COUNT(*) <> MAX(seq) - MIN(seq) + 1

min_value
歯抜けあり
```
１が開始値でない場合にも適用させる
- COUNT(*) = 0: テーブルが空の場合の対応
- 1 < MIN(seq): 下限が1でない場合の対応
- NOT EXISTS: 1から始まり、かつ歯抜けの最小値を探す対応
``` sql
SELECT
	CASE
		WHEN COUNT(*) = 0 OR 1 < MIN(seq) THEN 1
		ELSE(
			SELECT MIN(seq + 1) FROM Chapter6SeqTbl2 AS s1
			WHERE
				NOT EXISTS(
					SELECT * FROM Chapter6SeqTbl2 AS s2
					WHERE
						s1.seq + 1 = s2.seq
				))
	END AS min_value
FROM
	Chapter6SeqTbl2

-- result
min_value
1
```








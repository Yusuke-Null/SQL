# NULL_実践編続
## CASE式とNULL
以下は「×」を絶対に返さない  
-> WHEN NULL THEN '×'は「col_1 = NULL」の省略だから  
**※NULLは値ではない**
``` sql
CASE col_1
  WHEN 1 THEN '〇'
  WHEN NULL THEN '×'
END
```
よって以下のようにしないといけない
``` sql
CASE
  WHEN col_1 = 1 THEN '〇'
  WHEN col_1 IS NULL THEN '×'
END
```
## NOT INとNOT EXISTSは同値ではない
INをEXISTSに書き換えることは問題ないチューニングである  
ただ、NOT INとNOT EXISTSは必ずしも結果が一致するわけではない
Class_A
|name|age|city|
|:----|:----|:----|
|ブラウン|22|東京|
|ラリー|19|埼玉|
|ボギー|21|千葉|
Class_B
|name|age|city|
|:----|:----|:----|
|斎藤|22|東京|
|田尻|23|東京|
|山田|NULL|東京|
|和泉|18|千葉|
|武田|20|千葉|
|石川|19|神奈川|

EX) Bクラスの東京在住の生徒と年齢が一致しないAクラスの生徒を抽出する

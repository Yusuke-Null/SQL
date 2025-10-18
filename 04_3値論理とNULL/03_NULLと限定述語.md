# NULL実践編続
## 限定述語とNULL
SQLにはALLとANYの2つの限定述語を持っている。ANYはINTと同値のためあまり使われない  
EX) Bクラスの東京在住の誰よりも若いAクラスの生徒を取得する  
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
|山田|20|東京|
|和泉|18|千葉|
|武田|20|千葉|
|石川|19|神奈川|

``` sql
SELECT * FROM Chapter4Class_A
WHERE
	age < ALL(
		SELECT age FROM Chapter4Class_B
		WHERE
			Chapter4Class_B.city = '東京')

-- result
name	age	city
ラリー	19	埼玉
```
ただし、最も若い山田のageがNULLの場合は結果は空欄になる

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
1. 「age < ALL(22, 23, NULL)」
1. ALLをANDに同値変換「(age < 22) AND (age < 23) AND (age < NULL)」
1. NULLに<を適用すると「(age <> 22) AND (age <> 23) AND (age <> unknown)」
1. AND演算にunknownがあるとtrueにならない

## 限定述語と極値関数は同値でない
極値関数でSQLを書きなおす  
※Class_Bの山田のageをNULLに修正した
``` sql
SELECT * FROM Chapter4Class_A
WHERE
	age < (
		SELECT MIN(age) FROM Chapter4Class_B
		WHERE
			Chapter4Class_B.city = '東京')

-- result
name	age	city
ラリー	19	埼玉
ボギー	21	千葉
```
限定述語のALLとは異なり極値関数MINではNULLを含んでいても次点の斎藤のage22で判定してくれる  
-> **極値関数が集計時にNULLを排除する特性があるため = NULLがなかったかのように扱う**  
限定述語(ALL)と極値関数を命題は以下である  
- ALL: 彼は東京在住の生徒の誰よりも若い
- 極値関数: 彼は東京在住の生徒の最も若い生徒よりも若い

現実では同じことをいっているが、NULLが含まれていると意味が異なる  

さらに述語(関数)の入力が空集合の場合にも同値でなくなる  
Class_B(東京在住者がいないケース)
|name|age|city|
|:----|:----|:----|
|和泉|18|千葉|
|武田|20|千葉|
|石川|19|神奈川|

- ALL: Aクラス全員が選択される
- 極値関数: 誰も選択されない
``` sql
-- ALL
SELECT * FROM Chapter4Class_A
WHERE
	age < ALL (
		SELECT age FROM Chapter4Class_B_NotToKyo
		WHERE
			Chapter4Class_B_NotToKyo.city = '東京')

-- result
name	age	city
ブラウン	22	東京
ラリー	19	埼玉
ボギー	21	千葉

-- 極値関数
SELECT * FROM Chapter4Class_A
WHERE
	age < (
		SELECT MIN(age) FROM Chapter4Class_B_NotToKyo
		WHERE
			Chapter4Class_B_NotToKyo.city = '東京')

-- result
name	age	city
```
条件が「age < NULL」となり、結果unknownになる
**極値関数は空集合の場合はnullを返す仕様の為**  
全件返すのか1行も返さないのか、仮に全行返すならALLを使うか、COALSECE関数でNULLを置換して極値関数を使うかは要件による



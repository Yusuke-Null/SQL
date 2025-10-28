# DISTINCTをEXISTSで代用する
DISTINCTも重複排除のためにソートを行う  
Chapter8SalesHistoryとItemsテーブル
| 販売実績テーブル       |        |         |   | 商品マスタ |           |
|------------------------|--------|---------|---|------------|-----------|
| sale_date              | item_no| quantity|   | item_no    | item      |
| 2018-10-01             | 10     | 4       |   | 10         | SDカード   |
| 2018-10-01             | 20     | 10      |   | 20         | CD-R      |
| 2018-10-01             | 30     | 3       |   | 30         | USBメモリ |
| 2018-10-03             | 10     | 32      |   | 40         | DVD       |
| 2018-10-03             | 30     | 12      |   |            |           |
| 2018-10-04             | 20     | 22      |   |            |           |
| 2018-10-04             | 30     | 7       |   |            |           |


``` sql
-- 内部結合
SELECT i.item_no,item FROM Chapter8Items AS i
INNER JOIN
	Chapter8SalesHistory AS s
ON
	i.item_no = s.item_no

-- result
item_no	item
10	SDカード
20	CD-R
30	USBメモリ
10	SDカード
30	USBメモリ
20	CD-R
30	USBメモリ

-- DISTINCT使用
SELECT DISTINCT i.item_no,item FROM Chapter8Items AS i
INNER JOIN
	Chapter8SalesHistory AS s
ON
	i.item_no = s.item_no

-- result
item_no	item
10	SDカード
20	CD-R
30	USBメモリ
```
2つのテーブルを一位にするためにDISTINCTを行っている場合はEXISTSで代用できる  
下記であればソートも発生せず、最適解になる。EXISTSは結合に劣らず高速に動作する
``` sql
SELECT i.item_no,item FROM Chapter8Items AS i
WHERE
	EXISTS(
		SELECT * FROM Chapter8SalesHistory AS s 
		WHERE
			i.item_no = s.item_no)

```

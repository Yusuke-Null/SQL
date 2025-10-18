# 3値論理とNULL基本
DBにnullが1つでも含まれていればクエリから正しくない結果が返される可能性がある。  
加えて、どのクエリから正しくない結果が返されるか知る方法はないため  
nullが含まれたDBから正しい結果が得られると確認できない  
SQLではnullを持ち込んだために、true,false,unknownの3値論理になった  

## 理論編
NULLは2種類に分けて考える
- 未知(unknown): 現在不明だが、条件がそろえばわかる
- 適用不能(applicable,inapplicable): 論理的に不可能、どう頑張ってもわからない
### なぜ = NULLでなく、IS NULLと書くのか？
**NULLは値でも変数でもなく、値がないことを示すマークにすぎないため**  
-> 比較述語(=,<,>,<=,>=,<>)を適用できるのは値だけ
NULLに比較述語を適用すると常に結果がunknownになるから  
1 = NULL, 2 > NULLなど全てunknownに評価される
### unknown、第三の真理値
3値の真理値の優先順位(強い方が優先される)
- AND: false > unknown > true
- OR: true > unknown > false

## 実践編
命題とその否定をまたはで繋げる命題は全て真という命題は2値論理で排中律と呼ぶ  
-> 中間がなく、白黒はっきり命題の真偽が定まる  
ex「ジョンは20歳か20歳でないかのどちらかだ」という命題は現実では正しい  
例の年齢の条件をSQLに起こして実行してみると(以下の表はStudentsテーブル)  
ジョンのageはNULLのため、比較述語で見てみるとNULL = 20 OR NULL <> 20(unknown OR unknown)となる  
-> SQLの選択結果はtrueと評価されるレコードのみのため、ジョンは出力されない
| name | age |
| :--: | :--: |
| ブラウン	| 22 |
| ラリー	 | 19 |
| ジョン	 | NULL |
| ボギー	 | 21 |
```sql
SELECT * FROM Students
WHERE age = 20 OR age <> 20;

-- result
name	age
ブラウン	22
ラリー	19
ボギー	21
```
ジョンの結果を表示するには第3の条件IS NULLが必要
``` sql
SELECT * FROM Chapter4Students
WHERE age = 20 OR age <> 20 OR age IS NULL;
name	age
ブラウン	22
ラリー	19
ジョン	NULL
ボギー	21
```
ジョンは現実では年齢があるはずだが、テーブルの利用者は何歳かを知らない  
-> 現実では正しいこともSQLでは正しくないことが起こる  
つまり、関係モデルは現実に対する人間の認識状態を記述する心のモデルである

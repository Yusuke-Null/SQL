# 条件分岐をさせたUPDATE
更新条件が分岐する場合もSet句でCASEが使える  
下記条件を2度のUPDATE文で行うと0.9倍した給料が2の条件に該当し、1.2倍されてしまうため正しくない  
-> 増減の条件をCASE式にする
例)
1. 30万以上なら10%減
2. 25万以上かつ28万未満なら20%増
``` SQL
UPDATE test
SET salary = CASE
        				WHEN 300000 <= salary THEN salary * 0.9
        				WHEN 250000 <= salary AND salary < 280000 THEN salary * 1.2
        				ELSE salary -- ELSE句がないと、該当しなかった人の給料が全員NULLになる
      			　END;
```

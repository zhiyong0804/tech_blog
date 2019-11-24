# SQL99连接

#### 等值连接
```
SELECT player_id, a.team_id, player_name, height, team_name FROM player as a, team as b WHERE a.team_id = b.team_id
SELECT player_id, team_id, player_name, height, team_name FROM player NATURAL JOIN team 

```

#### Using连接
* USING 指定数据表里的同名字段进行等值连接

```
SELECT player_id, team_id, player_name, height, team_name FROM player JOIN team USING(team_id)
```

#### select执行语句顺序
1、FROM子句组装数据（包括通过ON进行连接）
2、WHERE子句进行条件筛选
3、GROUP BY分组 
4、使用聚集函数进行计算； 
5、HAVING筛选分组； 
6、计算所有的表达式； 
7、SELECT 的字段；
8、ORDER BY排序
9、LIMIT筛选
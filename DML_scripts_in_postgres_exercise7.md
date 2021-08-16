<h1 align="center"> ДЗ 7 </h1>
<h1 align="center"> DML агрегация и сортировка, CTE, аналитические функции </h1>

---

```sql

CREATE TABLE IF NOT EXISTS statistic
( player_name VARCHAR(100) NOT NULL,
  player_id INT NOT NULL,
  year_game SMALLINT NOT NULL CHECK (year_game > 0),
  points DECIMAL(12,2) CHECK (points >= 0),
  PRIMARY KEY (player_name,year_game));

INSERT INTO statistic
( player_name, player_id, year_game, points)
VALUES ('Mike',1,2018,18),
       ('Jack',2,2018,14),
       ('Jackie',3,2018,30),
       ('Jet',4,2018,30),
       ('Luke',1,2019,16),
       ('Mike',2,2019,14),
       ('Jack',3,2019,15),
       ('Jackie',4,2019,28),
       ('Jet',5,2019,25),
       ('Luke',1,2020,19),
       ('Mike',2,2020,17),
       ('Jack',3,2020,18),
       ('Jackie',4,2020,29),
       ('Jet',5,2020,27);

--Запрос суммы очков с группировкой и сортировкой по годам
SELECT year_game, sum(points) FROM statistic
GROUP BY year_game
ORDER BY year_game;

--CTE + оконые функции (Запрос суммы очков с группировкой и сортировкой по годам)
WITH cte_statistic AS  (
    SELECT player_name, player_id, year_game, points,
    SUM(points) OVER(PARTITION BY year_game ORDER BY year_game) AS "sum_points"
    FROM statistic
)
SELECT year_game, sum_points FROM cte_statistic
GROUP BY year_game, sum_points
ORDER BY year_game, sum_points;

--lag() вывести кол-во очков по всем игрокам за текущий код и за предыдущий
WITH cte_statistic AS  (
    SELECT player_name, player_id, year_game, points,
    SUM(points) OVER(PARTITION BY year_game) AS "sum_points"
    FROM statistic
)
SELECT year_game, sum_points,
lag(sum_points) OVER (ORDER BY year_game) AS "previous_year_sum_points"
FROM cte_statistic
GROUP BY year_game, sum_points
ORDER BY year_game;

```


| Database   | ver |
| -----      | --- |
| PostgreSQL | 13.3|

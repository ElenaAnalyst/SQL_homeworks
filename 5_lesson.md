## 1 задание 

Написать SQL-запрос, который выведет названия сериалов, у которых первый эпизод первого сезона получил рейтинг 10.  
Для выборки используйте таблицы `titles`, `ratings`, `episodes` и коррелированные подзапросы. 

Результат отсортируйте в лексикографическом порядке.

```sql
SELECT gr.name
FROM   (SELECT (SELECT t.original_title
                        FROM   titles as t
                        WHERE  t.id = e.parent_id) as name, (SELECT r.avg_rating
                                             FROM   ratings as r
                                             WHERE  r.title_id = e.title_id) as avg_rating
        FROM   episodes as e
        WHERE  e.episode = 1
           and e.season = 1) as gr
WHERE  gr.avg_rating = 10
   and name is not null
ORDER BY 1;
```

## 2 задание 

Написать SQL-запрос, который выведет оригинальное название `(original_title)`, год начала `(year_of_start)` и количество эпизодов произведений, у которых больше 500 серий, отсортировав их по убыванию количества серий. Для этого используйте подзапрос к таблице `episodes` с группировкой по полю `parent_id`. Выведите только строки, которые есть в таблице `titles` и в сгруппированной таблице.
Назовите поле с количеством эпизодов произведений `ep_cnt`. Отсортируйте результат по этому полю в порядке убывания.

```sql
SELECT t.original_title,
       t.year_of_start,
       e.ep_cnt
FROM   titles as t join (SELECT parent_id,
                                count(1) as ep_cnt
                         FROM   episodes as e
                         GROUP BY parent_id having count(1) > 500) as e
        ON t.id = e.parent_id
ORDER BY ep_cnt desc;
```

## 3 задание 

Написать SQL-запрос, который выведет оригинальное название `(original_title)`, год начала `(year_of_start)` и количество человек в команде `(com_cnt)`, у которых команда больше 30 человек, отсортировав их по убыванию количества людей. Для этого используйте подзапрос к таблице `principals` с группировкой. Выведите только строки, которые есть в таблице `titles` и в сгруппированной таблице.

```sql
SELECT t.original_title,
       t.year_of_start,
       e.com_cnt
FROM   titles as t join (SELECT title_id,
                                count(1) as com_cnt
                         FROM   principals as e
                         GROUP BY title_id having count(1) > 30) as e
        ON t.id = e.title_id
ORDER BY com_cnt desc;
```

## 4 задание 

Написать SQL-запрос, который выведет:

- количество строк в таблице `titles`;
- количество уникальных записей в поле `type` таблицы `titles`;
- общее количество голосов `vote_cnt` в таблице `ratings`.

```sql
SELECT count(1) as title_cnt,
       count(distinct type) as type_cnt,
       (SELECT sum(vote_cnt)
 FROM   ratings) as sum_vote
FROM   titles;
```

## 5 задание 

Написать SQL-запрос, который с помощью коррелированного подзапроса выведет:

- `id` и `original_title` из таблицы `titles`;
- `avg_rating` из таблицы `ratings`, относящийся к строке из `titles`.

Результат отсортируйте по возрастанию `id`, `original_title`, `avg_rating`.

```sql
SELECT id,
       original_title,
       (SELECT avg_rating
 FROM   ratings as r
 WHERE  r.title_id = t.id)
FROM   titles as t
ORDER BY 1, 2, 3;
```

## 6 задание 

Написать SQL-запрос, который с помощью подзапроса в блоке `WHERE` выведет строку таблицы `titles`, у которой максимальный рейтинг (`avg_rating` из таблицы `ratings`) и максимальное количество голосов `(vote_cnt)` среди других произведений с тем же рейтингом.

```sql
SELECT *
FROM   titles
WHERE  id in (SELECT title_id
              FROM   ratings
              ORDER BY avg_rating desc, vote_cnt desc limit 1);           
```

## 7 задание 

Написать написать SQL-запрос, который выведет название `(original_title)` произведения с максимальной продолжительностью. 
Если таких произведений несколько, то выведите все.

```sql
SELECT original_title
FROM   titles
WHERE  run_time = (SELECT max(titles.run_time)
                   FROM   titles);
```

## 8 задание 

Написать SQL-запрос, который выведет названия `(original_title)` произведений, продолжительностью между `floor` и `ceil` средней продолжительности произведений в таблице `titles`.

Результат отсортируйте по возрастанию `original_title`.

```sql
SELECT original_title
FROM   titles
WHERE  run_time between (SELECT floor(avg(titles.run_time))
                         FROM   titles) and (SELECT ceil(avg(titles.run_time))
                    FROM   titles)
ORDER BY 1;
```

## 9 задание 

Выберите имена (без фамилий) из таблицы `persons`, которых в таблице `persons` больше 1000. Выведите строки из таблицы `persons` про людей с такими именами.

Результат отсортируйте по возрастанию `id`.

```sql
SELECT *
FROM   persons
WHERE  split_part(name, ' ', 1) in (SELECT split_part(name, ' ', 1)
                                    FROM   persons
                                    WHERE  position(' ' in name) > 0
                                    GROUP BY 1 having count(1) > 1000)
ORDER BY 1;
```

## 10 задание 

Написать SQL-запрос, который выведет тип `(type)`, оригинальное название `(original_title)`, год выпуска `(year_of_start)` и жанр `(genres)` произведений для взрослых `(is_adult)`, отсортировав их по убыванию рейтинга `(avg_rating в таблице ratings)`. Для этого используйте коррелированный подзапрос в секции сортировки.

```sql
SELECT type,
       original_title,
       year_of_start,
       genres
FROM   titles as t
WHERE  is_adult
ORDER BY (SELECT avg_rating
          FROM   ratings as r
          WHERE  r.title_id = t.id) desc;
```

## 11 задание 

Написать SQL-запрос, который выведет имена людей (поле `name` из таблицы `persons`), которые судя по таблице `principals` участвовали в создании 10 самых высокооценённых произведений (максимум голосов среди тех, кто набрал максимальный рейтинг `(avg_rating)` из таблицы `ratings`). Итоговый результат отсортируйте по `name`.

```sql
WITH the_best_id as (SELECT title_id
                     FROM   ratings
                     ORDER BY avg_rating desc, vote_cnt desc limit 10), prin as (SELECT person_id
                                                            FROM   principals
                                                            WHERE  title_id in (SELECT title_id
                                                                                FROM   the_best_id))
SELECT name
FROM   persons
WHERE  id in (SELECT person_id
              FROM   prin)
ORDER BY name;
```

## 12 задание 

Перепишите SQL-запрос из первого задания с использованием `CTE`. Запрос выводит названия `(original_name)` сериалов, у которых первый эпизод первого сезона получил рейтинг 10. Назовите результирующее поле `name` и отсортируйте результат по его возрастанию.

```sql
WITH cte as (SELECT (SELECT t.original_title
                                  FROM   titles as t
                                  WHERE  t.id = e.parent_id) as name, (SELECT r.avg_rating
                                                  FROM   ratings as r
                                                  WHERE  r.title_id = e.title_id) as avg_rating
             FROM   episodes as e
             WHERE  e.episode = 1
                and e.season = 1)
SELECT name
FROM   cte
WHERE  avg_rating = 10
   and name is not null
ORDER BY name;
```

## 13 задание 

Перепишите SQL-запрос из второго задания с использованием `CTE`. Запрос выводит оригинальное название `(original_title)`, год начала `(year_of_start)` и количество эпизодов произведений `(ep_cnt)`, у которых больше 500 серий, отсортировав их по убыванию количества серий. 
Выводятся только те строки, которые есть в таблице `titles` и в сгруппированной таблице.

```sql
WITH cte as (SELECT parent_id,
                    count(1) as ep_cnt
             FROM   episodes as e
             GROUP BY parent_id having count(1) > 500)
SELECT t.original_title,
       t.year_of_start,
       cte.ep_cnt
FROM   titles as t join cte
        ON t.id = cte.parent_id
ORDER BY ep_cnt desc;
```

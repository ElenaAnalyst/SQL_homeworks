## 1 задание 

Написать SQL-запрос, который выведет:

- название сериала;
- название эпизода, для этого необходимо выполнить коррелированный подзапрос, обратившись к внешней таблице `episodes`;
- номер сезона;
- номер эпизода в сезоне;
- сквозной номер (назовите поле `rn`).
  
Все названия хранятся в поле `original_title` таблицы `titles`. Партицирование сделайте по `id` названия сериала.

Результат отсортируйте по возрастанию `serial_name`, `episode_name`, `season`, `episode`, `rn`.

```sql
with cte as (SELECT t.original_title as serial_name,
                    (SELECT t.original_title
              FROM   titles as t
              WHERE  t.id = e.title_id) as episode_name, e.parent_id, e.season, e.episode
             FROM   episodes as e join titles as t
                     ON t.id = e.parent_id)
SELECT serial_name,
       episode_name,
       season,
       episode,
       row_number() OVER (PARTITION BY parent_id
                          ORDER BY season, episode) as rn
FROM   cte
ORDER BY 1, 2, 3, 4, 5;
```

## 2 задание 

Написать SQL-запрос, который выведет количество умерших людей из таблицы `persons` для каждого года, начиная с 2000-го, накопленным итогом.
Например, для 2012 года будет указано количество людей, которые ушли из жизни с 2000 по 2012 год.

Поля к выводу: год `(year_of_death)`, кумулятивная сумма на этот год `(cume_cnt)`. Отсортируйте результат по их возрастанию.

```sql
with death_stat as (SELECT year_of_death,
                           count(1) as dead_cnt
                    FROM   persons
                    WHERE  year_of_death >= 2000
                    GROUP BY year_of_death)
SELECT year_of_death,
       sum(dead_cnt) OVER (ORDER BY year_of_death) as cume_cnt
FROM   death_stat
ORDER BY 1, 2;
```

## 3 задание 

Написать SQL-запрос, который выведет:

- `year_of_start` из таблицы `titles`;
- количество произведений по годам накопленным итогом, набравших максимальный средний рейтинг, равный 10.

Для выполнения запроса используйте таблицы `titles` и `ratings`. Поля к выводу: год `(year_of_start)`, кумулятивная сумма `(cum_cnt)`. Отсортируйте результат по их возрастанию.

```sql
with cte as (SELECT year_of_start,
                    count(1) as ten_cnt
             FROM   titles as t join ratings as r
                     ON t.id = r.title_id
             WHERE  avg_rating = 10
             GROUP BY year_of_start)
SELECT year_of_start,
       sum(ten_cnt) OVER (ORDER BY year_of_start) as cume_cnt
FROM   cte
ORDER BY 1, 2;
```

## 4 задание 

Написать SQL-запрос, который выведет:

- название сериала;
- название эпизода;
- номер сезона;
- номер эпизода в сезоне;
- максимальный рейтинг эпизодов до этой серии включительно.

Серии с пустыми значениями сезона и эпизода показывать не нужно. Результат отсортируйте по всем колонкам в том порядке, в котором они расположены выше (сортировка в порядке возрастания).

```sql
with cte as (SELECT t.original_title as serial_name,
             (SELECT t.original_title
              FROM   titles as t
              WHERE  t.id = e.title_id) as episode_name, e.parent_id, e.season, e.episode, r.avg_rating
             FROM   episodes as e join ratings as r
                     ON e.title_id = r.title_id join titles as t
                     ON t.id = e.parent_id)
SELECT serial_name,
       episode_name,
       season,
       episode,
       max(avg_rating) OVER (PARTITION BY parent_id
                             ORDER BY season, episode) as max_rating
FROM   cte
WHERE  season is not null
   and episode is not null
ORDER BY 1, 2, 3, 4, 5;
```

## 5 задание 

Вывести:

- название сериала;
- название эпизода;
- номер сезона;
- номер эпизода в сезоне;
- количество голосов, отданных за сериал до текущего эпизода включительно (назовите поле `vote_cnt`).

Результат отсортируйте по возрастанию `serial_name`, `episode_name`, `season`, `episode`, `vote_cnt`.

```sql
WITH cte AS (
    SELECT t.original_title AS serial_name,
           (SELECT t.original_title FROM titles AS t WHERE t.id = e.title_id) AS episode_name,
           e.parent_id, e.season, e.episode, r.vote_cnt
      FROM episodes AS e
      JOIN ratings AS r
        ON e.title_id = r.title_id
      JOIN titles AS t
        ON t.id = e.parent_id)
SELECT serial_name, episode_name, season, episode,
       SUM(vote_cnt) OVER (PARTITION BY parent_id ORDER BY season, episode) AS vote_cnt
  FROM cte
ORDER BY 1, 2, 3, 4, 5;
```

## 6 задание 

Вывести:

- название сериала;
- название эпизода;
- номер сезона;
- номер эпизода в сезоне;
- расчетное поле: если предыдущий эпизод получил оценку хуже — `better`, если лучше — `worse`, если такую же — `the same`. Для первого эпизода значение принимается NULL. Назовите поле `mark`.

Результат отсортируйте по возрастанию `serial_name`, `season`, `episode`,`episode_name`.

```sql
with cte as (SELECT t.original_title as serial_name,
                    (SELECT t.original_title
              FROM   titles as t
              WHERE  t.id = e.title_id) as episode_name, e.season, e.episode, r.avg_rating, lag(avg_rating)
             OVER (
             PARTITION BY parent_id
             ORDER BY season, episode) as prev_rating
             FROM   episodes as e join ratings as r
                     ON e.title_id = r.title_id join titles as t
                     ON t.id = e.parent_id)
SELECT serial_name,
       episode_name,
       season,
       episode,
       case when prev_rating is null then null
            when prev_rating < avg_rating then 'better'
            when prev_rating = avg_rating then 'the same'
            else 'worse' end as mark
FROM   cte
ORDER BY serial_name, season, episode, episode_name;        
```

## 7 задание 

Вывести:

- название сериала;
- название эпизода;
- номер сезона;
- номер эпизода в сезоне;
- ранг эпизода с пропусками по убыванию среднего рейтинга (назовите поле `rnk`).

Результат отсортируйте по возрастанию `serial_name`, `season`, `episode`, `episode_name`.

```sql
with cte as (SELECT t.original_title as serial_name,
                    (SELECT t.original_title
              FROM   titles as t
              WHERE  t.id = e.title_id) as episode_name, e.parent_id, e.season, e.episode, r.avg_rating
             FROM   episodes as e join ratings as r
                     ON e.title_id = r.title_id join titles as t
                     ON t.id = e.parent_id)
SELECT serial_name,
       episode_name,
       season,
       episode,
       rank() OVER (PARTITION BY parent_id ORDER BY avg_rating desc) as rnk
FROM   cte
ORDER BY serial_name, season, episode, episode_name;
```

## 8 задание 

Вывести:

- название сериала;
- название эпизода;
- номер сезона;
- номер эпизода в сезоне;
- рейтинг этого эпизода;
- максимальный рейтинг эпизодов среди последних 3 эпизодов, включая текущий (назовите поле `max_rating`).

Результат отсортируйте по возрастанию `serial_name`, `season`, `episode`, `episode_name`.

```sql
with cte as (SELECT t.original_title as serial_name,
                    (SELECT t.original_title
              FROM   titles as t
              WHERE  t.id = e.title_id) as episode_name, e.parent_id, e.season, e.episode, r.avg_rating
             FROM   episodes as e join ratings as r
                     ON e.title_id = r.title_id join titles as t
                     ON t.id = e.parent_id)
SELECT serial_name,
       episode_name,
       season,
       episode,
       avg_rating,
       max(avg_rating) OVER (PARTITION BY parent_id
                             ORDER BY season, episode rows between 2 preceding and current row) as max_rating
FROM   cte
ORDER BY serial_name, season, episode, episode_name;
```

## 9 задание 

Напишите SQL-запрос, который выведет количество умерших людей из таблицы `persons` для каждого года, начиная с 2000-го, накопленным итогом для текущего года и всех последующих лет. Например, для 2012 года будет указано количество людей, которые ушли из жизни с 2012 по 2024 год.
Поля к выводу: год `(year_of_death)`, кумулятивная сумма на этот год `(cume_cnt)`.

Результат отсортируйте по возрастанию `year_of_death`, `cume_cnt`.

```sql
with death_stat as (SELECT year_of_death,
                           count(1) as dead_cnt
                    FROM   persons
                    WHERE  year_of_death >= 2000
                    GROUP BY year_of_death)
SELECT year_of_death,
       sum(dead_cnt) OVER (ORDER BY year_of_death rows between current row and unbounded following) as cume_cnt
FROM   death_stat
ORDER BY 1, 2;
```

## 10 задание 

Вывести:

- название сериала;
- название эпизода;
- номер сезона;
- номер эпизода в сезоне;
- сквозной номер (назовите поле `num`);
- максимальный рейтинг эпизодов до этой серии включительно;
- количество голосов, отданных за сериал до этой серии включительно (назовите поле `vote_cnt`).

Используйте `Window Frame`.

```sql
with cte as (SELECT t.original_title as serial_name,
                    (SELECT t.original_title
              FROM   titles as t
              WHERE  t.id = e.title_id) as episode_name, e.parent_id, e.season, e.episode, r.avg_rating, r.vote_cnt
             FROM   episodes as e join ratings as r
                     ON e.title_id = r.title_id join titles as t
                     ON t.id = e.parent_id)
SELECT serial_name,
       episode_name,
       season,
       episode,
       row_number() OVER w as num,
       max(avg_rating) OVER w as max_rating,
       sum(vote_cnt) OVER w as vote_cnt
FROM   cte window w as (
PARTITION BY parent_id
ORDER BY season, episode);
```

## 1 задание 

Написать SQL-запрос, который выведет следующие метрики из таблицы `titles`:

- количество строк;
- минимальное время из поля `run_time`;
- максимальное время из поля `run_time`;
- среднее время из поля `run_time`;
- количество произведений с пометкой для взрослых.

```sql
SELECT count(1) as title_cnt,
       min(run_time) as min_time,
       max(run_time) as max_time,
       avg(run_time) as avg_time,
       sum(is_adult::int) as adult_cnt
FROM   titles;
```

## 2 задание 

Написать SQL-запрос, который выведет следующие метрики из таблицы `persons`:

- количество строк;
- максимальный возраст;
- минимальный возраст;
- средний возраст.

```sql
SELECT count(1) as persons_cnt,
       max(coalesce(year_of_death, 2024) - year_of_birth) as max_age,
       min(coalesce(year_of_death, 2024) - year_of_birth) as min_age,
       avg(coalesce(year_of_death, 2024) - year_of_birth) as avg_age
FROM   persons;
```

## 3 задание 

Написать SQL-запрос, который выведет записи из таблицы `titles`, сгруппированные по полю `type`, и рассчитает, какое количество строк относится к каждому типу. Запрос должен выводить два поля: тип и количество строк cnt.

Результат отсортируйте по возрастанию `type`, `cnt`.

```sql
SELECT type,
       count(1) as cnt
FROM   titles
GROUP BY type
ORDER BY 1, 2;
```

## 4 задание 

Написать SQL-запрос, который выведет количество людей, родившихся в один год, для каждого года рождения из таблицы `persons`. Сделайте сортировку по убыванию количества людей в группах.
Поля вывода: `year_of_birth`; количество людей, рожденных в этом году `(cnt)`.

```sql
SELECT year_of_birth,
       count(1) as cnt
FROM   persons
GROUP BY year_of_birth
ORDER BY cnt desc;
```

## 5 задание 

Написать SQL-запрос, который выведет жанры `genres` и количество строк, соответствующих этим жанрам из таблицы `titles`. 

Для этого:
- произведите группировку по полю `genres`;
- исключите строки, где больше одного жанра (такие строки содержат запятую);
- отсортируйте по убыванию `cnt`, `genres`.

```sql
SELECT genres,
       count(1) as cnt
FROM   titles
WHERE  genres not like '%,%'
GROUP BY genres
ORDER BY cnt desc, genres desc;
```

## 6 задание 

Написать SQL-запрос, который выведет количество людей в каждой из профессий из таблицы `persons`. Исключите тех людей, которые имеют больше одной профессии (аналогично предыдущему заданию). 
Назовите расчетное поле `cnt`. 

Отсортируйте по убыванию `cnt`, `professions`.

```sql
SELECT professions,
       count(1) as cnt
FROM   persons
WHERE  professions not like '%,%'
GROUP BY professions
ORDER BY cnt desc, professions desc;
```

## 7 задание 

Написать SQL-запрос, который выведет количество живых и умерших людей из таблицы `persons`. Если поле `year_of_death` пустое, выведите `alive`, иначе — `dead`. 

Результат отсортируйте по возрастанию `status`, `cnt`.

```sql
SELECT case when year_of_death is null then 'alive'
            else 'dead' end as status,
       count(1) as cnt
FROM   persons
GROUP BY case when year_of_death is null then 'alive' else 'dead' end
ORDER BY 1, 2;
```

## 8 задание 

Написать SQL-запрос, который выведет средний рейтинг каждого из жанров. Для этого:

- объедините таблицы `titles` и `ratings`;
- сгруппируйте результат объединения по полю `genres`;
- отфильтруйте строки с более, чем одним жанром;
- посчитайте среднее значение поля `avg_rating`;
- выведите жанр и средний рейтинг;
- отсортируйте по убыванию среднего рейтинга.

```sql
SELECT genres,
       avg(r.avg_rating) as avg_rating
FROM   titles as t
    LEFT JOIN ratings as r
        ON t.id = r.title_id
WHERE  genres not like '%,%'
GROUP BY genres
ORDER BY avg_rating desc;
```

## 9 задание 

Написать SQL-запрос, который выведет жанр и сумму голосов (`vote_cnt` из таблицы `ratings`) для каждого жанра. Назовите расчетное поле `sum_vote`. Отсортируйте по убыванию сумм голосов. Исключите строки, содержащие более одного жанра, из выгрузки.

```sql
SELECT genres,
       sum(r.vote_cnt) as sum_vote
FROM   titles as t
    LEFT JOIN ratings as r
        ON t.id = r.title_id
WHERE  genres not like '%,%'
GROUP BY genres
ORDER BY sum_vote desc;
```

## 10 задание 

Написать SQL-запрос, который выведет жанры и количество произведений в каждом из них (назовите расчетное поле `cnt`) из таблицы `titles`. Не исключайте составные жанры. Ограничьте список теми жанрами, в которых более 1000 строк, и отсортируйте по убыванию этого количества.

```sql
SELECT genres,
       count(1) as cnt
FROM   titles
GROUP BY genres having count(1) > 1000
ORDER BY cnt desc;
```

## 11 задание 

Написать SQL-запрос, который выведет количество людей (расчетное поле `cnt`) для каждой профессии из таблицы `persons`. Исключите людей с несколькими профессиями. Выведите только те профессии, где количество людей превышает 10000. Отсортируйте результаты по убыванию количества людей. Поля вывода: `professions`, `cnt`.

```sql
SELECT professions,
       count(1) as cnt
FROM   persons
WHERE  professions not like '%,%'
GROUP BY professions having count(1) > 10000
ORDER BY cnt desc;
```

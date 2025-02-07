## 1 задание 

Написать SQL-запрос, который выведет все строки из таблицы `titles`, содержащие жанр `'Sci-Fi'`. Регулярное выражение для поиска такого вхождения: `\mSci-Fi\M`.

Результат отсортируйте по возрастанию `id`.

```sql
SELECT *
FROM   titles
WHERE  genres ~ '\mSci-Fi\M'
ORDER BY id;
```

## 2 задание 

Написать SQL-запрос, который выведет из таблицы `titles` все строки, содержащие жанр `'Sci-Fi'` и заменит его на `'Фантастика'`.

Поля к выводу:

- `id` из таблицы `titles`;
- оригинальное название;
- изначальное значение строки `genres`;
- изменённое значение строки `genres`.

Результат отсортируйте по возрастанию `id`, `original_title`, `genres`, `new_genres`.

```sql
SELECT id,
       original_title,
       genres,
       regexp_replace(genres, 'Sci-Fi', 'Фантастика', 'g') as new_genres
FROM   titles
WHERE  genres ~ '\mSci-Fi\M'
ORDER BY 1, 2, 3, 4;
```

## 3 задание 

Написать SQL-запрос, который с помощью `рекурсии` выведет все буквы от `A до Z`. Для того чтобы получить следующую букву, нужно применить составную функцию. 

```sql
with recursive alphabet as (SELECT 'A' as letter 
                            UNION all
                            SELECT chr(ascii(letter) + 1)
                          FROM   alphabet
                          WHERE  letter < 'Z')
SELECT letter
FROM   alphabet
ORDER BY 1;
```

## 4 задание 

Написать SQL-запрос, который с помощью `рекурсии` выведет все года с `2000 по 2032` и посчитает количество фильмов с `year_of_start`, соответствующих каждому выведенному году.

```sql
with recursive years as (SELECT 2000 as year UNION all
SELECT year + 1
                         FROM   years
                         WHERE  year < 2032)
SELECT year,
       count(t.id)
FROM   titles as t
    RIGHT JOIN years
        ON t.year_of_start = year
GROUP BY year
ORDER BY 1;
```

## 5 задание 

Написать SQL-запрос, который выведет год начала произведений `(year_of_start)` из таблицы `titles` и их количество (алиас `count`), отсортированное в обратном порядке.

```sql
SELECT year_of_start,
       count(1) as count
FROM   titles
GROUP BY year_of_start
ORDER BY count(1) desc;
```

## 6 задание 

Написать SQL-запрос, который выведет год начала произведений из таблицы `titles` и их количество (алиас `count`) в каждом году, отсортированное в порядке возрастания, начиная с `2030 до 2000` года, не пропуская тех лет, в которые не вышло ни одного произведения.

```sql
with recursive date_list as (SELECT 2000 as dt UNION all
SELECT dt + 1
                             FROM   date_list
                             WHERE  dt < 2030)
SELECT dl.dt,
       count(t.id) as count
FROM   date_list as dl
    LEFT JOIN titles as t
        ON dl.dt = t.year_of_start
GROUP BY dl.dt
ORDER BY count(t.id);          
```

## 7 задание 

Написать SQL-запрос, который выведет из таблицы `titles`:

- общее количество произведений в таблице `titles` (алиас `cnt`);
- количество строк с типом `'tvEpisode'` (алиас `episode_cnt`);
- количество строк с типом `'movie'` (алиас `movie_cnt`).

```sql
SELECT count(1) as cnt,
       sum(case when type = 'tvEpisode' then 1
                else 0 end) as episode_cnt,
       sum(case when type = 'movie' then 1
                else 0 end) as movie_cnt
FROM   titles;
```

## 8 задание 

Написать SQL-запрос, который выведет из таблицы `persons`:

- число людей в таблице `persons`;
- количество людей, у которых в списке профессий есть `'actor'`;
- количество людей, у которых в списке профессий есть `'actress'`.

```sql
SELECT count(1) as person_cnt,
       sum(case when professions like '%actor%' then 1
                else 0 end) as actor_cnt,
       sum(case when professions like '%actress%' then 1
                else 0 end) as actress_cnt
FROM   persons;
```

## 9 задание 

Написать SQL-запрос, который найдет дубли в поле `name` таблицы `persons` и выведет для них:

- имя;
- количество совпадений в порядке возрастания.

Результат отсортируйте в порядке возрастания количества.

```sql
SELECT name,
       count(1) as cnt
FROM   persons
GROUP BY name having count(1) > 1
ORDER BY cnt;
```

## 10 задание 

Написать SQL-запрос, который найдет дубли в поле `original_title` таблицы `titles`. 
Выведите для них:

- название;
- количество совпадений в порядке убывания.

```sql
SELECT original_title,
       count(1) as cnt
FROM   titles
GROUP BY original_title having count(1) > 1
ORDER BY count(1) desc;
```

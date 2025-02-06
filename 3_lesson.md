## 1 задание 

Написать SQL-запрос, который выберет все уникальные значения поля `type` из таблицы `titles`. 

Результат отсортируйте по возрастанию `type`.

```sql
SELECT DISTINCT type
FROM   titles
ORDER BY 1;
```

## 2 задание 

Написать SQL-запрос, который выберет все уникальные пары значений полей `type` и `genres` из таблицы `titles`. 

Результат отсортируйте по возрастанию `type` и `genres`.

```sql
SELECT DISTINCT type,
                genres
FROM   titles
ORDER BY 1, 2;
```

## 3 задание 

Написать SQL-запрос, который объединит в себе два запроса в одно поле с именем `role`:

- все уникальные категории из таблицы `principals`;
- все уникальные профессии из таблицы `persons` за исключением тех, в которых есть запятая.
  
Результат отсортируйте в лексикографическом порядке.

```sql
SELECT DISTINCT category as role
FROM   principals
UNION all
SELECT DISTINCT professions
FROM   persons
WHERE  professions not like '%,%'
ORDER BY 1;
```

## 4 задание 

Проведите дедупликацию в запросе из предыдущего задания.

```sql
SELECT DISTINCT category as role
FROM   principals
UNION
SELECT DISTINCT professions
FROM   persons
WHERE  professions not like '%,%'
ORDER BY role;
```

## 5 задание 

Написать SQL-запрос, который объединит таблицы `titles` и `ratings`. Выведите поля:

- `original_title` из таблицы `titles`;
- `type` из таблицы `titles`;
- `year_of_start` из таблицы `titles`;
- `avg_rating` из таблицы `ratings`;
- `vote_cnt` из таблицы `ratings`.

Результат отсортируйте по возрастанию original_title, type, year_of_start, avg_rating.

```sql
SELECT t.original_title,
       t.type,
       t.year_of_start,
       r.avg_rating,
       r.vote_cnt
FROM   titles as t join ratings as r
        ON t.id = r.title_id
ORDER BY 1, 2, 3, 4;
```

## 6 задание 

Написать SQL-запрос, который выведет поле `title_id` тех строк из таблицы `ratings`, для которых нет соответствия в таблице `titles`. 

Результат отсортируйте в порядке убывания.

```sql
SELECT r.title_id
FROM   ratings as r
    LEFT JOIN titles as t
        ON t.id = r.title_id
WHERE  t.original_title is null
ORDER BY title_id desc;
```

## 7 задание 

Написать SQL-запрос, который находит произведения с типом `movie`, у которых есть клоны с таким же `original_title`. Для этого объедините таблицу `titles` с собой по названию произведения, исключив строки, которые поджойнились сами на себя.

Поля к выводу:

- `original_title`;
- `type` оригинала (с алиасом `type_1`);
- `type` клона (с алиасом `type_2`);
- `year_of_start` оригинала (с алиасом `year_of_start_1`);
- `year_of_start` клона (с алиасом `year_of_start_2`).

Результат отсортируйте по возрастанию original_title, type_1, type_2, year_of_start_1, year_of_start_2

```sql
SELECT t1.original_title,
       t1.type as type_1,
       t2.type as type_2,
       t1.year_of_start as year_of_start_1,
       t2.year_of_start as year_of_start_2
FROM   titles as t1 join titles as t2
        ON t1.original_title = t2.original_title and
           t1.id != t2.id and
           t1.type = 'movie'
ORDER BY 1, 2, 3, 4, 5;
```

## 8 задание 

Написать SQL-запрос, который объединяет таблицы `titles` и `ratings` и выводит поля `original_title` и `year_of_start` из таблицы `titles` и `vote_cnt` из таблицы `ratings` в порядке убывания `vote_cnt`. Выберите только те записи, у которых средний рейтинг `(avg_rating)` равен 10.

```sql
SELECT t.original_title,
       t.year_of_start,
       r.vote_cnt
FROM   titles as t
    LEFT JOIN ratings as r
        ON t.id = r.title_id
WHERE  r.avg_rating = 10
ORDER BY r.vote_cnt desc;
```

## 9 задание 

Написать SQL-запрос, который выводит `id` произведений из таблицы `titles`, для которых нет записей в таблице `episodes`. Для этого объедините эти две таблицы по полю `title_id` и выберите те записи, для которых нет соответствий в таблице `episodes`.

Результат отсортируйте по возрастанию `id`.

```sql
SELECT t.id
FROM   titles as t
    LEFT JOIN episodes as e
        ON e.title_id = t.id
WHERE  e.episode is null
ORDER BY 1;
```

## 1 задание 

Вывести из таблицы `ratings`:

- значение поля `avg_rating`, умноженное на 100;
- ближайшее большее целое поля `avg_rating`;
- ближайшее меньшее целое поля `avg_rating`;
- округлённое значение поля `vote_cnt`, делённого на 1000.

```sql
SELECT avg_rating * 100 AS column_1,
    ceil(avg_rating) AS column_2,
    floor(avg_rating) AS column_3,
    round(vote_cnt / 1000) AS column_4
FROM ratings;
```
## 2 задание 

Вывести:

- поле `run_time` из таблицы `titles`;
- количество полных часов без минут, указанных в поле `run_time` (назвать это поле `hours`).

```sql
SELECT run_time,
       run_time/60 as hours
FROM   titles;
```

## 3 задание 

Вывести:

- поле `name` из таблицы `persons`;
- количество символов в поле `name` (назвать это поле `len`);
- поле `name`, переведённое в верхний регистр (назвать это поле `up`);
- номер символа с первым вхождением буквы `a` (назвать это поле `a_pos`).

```sql
SELECT name,
       length(name) as len,
       upper(name) as up,
       position('a' in name) as a_pos
FROM   persons;
```

## 4 задание 

Написать SQL-запрос, который составит поле вида `name` — `professions` из таблицы `persons`. 
Назвать результирующее поле `about`.

```sql
SELECT concat(name, ' - ', professions) as about
FROM   persons;
```

## 5 задание 

Вывести из таблицы `persons`:

- имя;
- год текущей даты (назовите его `year`);
- год рождения (полe `year_of_birth`).

```sql
SELECT 
    name, 
    extract(year from now()) as year,
    year_of_birth
FROM persons;
```

## 6 задание 

Написать SQL-запрос, который выводит:

- количество лет, прошедших с выхода произведения (поле `year_of_start` таблицы `titles`) до текущего года (назвать поле `years`).

```sql
SELECT 
    EXTRACT(YEAR FROM NOW()) - year_of_start AS years
FROM titles;
```

## 7 задание 

Написать SQL-запрос, который выводит:

- поле `popular_title` из таблицы `titles`;
- поле `original_title` из таблицы `titles`;
- флаг, одинаковы ли эти значения (назовите его `title_equality`);
- флаг, принимающий значение True, если длина первого поля больше длины второго поля (назовите его `title_over`).

```sql
SELECT 
    popular_title,
    original_title,
    popular_title = original_title AS title_equality,
    length(popular_title) > length(original_title) AS title_over
FROM titles;
```

## 8 задание 

Вывести:

- имя из таблицы `persons`;
- если год рождения больше или равен 2000, то значение 'young', иначе значение 'old'. Присвоить расчетному полю имя `age_group`

```sql
SELECT 
    name,
    CASE WHEN year_of_birth >= 2000 THEN 'young'
            ELSE 'old' END AS age_group
FROM persons;
```

## 9 задание 

Вывести:

- поле `id` из таблицы `titles`;
- номер символа с первым вхождением цифры 6 в поле `id` из таблицы `titles` (назовите поле `pos_numb`).

```sql
SELECT 
    id,
    position('6' in id::text) as pos_numb
FROM titles;
```

## 10 задание 

Вывести:

- поле `original_title`;
- поле `is_adult`;
- поле `is_adult`, приведённое к целочисленному типу (назовите его `is_adult_int`).

```sql
SELECT
    original_title,
    is_adult,
    is_adult::int as is_adult_int
FROM titles;
```

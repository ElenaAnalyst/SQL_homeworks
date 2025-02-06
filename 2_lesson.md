## 1 задание 

Написать SQL-запрос, который находит строку в таблице `persons` с именем Дженна Ортега (Jenna Ortega).
Поля в запросе: `name`, `year_of_birth`, `year_of_death`, `professions`.

```sql
SELECT
  name,
  year_of_birth,
  year_of_death,
  professions
FROM
  persons
WHERE
  name = 'Jenna Ortega'; 
```

## 2 задание

Написать SQL-запрос, который найдет в таблице `titles` строки, в которых в названии `(original_title)` указано `Wednesday`.

```sql
SELECT *
FROM
  titles
WHERE 
  original_title = 'Wednesday';
```

## 3 задание 

Написать SQL-запрос, который вернёт имя `(name)`, год рождения `(year_of_birth)`, год смерти `(year_of_death)` и профессию `(professions)` из таблицы `persons`, умерших в 2022г с профессией `actor` или `actress`.
Pезультат отсортируйте по возрастанию name, year_of_birth, year_of_death, professions.

```sql
SELECT name,
       year_of_birth,
       year_of_death,
       professions
FROM   persons
WHERE  year_of_death = 2022
   and professions in ('actor', 'actress')
ORDER BY 1, 2, 3, 4;
```

## 4 задание 

Написать SQL-запрос, который вернёт имя `(name)`, год рождения `(year_of_birth)`, год смерти `(year_of_death)` и профессию `(professions)` из таблицы `persons`,  родившихся в 2000 г., с именем, начинающимся с `Y`.
Результат отсортируйте по возрастанию name, year_of_birth, year_of_death, professions

```sql
SELECT name, year_of_birth, year_of_death, professions
FROM persons
WHERE year_of_birth = 2000
AND name LIKE 'Y%'
ORDER BY 1,2,3,4;
```

## 5 задание 

Написать SQL-запрос, который вернёт топ-10 строк с максимальным количеством голосов `(vote_cnt)` из таблицы рейтингов `ratings`. 

```sql
SELECT *
FROM ratings
ORDER BY vote_cnt DESC 
LIMIT 10;
```

## 6 задание 

Написать SQL-запрос, который вернёт сотую строку в топе с максимальным количеством голосом `(vote_cnt)` из таблицы рейтингов `ratings`. 

```sql
SELECT *
FROM ratings
ORDER BY vote_cnt DESC 
OFFSET 99 LIMIT 1;
```

## 7 задание 

Написать SQL-запрос, который вернёт имя `(name)`, год рождения `(year_of_birth)`, год смерти `(year_of_death)` и профессию `(professions)` из таблицы `persons`, всех родившихся в 1990г и уже умерших.
Результат отсортируйте по возрастанию name, year_of_birth, year_of_death, professions.

```sql
SELECT name,
       year_of_birth,
       year_of_death,
       professions
FROM   persons
WHERE  year_of_birth = 1990
   and year_of_death is not null
ORDER BY 1, 2, 3, 4;
```

## 8 задание 

Написать SQL-запрос, который вернёт имя `(name)`, год рождения `(year_of_birth)`, год смерти `(year_of_death)` и профессию `(professions)`  из таблицы `persons` всех у кого не указан год рождения и год смерти.
Результат отсортируйте по возрастанию name, year_of_birth, year_of_death, professions.

```sql
SELECT name,
       year_of_birth,
       year_of_death,
       professions
FROM   persons
WHERE  year_of_birth is null
   and year_of_death is null
ORDER BY 1, 2, 3, 4;
```

## 9 задание 

Написать SQL-запрос, который вернёт имя `(name)`, год рождения `(year_of_birth)`, год смерти `(year_of_death)` и профессию `(professions)`  из таблицы `persons` всех родившихся в 2000г. При этом если человек не умер — замените NULL в поле `year_of_death` строкой `alive` (назовите поле также, как исходное — `year_of_death`).
Результат отсортируйте по возрастанию name, year_of_birth, year_of_death, professions.

```sql
SELECT name,
       year_of_birth,
       professions,
       COALESCE(year_of_death::TEXT, 'alive') AS year_of_death
FROM   persons
WHERE year_of_birth = 2000 
ORDER BY 1, 2, 3, 4;
```

## 10 задание 

Написать запрос, который вернёт имя `(name)`, возраст `(age)`, приведенный к целочисленному типу, и профессию `(professions)`  из таблицы `persons`, с профессией `(profession)` `actor` или `actress`, у которых указан год рождения `(year_of_birth)`. При этом если год смерти `(year_of_death)` не указан — необходимо заменить его на текущий.
Результат отсортируйте по возрастанию name, age, professions

```sql
SELECT name,
       coalesce(year_of_death, date_part('year', now())::int) - year_of_birth as age,
       professions
FROM   persons
WHERE  professions in ('actor', 'actress')
   and year_of_birth is not null
ORDER BY 1, 2, 3;
```

## В этом блоке представлены мои запросы к базе данных, которые разделены на следующие блоки:

* [Типы данных и функции преобразований](https://github.com/ElenaAnalyst/SQL_homeworks/blob/main/1_lesson.md)
* [Фильтрация и сортировка данных](https://github.com/ElenaAnalyst/SQL_homeworks/blob/main/2_lesson.md)
* [Сложные запросы. UNION, JOIN и дедупликация](https://github.com/ElenaAnalyst/SQL_homeworks/blob/main/3_lesson.md)
* [Агрегация, группировка данных](https://github.com/ElenaAnalyst/SQL_homeworks/blob/main/4_lesson.md)
* [Подзапросы, CTE](https://github.com/ElenaAnalyst/SQL_homeworks/blob/main/5_lesson.md)
* [Оконные функции](https://github.com/ElenaAnalyst/SQL_homeworks/blob/main/6_lesson.md)
* [Регулярные выражения, рекурсивный запрос](https://github.com/ElenaAnalyst/SQL_homeworks/blob/main/7_lesson.md)


Запросы выполнялись в Redash(PostgreSQL).

Далее рассмотрим данные, которые необходимо анализировать в процессе решения задач.

### 1. База данных о персонажах и эпизодах из мультсериала "Рик и Морти" 👩‍🎤

![Схема базы данных](https://storage.yandexcloud.net/klms-public/production/learning-content/434/4009/36450/101099/521981/photo_2024-07-29_23-09-13.jpg)

Имеются следующие таблицы:

**`Titles`** — информация о произведениях (фильмах, сериалах, шоу):

* **id** — уникальный идентификатор;
* **type** — тип произведения: фильм, сериал, короткометражка;
* **popular_title** — популярное название;
* **original_title** — оригинальное название;
* **year_of_start** — год выпуска/старта сериала;
* **year_of_end** — год окончания сериала;
* **is_adult** — есть ли у фильма ограничение 18+;
* **run_time** — длительность в минутах;
* **genres** — жанры, к которым относится произведение.

**`Episodes`** — содержит данные с эпизодами сериалов:

* **title_id** — ссылка на **title_id** этого эпизода;
* **parent_id** — ссылка на **title_id** всего сериала;
* **season** — номер сезона;
* **episode** — номер эпизода.

**`Ratings`** — содержит данные с рейтингами от зрителей:

* **title_id** — оцениваемое произведение;
* **avg_rating** — средний рейтинг;
* **vote_cnt** — количество голосовавших.

**`Principals`** — данные с людьми, участвовавшими в создании произведений:

* **title_id** — id произведения;
* **ordering** — уникальный номер в рамках одного **title_id**;
* **person_id** — id из таблицы persons;
* **category** — категория работы в рамках произведения;
* **job** — конкретная работа. Человек может быть автором произведения и в рамках фильма работать над адаптацией текста;
* **characters** — персонажи, которых играл человек.

**`Persons`** — таблица с людьми, относящимися к миру кино:

* **id** — уникальный идентификатор человека;
* **name** — имя;
* **year_of_birth** — год рождения;
* **year_of_death** — год смерти;
* **professions** — список профессий.

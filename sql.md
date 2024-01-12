# Структура sql-запросов

SELECT ('столбцы или * для выбора всех столбцов; обязательно')
FROM ('таблица; обязательно')
WHERE ('условие/фильтрация'; необязательно')
GROUP BY ('столбец, по которому хотим сгруппировать данные; необязательно')
HAVING ('условие/фильтрация на уровне сгруппированных данных; необязательно')
ORDER BY ('столбец, по которому хотим отсортировать вывод; необязательно')

# Создание

```mysql
CREATE TABLE books
(
    id     INT       NOT NULL AUTO_INCREMENT PRIMARY KEY,
    title  CHAR(100) NOT NULL,
    author CHAR(40)  NOT NULL,
    price  INT       NOT NULL
);

CREATE UNIQUE INDEX UNIQ_B6A2DD685F37A13B ON access_token (token);
CREATE INDEX IDX_B6A2DD68A76ED395 ON access_token (user_id);
ALTER TABLE access_token
    ADD CONSTRAINT FK_B6A2DD68A76ED395 FOREIGN KEY (user_id) REFERENCES "user" (id) NOT DEFERRABLE INITIALLY IMMEDIATE;
```

### DEFERRABLE

Однако если мы объявим ограничение как ОТМЕНЯЕМОЕ, то у нас будет возможность дождаться завершения транзакции, прежде чем проверять ограничение.

После ключевого слова DEFERRABLE следует либо INITIALLY DEFERRED, либо INITIALLY IMMEDIATE. В первом случае проверка будет отложена до момента фиксации каждой транзакции. Во втором случае проверка будет выполняться сразу после каждого
оператора.

# Приверы

Вывести список сотрудников, получающих заработную плату большую чем у непосредственного руководителя

```mysql
select a.*
from employee a,
     employee b
where b.id = a.chief_id
  and a.salary > b.salary
```

Вывести список сотрудников, получающих максимальную заработную плату в своем отделе

```mysql
select a.*
from employee a
where a.salary = (select max(salary)
                  from employee b
                  where b.department_id = a.department_id)
```

Вывести список ID отделов, количество сотрудников в которых не превышает 3 человек

```mysql
select department_id
from employee
group by department_id
having count(*) <= 3
```

Вывести список сотрудников, не имеющих назначенного руководителя, работающего в том-же отделе

```mysql
select a.*
from employee a
         left join employee b on (b.id = a.chief_id and b.department_id = a.department_id)
where b.id is null
```

Найти список ID отделов с максимальной суммарной зарплатой сотрудников

```mysql
with sum_salary as
         (select department_id, sum(salary) salary
          from employee
          group by department_id)
select department_id
from sum_salary a
where a.salary = (select max(salary) from sum_salary) 
```

Составить SQL-запрос, вычисляющий произведение вещественных значений, содержащихся в некотором столбце таблицы

```mysql
select exp(sum(ln(decode(sign(salary), 0, 1, -1, -salary, salary))))
           * decode(mod(sum(decode(sign(salary), -1, 1, 0)), 2), 1, -1, 1)
           * sign(min(abs(salary)))
from employee
```

Как выбрать записи с нечётными Id?

```mysql
SELECT *
FROM sample
WHERE id % 2 != 0;
```

Как найти дубли в поле email?

```mysql
SELECT email, COUNT(email)
FROM customers
GROUP BY email
HAVING COUNT(email) > 1;
```

При выборке из таблицы прибавьте к дате 1 день

```mysql
SELECT DATE_ADD(date, 1 DAY) as new_date
FROM table;
```

Выберите только уникальные имена

```mysql
SELECT DISTINCT name
FROM users;
```

Найдите в таблице среднюю зарплату работников

```mysql
SELECT AVG(salary)
FROM workers;
```

А теперь получите список сотрудников с зарплатой выше средней

```mysql
SELECT *
FROM workers
WHERE salary > (SELECT AVG(salary) FROM workers);
```

Даны таблицы workers и departments. Найдите все департаменты без единого сотрудника

```mysql
SELECT department_name
FROM workers w
         RIGHT JOIN departments d
                    ON (w.department_id = d.department_id)
WHERE first_name IS NULL;
```

Замените в таблице зарплату работника на 1000, если она равна 900, и на 1500 в остальных случаях

```mysql
UPDATE table
SET salary = CASE WHEN salary = 900 THEN 1000 ELSE 1500 END;
```

При выборке из таблицы пользователей создайте поле, которое будет включать в себя и имена, и зарплату

```mysql
SELECT CONCAT(name, salary) AS new_field
FROM users;
```

Переименуйте таблицу

```mysql
ALTER TABLE first_table RENAME second_table;
```

# Домашнее задание к занятию "`Базы данных`" - `Евгений Головенко`

---

### Легенда

Заказчик передал вам [файл в формате Excel](https://github.com/netology-code/sdb-homeworks/blob/main/resources/hw-12-1.xlsx), в котором сформирован отчёт. 

На основе этого отчёта нужно выполнить следующие задания.

### Задание 1

Опишите не менее семи таблиц, из которых состоит база данных. Определите:

- какие данные хранятся в этих таблицах,
- какой тип данных у столбцов в этих таблицах, если данные хранятся в PostgreSQL.

Начертите схему полученной модели данных. Можете использовать онлайн-редактор: https://app.diagrams.net/

Этапы реализации:
1.	Внимательно изучите предоставленный вам файл с данными и подумайте, как можно сгруппировать данные по смыслу.
2.	Разбейте исходный файл на несколько таблиц и определите список столбцов в каждой из них. 
3.	Для каждого столбца подберите подходящий тип данных из PostgreSQL. 
4.	Для каждой таблицы определите первичный ключ (PRIMARY KEY).
5.	Определите типы связей между таблицами. 
6.	Начертите схему модели данных.
На схеме должны быть чётко отображены:
   - все таблицы с их названиями,
   - все столбцы  с указанием типов данных,
   - первичные ключи (они должны быть явно выделены),
   - линии, показывающие связи между таблицами.

**Результатом выполнения задания** должен стать скриншот получившейся схемы базы данных.

### Решение 1

Скриншот получившейся схемы базы данных:

<img width="820" height="454" alt="Postgre_1" src="https://github.com/user-attachments/assets/8f145f37-cd71-424e-b0e4-9f753c195f93" />

Связи между таблицами:

employees -> positions - многие к одному

employees -> departments - многие к одному

departments -> department_types - многие к одному

employees -> branches - многие к одному

employees <-> projects - многие ко многим через employee_projects

employees -> salaries - один ко многим

---

### Задание 2*

1. Разверните СУБД Postgres на своей хостовой машине, на виртуальной машине или в контейнере docker.
2. Опишите схему, полученную в предыдущем задании, с помощью скрипта SQL.
3. Создайте в вашей полученной СУБД новую базу данных и выполните полученный ранее скрипт для создания вашей модели данных.

В качестве решения приложите SQL скрипт и скриншот диаграммы.

### Решение 2*

1. Развернут PostgreSQL 18, установлен DBeaver.

2. SQL - скрипт:

```
CREATE DATABASE hw_12_1_db
    WITH
    OWNER = postgres
    ENCODING = 'UTF8';

CREATE SCHEMA hr;
SET search_path TO hr;

-- Типы подразделений
CREATE TABLE hr.department_types (
    department_type_id BIGSERIAL PRIMARY KEY,
    type_name VARCHAR(50) NOT NULL
);

-- Подразделения
CREATE TABLE hr.departments (
    department_id BIGSERIAL PRIMARY KEY,
    department_name VARCHAR(200) NOT NULL,
    department_type_id BIGINT NOT NULL
        REFERENCES hr.department_types(department_type_id)
);

-- Должности
CREATE TABLE hr.positions (
    position_id BIGSERIAL PRIMARY KEY,
    position_name VARCHAR(200) NOT NULL
);

-- Филиалы
CREATE TABLE hr.branches (
    branch_id BIGSERIAL PRIMARY KEY,
    address TEXT NOT NULL
);

-- Сотрудники
CREATE TABLE hr.employees (
    employee_id BIGSERIAL PRIMARY KEY,
    last_name VARCHAR(100) NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    middle_name VARCHAR(100),
    hire_date DATE NOT NULL,
    position_id BIGINT NOT NULL
        REFERENCES hr.positions(position_id),
    department_id BIGINT NOT NULL
        REFERENCES hr.departments(department_id),
    branch_id BIGINT NOT NULL
        REFERENCES hr.branches(branch_id)
);

-- Оклады
CREATE TABLE hr.salaries (
    salary_id BIGSERIAL PRIMARY KEY,
    employee_id BIGINT NOT NULL
        REFERENCES hr.employees(employee_id),
    salary_amount NUMERIC(12,2) NOT NULL,
    valid_from DATE NOT NULL
);

-- Проекты
CREATE TABLE hr.projects (
    project_id BIGSERIAL PRIMARY KEY,
    project_name VARCHAR(300) NOT NULL
);

-- Связь сотрудник–проект
CREATE TABLE hr.employee_projects (
    employee_id BIGINT NOT NULL
        REFERENCES hr.employees(employee_id),
    project_id BIGINT NOT NULL
        REFERENCES hr.projects(project_id),
    PRIMARY KEY (employee_id, project_id)
);
```

Скриншот представлен выше, в Задании 1.

# Лабораторная работа №2 #
## Автор: Окуньков Николай Степанович ##

*db<>fiddle [here](https://dbfiddle.uk/?rdbms=postgres_12&fiddle=2c865126dfa66ea65a461ed75882cb94)*

### 1.	Дана схема базы данных в виде следующих отношений.  С помощью операторов SQL создать логическую структуру соответствующих таблиц для хранения в СУБД, используя известные средства поддержания целостности (NOT NULL, UNIQUE, и т.д.). Обосновать выбор типов данных и используемые средства поддержания целостности. При выборе подходящих типов данных использовать информацию о конкретных значениях полей БД (см. прил.1)
```SQL

CREATE TABLE "ВОДИТЕЛЬ"
(
    "ИДЕНТИФИКАТОР" integer NOT NULL GENERATED BY DEFAULT AS IDENTITY,
    -- Идентификатор integer, не должен быть NULL. Если не задан,
    -- задается автоматоматически
    "ФАМИЛИЯ" character varying (50),
    -- character varying на 50 символов вполне хватит
    "АВТОПРЕДПРИЯТИЕ" character varying (50),
    -- character varying на 50 символов вполне хватит
    "ЛЬГОТА" smallint CHECK ("ЛЬГОТА">=0 AND "ЛЬГОТА"<=100),
    -- smallint занимает минимум памяти. CHECK позволяет избежать
    -- ошибок и хранить в данном поле только значения от 0% до 100%
    PRIMARY KEY ("ИДЕНТИФИКАТОР")
);

CREATE TABLE "ЦЕНТР ОБСЛУЖИВАНИЯ"
(
    "ИДЕНТИФИКАТОР" integer NOT NULL GENERATED BY DEFAULT AS IDENTITY,
    -- Идентификатор integer, не должен быть NULL. Если не задан,
    -- задается автоматоматически
    "НАЗВАНИЕ" character varying (50),
    -- character varying на 50 символов вполне хватит
    "ВЛАДЕЛЕЦ" character varying (50),
    -- character varying на 50 символов вполне хватит
    "КОМИССИОННЫЕ" smallint CHECK ("КОМИССИОННЫЕ">=0 AND "КОМИССИОННЫЕ"<=100),
    -- smallint занимает минимум памяти. CHECK позволяет избежать
    -- ошибок и хранить в данном поле только значения от 0% до 100%
    PRIMARY KEY ("ИДЕНТИФИКАТОР")
);

CREATE TABLE "ПРЕЙСКУРАНТ"
(
    "ИДЕНТИФИКАТОР" integer NOT NULL GENERATED BY DEFAULT AS IDENTITY,
    -- Идентификатор integer, не должен быть NULL. Если не задан,
    -- задается автоматоматически
    "ТОВАР" text,
    -- возможно, существуют какие-то длинные названия товаров. На всякий
    -- случай сделаем это поле text. В предоставленных данных таких заданий нет,
    -- поэтому, в принципе, можно заменить текст на varchar (character varying)
    "ЦЕНА" int,
    -- т.к. она в рублях, а рубль из-за дяди Вовы обесценивается с огромной
    -- скоростью, smallint здесь может не хватить
    "У КОГО ЗАКУПАЕТСЯ" character varying (50),
    -- character varying на 50 символов вполне хватит
    "МАКС. КОЛ-ВО" smallint,
    -- вряд-ли будет более 32767, однако если в базе будут крупные закупки,
    -- лучше использовать integer
    PRIMARY KEY ("ИДЕНТИФИКАТОР")
);

CREATE TABLE "ЗАКАЗ"
(
    "НОМЕР ВЕДОМОСТИ" integer NOT NULL,
    -- не должен быть NULL
    "ДАТА" varchar (10),
    -- т.к. тут хранятся названия месяцев, не вижу смысла делать больше 10
    -- можно добавить огромный чек на кейсах, чтобы сюда нельзя было записать
    -- ничего кроме названия месяцев. Но не хочется вставлять кучу строчек
    -- с условиями. Можно было также преобразовывать эти значения и хранить их 
    -- в date, но я подумал, что раз они даны в таком виде, лучше в нем их и
    -- оставить.
    "ВОДИТЕЛЬ" integer,
    "ЦЕНТР ОБСЛ." integer,
    "ТОВАР ПО ПРЕЙСКУРАНТУ" integer,
    -- Эти три поля выше, по сути, связаны с теми, что были в предыдущих таблицах,
    -- поэтому и типы мы им оставим такие же
    "КОЛ-ВО" smallint,
    -- вряд-ли будет более 32767, однако если в базе будут крупные закупки,
    -- лучше использовать integer
    "ИТОГО" int,
    -- можно было использовать money, но конкретно для заданий из методички
    -- и для предоставленных данных это оказалось не слишком удобным.
    PRIMARY KEY ("НОМЕР ВЕДОМОСТИ")
);
```
<pre>

 
 
 ✓
 
 ✓
 
 ✓
 
 ✓
 </pre>
 
### 2.	Ввести в ранее созданные таблицы конкретные данные (см. прил. 1). Использовать скрипт-файл из операторов INSERT или вспомогательную утилиту .
```SQL

INSERT INTO "ВОДИТЕЛЬ" VALUES
(001, 'Горбунов', 'АТП1', 5),
(002,'Попов','МП "ФорТУНА"',0),
(003,'Денисов','АО "Автотранс"',10),
(004,'Сергеев','АО "Автотранс"',10),
(005,'Левкин','АТП1',5);

INSERT INTO "ЦЕНТР ОБСЛУЖИВАНИЯ" VALUES
('001', 'Окружная дорога1', 'АТП1', '3'),
('002', 'Окружная дорога2', 'АТП1', '3'),
('003', '123КМ', 'АО "Автотранс"', '2'),
('004', 'АЗС12', 'АО "ФорТУНА"', '4'),
('005', 'АЗС7', 'АТП1', '3'),
('006', 'У поворота', 'АО "ФорТУНА"', '4');

INSERT INTO "ПРЕЙСКУРАНТ" VALUES
('001', 'Бензин АИ-72', '9000', 'АТП1', '10000'),
('002', 'Бензин АИ-96', '10000', 'АТП1', '12000'),
('003', 'Масло моторное МТ23-12', '7000', 'АО "ФорТУНА"', '7000'),
('004', 'Масло моторное УММ-23Т', '18500', 'АО "Автотранс"', '5300'),
('005', 'Свеча зажигания', '22000', 'АО "Автотранс"', '200'),
('006', 'Прокладка', '6000', 'АТП1', '500'),
('007', 'Жидкость смывная', '12000', 'АО "ФорТУНА"', '100');

INSERT INTO "ЗАКАЗ" VALUES
('12201', 'Январь', '002', '004', '007', '4', '48000'),
('12202', 'Январь', '003', '005', '007', '4', '48000'),
('12203', 'Январь', '003', '005', '007', '6', '72000'),
('12204', 'Февраль', '003', '003', '004', '2', '37000'),
('12205', 'Февраль', '004', '002', '001', '40', '360000'),
('12206', 'Февраль', '004', '001', '001', '40', '360000'),
('12207', 'Март', '003', '001', '001', '20', '180000'),
('12208', 'Апрель', '002', '004', '003', '10', '70000'),
('12209', 'Апрель', '003', '003', '006', '4', '24000'),
('12210', 'Май', '001', '002', '006', '2', '12000'),
('12211', 'Май', '003', '006', '003', '2', '14000'),
('12212', 'Июнь', '005', '004', '007', '10', '120000'),
('12213', 'Июль', '003', '004', '004', '10', '185000'),
('12214', 'Июль', '005', '004', '002', '6', '60000'),
('12215', 'Август', '002', '006', '005', '4', '88000'),
('12216', 'Сентябрь', '001', '004', '005', '6', '132000'),
('12217', 'Сентябрь', '003', '001', '007', '40', '360000');
```
<pre>
 
5 rows affected
 
6 rows affected
 
7 rows affected
 
17 rows affected
</pre>
### 3.	Используя оператор SELECT создать запрос для вывода всех строк каждой таблицы. Проверить правильность ввода. При необходимости произвести коррекцию значений операторами INSERT, UPDATE, DELETE. 
```SQL

SELECT * FROM "ВОДИТЕЛЬ";
SELECT * FROM "ЦЕНТР ОБСЛУЖИВАНИЯ";
SELECT * FROM "ПРЕЙСКУРАНТ";
SELECT * FROM "ЗАКАЗ";
``` 
 
 ИДЕНТИФИКАТОР | ФАМИЛИЯ   | АВТОПРЕДПРИЯТИЕ | ЛЬГОТА
 ------------: | :-------- | :-------------- | -----------:
 1 | Горбунов | АТП1                        |            5
2 | Попов       | МП &quot;ФорТУНА&quot;          |            0
3 | Денисов   | АО &quot;Автотранс&quot;      |           10
4 | Сергеев   | АО &quot;Автотранс&quot;      |           10
5 | Левкин     | АТП1                        |            5
 
 ИДЕНТИФИКАТОР | НАЗВАНИЕ               | ВЛАДЕЛЕЦ          | КОМИССИОННЫЕ
 -------------------------: | :----------------------------- | :------------------------ | -----------------------:
1 | Окружная дорога1 | АТП1                   |                        3
2 | Окружная дорога2 | АТП1                   |                        3
3 | 123КМ                        | АО &quot;Автотранс&quot; |                        2
4 | АЗС12                       | АО &quot;ФорТУНА&quot;     |                        4
5 | АЗС7                        | АТП1                   |                        3
6 | У поворота            | АО &quot;ФорТУНА&quot;     |                        4
 
 ИДЕНТИФИКАТОР | ТОВАР                              | ЦЕНА | У КОГО ЗАКУПАЕТСЯ | МАКС. КОЛ-ВО
 -------------------------: | :-------------------------------------- | -------: | :------------------------------- | --------------------:
1 | Бензин АИ-72                    |     9000 | АТП1                          |                 10000
2 | Бензин АИ-96                    |    10000 | АТП1                          |                 12000
3 | Масло моторное МТ23-12   |     7000 | АО &quot;ФорТУНА&quot;            |                  7000
4 | Масло моторное УММ-23Т |    18500 | АО &quot;Автотранс&quot;        |                  5300
5 | Свеча зажигания           |    22000 | АО &quot;Автотранс&quot;        |                   200
6 | Прокладка                      |     6000 | АТП1                          |                   500
7 | Жидкость смывная         |    12000 | АО &quot;ФорТУНА&quot;            |                   100
 
 НОМЕР ВЕДОМОСТИ | ДАТА         | ВОДИТЕЛЬ | ЦЕНТР ОБСЛ. | ТОВАР ПО ПРЕЙСКУРАНТУ | КОЛ-ВО | ИТОГО
 ----------------------------: | :--------------- | ---------------: | -------------------: | ---------------------------------------: | ----------: | ---------:
12201 | Январь     |                2 |                    4 |                                        7 |           4 |      48000
12202 | Январь     |                3 |                    5 |                                        7 |           4 |      48000
12203 | Январь     |                3 |                    5 |                                        7 |           6 |      72000
12204 | Февраль   |                3 |                    3 |                                        4 |           2 |      37000
12205 | Февраль   |                4 |                    2 |                                        1 |          40 |     360000
12206 | Февраль   |                4 |                    1 |                                        1 |          40 |     360000
12207 | Март         |                3 |                    1 |                                        1 |          20 |     180000
12208 | Апрель     |                2 |                    4 |                                        3 |          10 |      70000
12209 | Апрель     |                3 |                    3 |                                        6 |           4 |      24000
12210 | Май           |                1 |                    2 |                                        6 |           2 |      12000
12211 | Май           |                3 |                    6 |                                        3 |           2 |      14000
12212 | Июнь         |                5 |                    4 |                                        7 |          10 |     120000
12213 | Июль         |                3 |                    4 |                                        4 |          10 |     185000
12214 | Июль         |                5 |                    4 |                                        2 |           6 |      60000
12215 | Август     |                2 |                    6 |                                        5 |           4 |      88000
12216 | Сентябрь |                1 |                    4 |                                        5 |           6 |     132000
12217 | Сентябрь |                3 |                    1 |                                        7 |          40 |     360000
 


### 4.	Создать запросы для вывода:
```SQL
-- a) всех различных идентификаторов и фамилий водителей;
SELECT DISTINCT ИДЕНТИФИКАТОР, ФАМИЛИЯ FROM ВОДИТЕЛЬ;
-- b) всех различных мест работы водителей;
SELECT DISTINCT АВТОПРЕДПРИЯТИЕ FROM ВОДИТЕЛЬ;
-- c) всех различных владельцев центров обслуживания
SELECT DISTINCT ВЛАДЕЛЕЦ FROM "ЦЕНТР ОБСЛУЖИВАНИЯ";
```
 
 ИДЕНТИФИКАТОР | ФАМИЛИЯ  
 -------------------------: | :---------------
 5 | Левкин    
 2 | Попов      
 1 | Горбунов
 3 | Денисов  
 4 | Сергеев  
 
 | АВТОПРЕДПРИЯТИЕ |
 | :----------------------------- |
 | АО &quot;Автотранс&quot;      |
 | МП &quot;ФорТУНА&quot;          |
 | АТП1                        |
 
 | ВЛАДЕЛЕЦ          |
 | :------------------------ |
 | АО &quot;Автотранс&quot; |
 | АТП1                   |
 | АО &quot;ФорТУНА&quot;     |
 

### 5.	Создав запрос получить следующую информацию:
```SQL
-- a)	названия и владельцы центров обслуживания с размером комиссионных менее 4%;
SELECT НАЗВАНИЕ, ВЛАДЕЛЕЦ FROM "ЦЕНТР ОБСЛУЖИВАНИЯ" WHERE КОМИССИОННЫЕ < 4;
-- b)	полное название, владельцы и размер комиссионных автозаправочных станций (т.е. таких центров обслуживания, где в названии есть “АЗС”);
SELECT НАЗВАНИЕ, ВЛАДЕЛЕЦ, КОМИССИОННЫЕ FROM "ЦЕНТР ОБСЛУЖИВАНИЯ" WHERE НАЗВАНИЕ LIKE '%АЗС%';
-- c)	фамлии и места работы водителей у которых нет льгот;
SELECT ФАМИЛИЯ, АВТОПРЕДПРИЯТИЕ FROM ВОДИТЕЛЬ WHERE ЛЬГОТА = 0;
```
 
 НАЗВАНИЕ               | ВЛАДЕЛЕЦ         
 :----------------------------- | :------------------------
 Окружная дорога1 | АТП1                  
 Окружная дорога2 | АТП1                  
 123КМ                        | АО &quot;Автотранс&quot;
 АЗС7                        | АТП1                  
 
 НАЗВАНИЕ | ВЛАДЕЛЕЦ      | КОМИССИОННЫЕ
 :--------------- | :-------------------- | -----------------------:
 АЗС12         | АО &quot;ФорТУНА&quot; |                        4
 АЗС7          | АТП1               |                        3
 
 ФАМИЛИЯ | АВТОПРЕДПРИЯТИЕ
 :------------- | :-----------------------------
 Попов     | МП &quot;ФорТУНА&quot;         
 


### 6.	На основании данных о заказах вывести все данные в таком формате:
a)	номер, ведомости, дата, фамилия водителя, итоговая сумма заказа (итого). Отсортировать по фамилиям и сумме заказа;
```SQL
SELECT ЗАКАЗ."НОМЕР ВЕДОМОСТИ", ЗАКАЗ.ДАТА, ВОДИТЕЛЬ.ФАМИЛИЯ, ИТОГО
FROM ЗАКАЗ, ВОДИТЕЛЬ WHERE ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ
ORDER BY ВОДИТЕЛЬ.ФАМИЛИЯ, ИТОГО;
```
 
 НОМЕР ВЕДОМОСТИ | ДАТА         | ФАМИЛИЯ   | ИТОГО
 ----------------------------: | :--------------- | :--------------- | ---------:
12210 | Май           | Горбунов |      12000
12216 | Сентябрь | Горбунов |     132000
12211 | Май           | Денисов   |      14000
12209 | Апрель     | Денисов   |      24000
12204 | Февраль   | Денисов   |      37000
12202 | Январь     | Денисов   |      48000
12203 | Январь     | Денисов   |      72000
12207 | Март         | Денисов   |     180000
12213 | Июль         | Денисов   |     185000
12217 | Сентябрь | Денисов   |     360000
12214 | Июль         | Левкин     |      60000
12212 | Июнь         | Левкин     |     120000
12201 | Январь     | Попов       |      48000
12208 | Апрель     | Попов       |      70000
12215 | Август     | Попов       |      88000
12206 | Февраль   | Сергеев   |     360000
12205 | Февраль   | Сергеев   |     360000
 
b)	фамилия водителя, название центра обслуживания, товар ( по прейскуранту), количество.
```SQL
SELECT ВОДИТЕЛЬ.ФАМИЛИЯ, "ЦЕНТР ОБСЛУЖИВАНИЯ".НАЗВАНИЕ,
ПРЕЙСКУРАНТ.ТОВАР, ЗАКАЗ."КОЛ-ВО"
FROM ЗАКАЗ, ВОДИТЕЛЬ, "ЦЕНТР ОБСЛУЖИВАНИЯ", ПРЕЙСКУРАНТ
WHERE ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ AND 
"ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР = ЗАКАЗ."ЦЕНТР ОБСЛ." AND
ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР = ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ";
 ```
 
 ФАМИЛИЯ   | НАЗВАНИЕ               | ТОВАР                              | КОЛ-ВО
 :--------------- | :----------------------------- | :-------------------------------------- | ----------:
 Попов       | АЗС12                       | Жидкость смывная         |           4
 Денисов   | АЗС7                        | Жидкость смывная         |           4
 Денисов   | АЗС7                        | Жидкость смывная         |           6
 Денисов   | 123КМ                        | Масло моторное УММ-23Т |           2
 Сергеев   | Окружная дорога2 | Бензин АИ-72                    |          40
 Сергеев   | Окружная дорога1 | Бензин АИ-72                    |          40
 Денисов   | Окружная дорога1 | Бензин АИ-72                    |          20
 Попов       | АЗС12                       | Масло моторное МТ23-12   |          10
 Денисов   | 123КМ                        | Прокладка                      |           4
 Горбунов | Окружная дорога2 | Прокладка                      |           2
 Денисов   | У поворота            | Масло моторное МТ23-12   |           2
 Левкин     | АЗС12                       | Жидкость смывная         |          10
 Денисов   | АЗС12                       | Масло моторное УММ-23Т |          10
 Левкин     | АЗС12                       | Бензин АИ-96                    |           6
 Попов       | У поворота            | Свеча зажигания           |           4
 Горбунов | АЗС12                       | Свеча зажигания           |           6
 Денисов   | Окружная дорога1 | Жидкость смывная         |          40
 


 7.	Вывести:
a)	названия центров обслуживания, где осуществляли покупку товаров стоимостью от 8000 до 10000руб. автоводители из  
```SQL
организации владельца;
SELECT DISTINCT НАЗВАНИЕ FROM "ЦЕНТР ОБСЛУЖИВАНИЯ" WHERE ИДЕНТИФИКАТОР IN
(SELECT "ЦЕНТР ОБСЛ." FROM ЗАКАЗ WHERE ВОДИТЕЛЬ IN
(SELECT ИДЕНТИФИКАТОР FROM ВОДИТЕЛЬ WHERE АВТОПРЕДПРИЯТИЕ IN
(SELECT ВЛАДЕЛЕЦ FROM "ЦЕНТР ОБСЛУЖИВАНИЯ")) AND ИТОГО BETWEEN 8000 AND 10000
);
 ```
 
 | НАЗВАНИЕ |
 | :--------------- |
 


b)	организации-владельцы тех центров обслуживания, где приобретали не ранее февраля месяца бензин водители из АО “Автотранс”;
```SQL
SELECT DISTINCT "ЦЕНТР ОБСЛУЖИВАНИЯ".ВЛАДЕЛЕЦ FROM ЗАКАЗ
JOIN ПРЕЙСКУРАНТ ON ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ" = ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ
JOIN "ЦЕНТР ОБСЛУЖИВАНИЯ" ON "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР = ЗАКАЗ."ЦЕНТР ОБСЛ."
WHERE ЗАКАЗ.ДАТА != 'Январь' AND ВОДИТЕЛЬ.АВТОПРЕДПРИЯТИЕ = 'АО "Автотранс"'
AND ПРЕЙСКУРАНТ.ТОВАР LIKE 'Бензин%';
```
 
 | ВЛАДЕЛЕЦ |
 | :--------------- |
 | АТП1          |
 


c)	название товаров, которые покупал Денисов в чужих центрах обслуживания;
```SQL
SELECT DISTINCT ПРЕЙСКУРАНТ.ТОВАР FROM ЗАКАЗ
JOIN ПРЕЙСКУРАНТ ON ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ" = ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР
JOIN "ЦЕНТР ОБСЛУЖИВАНИЯ" ON "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР = ЗАКАЗ."ЦЕНТР ОБСЛ."
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ
WHERE ВОДИТЕЛЬ.ФАМИЛИЯ = 'Денисов' AND ВОДИТЕЛЬ.АВТОПРЕДПРИЯТИЕ != "ЦЕНТР ОБСЛУЖИВАНИЯ".ВЛАДЕЛЕЦ;
```
 
 
 | ТОВАР                              |
 | :-------------------------------------- |
 | Бензин АИ-72                    |
 | Жидкость смывная         |
 | Масло моторное МТ23-12   |
 | Масло моторное УММ-23Т |
 


d)	номера ведомостей и даты тех заказов, где центры обслуживания продавали товары, закупаемые у других организаций. Добавить названия центров, их владельцев и названия тех организаций, у кого центр обслуживания закупает товар. Отсортировать по датам.
```SQL
SELECT ЗАКАЗ."НОМЕР ВЕДОМОСТИ", ЗАКАЗ.ДАТА, "ЦЕНТР ОБСЛУЖИВАНИЯ".НАЗВАНИЕ, 
"ЦЕНТР ОБСЛУЖИВАНИЯ".ВЛАДЕЛЕЦ, ПРЕЙСКУРАНТ."У КОГО ЗАКУПАЕТСЯ" FROM ЗАКАЗ 
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ
JOIN ПРЕЙСКУРАНТ ON ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ" = ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР
JOIN "ЦЕНТР ОБСЛУЖИВАНИЯ" ON "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР = ЗАКАЗ."ЦЕНТР ОБСЛ."
WHERE "ЦЕНТР ОБСЛУЖИВАНИЯ".ВЛАДЕЛЕЦ != ПРЕЙСКУРАНТ."У КОГО ЗАКУПАЕТСЯ" ORDER BY
CASE
 WHEN ЗАКАЗ.ДАТА = 'Январь' THEN 1
 WHEN ЗАКАЗ.ДАТА = 'Февраль' THEN 2
 WHEN ЗАКАЗ.ДАТА = 'Март' THEN 3
 WHEN ЗАКАЗ.ДАТА = 'Апрель' THEN 4
 WHEN ЗАКАЗ.ДАТА = 'Май' THEN 5
 WHEN ЗАКАЗ.ДАТА = 'Июнь' THEN 6
 WHEN ЗАКАЗ.ДАТА = 'Июль' THEN 7
 WHEN ЗАКАЗ.ДАТА = 'Август' THEN 8
 WHEN ЗАКАЗ.ДАТА = 'Сентябрь' THEN 9
 WHEN ЗАКАЗ.ДАТА = 'Октябрь' THEN 10
 WHEN ЗАКАЗ.ДАТА = 'Ноябрь' THEN 11
 WHEN ЗАКАЗ.ДАТА = 'Декабрь' THEN 12
END
 ```
 
 НОМЕР ВЕДОМОСТИ | ДАТА         | НАЗВАНИЕ               | ВЛАДЕЛЕЦ          | У КОГО ЗАКУПАЕТСЯ
 ----------------------------: | :--------------- | :----------------------------- | :------------------------ | :-------------------------------
12202 | Январь     | АЗС7                        | АТП1                   | АО &quot;ФорТУНА&quot;           
12203 | Январь     | АЗС7                        | АТП1                   | АО &quot;ФорТУНА&quot;           
12209 | Апрель     | 123КМ                        | АО &quot;Автотранс&quot; | АТП1                         
12213 | Июль         | АЗС12                       | АО &quot;ФорТУНА&quot;     | АО &quot;Автотранс&quot;       
12214 | Июль         | АЗС12                       | АО &quot;ФорТУНА&quot;     | АТП1                         
12215 | Август     | У поворота            | АО &quot;ФорТУНА&quot;     | АО &quot;Автотранс&quot;       
12216 | Сентябрь | АЗС12                       | АО &quot;ФорТУНА&quot;     | АО &quot;Автотранс&quot;       
12217 | Сентябрь | Окружная дорога1 | АТП1                   | АО &quot;ФорТУНА&quot;           
 


### 8.	Создать запрос для модификации всех значений столбца с итоговой платой, чтобы он содержал истинную сумму, оплачиваемую работником ( с учетом льгот). Вывести новые значения.
```SQL
UPDATE ЗАКАЗ SET ИТОГО = ИТОГО-ИТОГО/100*ВОДИТЕЛЬ.ЛЬГОТА FROM ВОДИТЕЛЬ
WHERE ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ;

SELECT * FROM ЗАКАЗ -- проверяем
 ```
 <pre>
17 rows affected
 </pre>
 НОМЕР ВЕДОМОСТИ | ДАТА         | ВОДИТЕЛЬ | ЦЕНТР ОБСЛ. | ТОВАР ПО ПРЕЙСКУРАНТУ | КОЛ-ВО | ИТОГО
 ----------------------------: | :--------------- | ---------------: | -------------------: | ---------------------------------------: | ----------: | ---------:
12201 | Январь     |                2 |                    4 |                                        7 |           4 |      48000
12202 | Январь     |                3 |                    5 |                                        7 |           4 |      43200
12203 | Январь     |                3 |                    5 |                                        7 |           6 |      64800
12204 | Февраль   |                3 |                    3 |                                        4 |           2 |      33300
12205 | Февраль   |                4 |                    2 |                                        1 |          40 |     324000
12206 | Февраль   |                4 |                    1 |                                        1 |          40 |     324000
12207 | Март         |                3 |                    1 |                                        1 |          20 |     162000
12208 | Апрель     |                2 |                    4 |                                        3 |          10 |      70000
12209 | Апрель     |                3 |                    3 |                                        6 |           4 |      21600
12210 | Май           |                1 |                    2 |                                        6 |           2 |      11400
12211 | Май           |                3 |                    6 |                                        3 |           2 |      12600
12212 | Июнь         |                5 |                    4 |                                        7 |          10 |     114000
12213 | Июль         |                3 |                    4 |                                        4 |          10 |     166500
12214 | Июль         |                5 |                    4 |                                        2 |           6 |      57000
12215 | Август     |                2 |                    6 |                                        5 |           4 |      88000
12216 | Сентябрь |                1 |                    4 |                                        5 |           6 |     125400
12217 | Сентябрь |                3 |                    1 |                                        7 |          40 |     324000
 


### 9.	Расширить таблицу с данными о заказах столбцом, содержащим величину комиссионных для данного заказа. Создать запрос для ввода конкретных значений во все строки таблицы.
```SQL
ALTER TABLE ЗАКАЗ ADD COLUMN КОМИССИОННЫЕ smallint;

UPDATE ЗАКАЗ SET КОМИССИОННЫЕ = "ЦЕНТР ОБСЛУЖИВАНИЯ".КОМИССИОННЫЕ FROM "ЦЕНТР ОБСЛУЖИВАНИЯ"
WHERE "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР = ЗАКАЗ."ЦЕНТР ОБСЛ.";

SELECT * FROM ЗАКАЗ -- проверяем
 ```
 <pre>
 ✓
 
17 rows affected
 </pre>
 НОМЕР ВЕДОМОСТИ | ДАТА         | ВОДИТЕЛЬ | ЦЕНТР ОБСЛ. | ТОВАР ПО ПРЕЙСКУРАНТУ | КОЛ-ВО | ИТОГО | КОМИССИОННЫЕ
 ----------------------------: | :--------------- | ---------------: | -------------------: | ---------------------------------------: | ----------: | ---------: | -----------------------:
12201 | Январь     |                2 |                    4 |                                        7 |           4 |      48000 |                        4
12202 | Январь     |                3 |                    5 |                                        7 |           4 |      43200 |                        3
12203 | Январь     |                3 |                    5 |                                        7 |           6 |      64800 |                        3
12204 | Февраль   |                3 |                    3 |                                        4 |           2 |      33300 |                        2
12205 | Февраль   |                4 |                    2 |                                        1 |          40 |     324000 |                        3
12206 | Февраль   |                4 |                    1 |                                        1 |          40 |     324000 |                        3
12207 | Март         |                3 |                    1 |                                        1 |          20 |     162000 |                        3
12208 | Апрель     |                2 |                    4 |                                        3 |          10 |      70000 |                        4
12209 | Апрель     |                3 |                    3 |                                        6 |           4 |      21600 |                        2
12210 | Май           |                1 |                    2 |                                        6 |           2 |      11400 |                        3
12211 | Май           |                3 |                    6 |                                        3 |           2 |      12600 |                        4
12212 | Июнь         |                5 |                    4 |                                        7 |          10 |     114000 |                        4
12213 | Июль         |                3 |                    4 |                                        4 |          10 |     166500 |                        4
12214 | Июль         |                5 |                    4 |                                        2 |           6 |      57000 |                        4
12215 | Август     |                2 |                    6 |                                        5 |           4 |      88000 |                        4
12216 | Сентябрь |                1 |                    4 |                                        5 |           6 |     125400 |                        4
12217 | Сентябрь |                3 |                    1 |                                        7 |          40 |     324000 |                        3
 

10.	Используя операцию IN (NOT IN)  реализовать следующие запросы:
a)	найти водителей, не покупавших товаров в центрах обслуживания с комиссионными более 2%;
```SQL
SELECT ВОДИТЕЛЬ.ФАМИЛИЯ FROM ВОДИТЕЛЬ WHERE ВОДИТЕЛЬ.ИДЕНТИФИКАТОР NOT IN
(SELECT ВОДИТЕЛЬ.ИДЕНТИФИКАТОР FROM ЗАКАЗ
JOIN "ЦЕНТР ОБСЛУЖИВАНИЯ" ON "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР = ЗАКАЗ."ЦЕНТР ОБСЛ."
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ
WHERE "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР NOT IN 
(SELECT ИДЕНТИФИКАТОР FROM "ЦЕНТР ОБСЛУЖИВАНИЯ" WHERE КОМИССИОННЫЕ <=2));
 ```
 
 | ФАМИЛИЯ |
 | :------------- |
 


 b)	найти водителей, более раза покупавших моторное масло УММ-23Т в центрах обслуживания, принадлежащих чужим организациям;
 ```SQL
SELECT ФАМИЛИЯ FROM ВОДИТЕЛЬ WHERE ИДЕНТИФИКАТОР IN
(
SELECT ВОДИТЕЛЬ.ИДЕНТИФИКАТОР FROM ЗАКАЗ
JOIN ПРЕЙСКУРАНТ ON ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ" = ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР
JOIN "ЦЕНТР ОБСЛУЖИВАНИЯ" ON "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР = ЗАКАЗ."ЦЕНТР ОБСЛ."
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ
WHERE ПРЕЙСКУРАНТ.ТОВАР = 'Масло моторное УММ-23Т' AND 
ВОДИТЕЛЬ.АВТОПРЕДПРИЯТИЕ != "ЦЕНТР ОБСЛУЖИВАНИЯ".ВЛАДЕЛЕЦ 
GROUP BY ВОДИТЕЛЬ.ИДЕНТИФИКАТОР HAVING COUNT(ВОДИТЕЛЬ.ИДЕНТИФИКАТОР)>1 
);
 ```
 
 | ФАМИЛИЯ |
 | :------------- |
 


c)	запрос задания 7.а
<pre>Уже и так сделано (см. выше)</pre>
### 7.с:
```SQL
SELECT ТОВАР FROM ПРЕЙСКУРАНТ WHERE ИДЕНТИФИКАТОР IN 
(
SELECT "ТОВАР ПО ПРЕЙСКУРАНТУ" FROM ЗАКАЗ
JOIN "ЦЕНТР ОБСЛУЖИВАНИЯ" ON "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР = ЗАКАЗ."ЦЕНТР ОБСЛ."
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ
WHERE ВОДИТЕЛЬ.ФАМИЛИЯ = 'Денисов' AND ВОДИТЕЛЬ.АВТОПРЕДПРИЯТИЕ != "ЦЕНТР ОБСЛУЖИВАНИЯ".ВЛАДЕЛЕЦ
);
 ```
 
 | ТОВАР                              |
 | :-------------------------------------- |
 | Жидкость смывная         |
 | Бензин АИ-72                    |
 | Масло моторное МТ23-12   |
 | Масло моторное УММ-23Т |
 


11.	Используя операции ALL-ANY реализовать следующие запросы:
a)	найти водителей, покупавших товары, которые в мае заказывались в наибольшем количестве;
```SQL
SELECT DISTINCT ВОДИТЕЛЬ.ФАМИЛИЯ FROM ЗАКАЗ 
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ
WHERE ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ" = ANY
((
SELECT "ТОВАР ПО ПРЕЙСКУРАНТУ" FROM ЗАКАЗ WHERE ДАТА = 'Май'
GROUP BY "ТОВАР ПО ПРЕЙСКУРАНТУ"
HAVING COUNT("ТОВАР ПО ПРЕЙСКУРАНТУ") >= ALL 
(SELECT COUNT("ТОВАР ПО ПРЕЙСКУРАНТУ") as cnt
FROM ЗАКАЗ WHERE ДАТА = 'Май'
GROUP BY "ТОВАР ПО ПРЕЙСКУРАНТУ")
));
 ```
 
 | ФАМИЛИЯ   |
 | :--------------- |
 | Горбунов |
 | Денисов   |
 | Попов       |
 


b)	найти водителей, покупавших товары на максимальную сумму среди водителей своей организации;
```SQL
SELECT VO.ФАМИЛИЯ, VO.АВТОПРЕДПРИЯТИЕ FROM ЗАКАЗ
AS ZA
JOIN  ВОДИТЕЛЬ AS VO ON VO.ИДЕНТИФИКАТОР = ZA.ВОДИТЕЛЬ GROUP BY 
VO.ФАМИЛИЯ, VO.АВТОПРЕДПРИЯТИЕ, ZA.ВОДИТЕЛЬ
HAVING SUM(ИТОГО) >= ALL (SELECT SUM(ИТОГО) FROM ЗАКАЗ 
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ WHERE ВОДИТЕЛЬ.АВТОПРЕДПРИЯТИЕ = 
VO.АВТОПРЕДПРИЯТИЕ
GROUP BY (ВОДИТЕЛЬ.ИДЕНТИФИКАТОР, ВОДИТЕЛЬ.АВТОПРЕДПРИЯТИЕ));
```
 
 
 ФАМИЛИЯ | АВТОПРЕДПРИЯТИЕ
 :------------- | :-----------------------------
 Попов     | МП &quot;ФорТУНА&quot;         
 Денисов | АО &quot;Автотранс&quot;     
 Левкин   | АТП1                       
 


c)	среди водителей, покупавших товары в центре обслуживания, продавшем в январе самый дорогой товар, найти тех, кто имеет минимальный размер льгот;
```SQL
SELECT DISTINCT ФАМИЛИЯ FROM ВОДИТЕЛЬ
JOIN ЗАКАЗ ON ЗАКАЗ.ВОДИТЕЛЬ = ВОДИТЕЛЬ.ИДЕНТИФИКАТОР
WHERE ЗАКАЗ."ЦЕНТР ОБСЛ." IN
(

SELECT "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР FROM "ЦЕНТР ОБСЛУЖИВАНИЯ"
JOIN ЗАКАЗ ON ЗАКАЗ."ЦЕНТР ОБСЛ." = "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР
JOIN ПРЕЙСКУРАНТ ON ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ" = ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР
WHERE ДАТА = 'Январь'
GROUP BY "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР, ЦЕНА
HAVING ЦЕНА >= ALL
(
SELECT ЦЕНА FROM "ЦЕНТР ОБСЛУЖИВАНИЯ"
JOIN ЗАКАЗ ON ЗАКАЗ."ЦЕНТР ОБСЛ." = "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР
JOIN ПРЕЙСКУРАНТ ON ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ" = ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР
WHERE ДАТА = 'Январь'
)
) AND ЛЬГОТА <= ALL

(
SELECT DISTINCT ON (ФАМИЛИЯ) ЛЬГОТА FROM ВОДИТЕЛЬ
JOIN ЗАКАЗ ON ЗАКАЗ.ВОДИТЕЛЬ = ВОДИТЕЛЬ.ИДЕНТИФИКАТОР
WHERE ЗАКАЗ."ЦЕНТР ОБСЛ." IN
(

SELECT "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР FROM "ЦЕНТР ОБСЛУЖИВАНИЯ"
JOIN ЗАКАЗ ON ЗАКАЗ."ЦЕНТР ОБСЛ." = "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР
JOIN ПРЕЙСКУРАНТ ON ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ" = ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР
WHERE ДАТА = 'Январь'
GROUP BY "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР, ЦЕНА
HAVING ЦЕНА >= ALL
(
SELECT ЦЕНА FROM "ЦЕНТР ОБСЛУЖИВАНИЯ"
JOIN ЗАКАЗ ON ЗАКАЗ."ЦЕНТР ОБСЛ." = "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР
JOIN ПРЕЙСКУРАНТ ON ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ" = ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР
WHERE ДАТА = 'Январь'
)
)
);
 ```
 
 | ФАМИЛИЯ |
 | :------------- |
 | Попов     |
 


 d)	запрос задания 7.b.
 ```SQL
SELECT DISTINCT "ЦЕНТР ОБСЛУЖИВАНИЯ".ВЛАДЕЛЕЦ FROM "ЦЕНТР ОБСЛУЖИВАНИЯ"
WHERE ИДЕНТИФИКАТОР = ANY
(
SELECT "ЦЕНТР ОБСЛ." FROM ЗАКАЗ
JOIN ПРЕЙСКУРАНТ ON ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ" = ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ
JOIN "ЦЕНТР ОБСЛУЖИВАНИЯ" ON "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР = ЗАКАЗ."ЦЕНТР ОБСЛ."
WHERE ЗАКАЗ.ДАТА != 'Январь' AND ВОДИТЕЛЬ.АВТОПРЕДПРИЯТИЕ = 'АО "Автотранс"'
AND ПРЕЙСКУРАНТ.ТОВАР LIKE 'Бензин%'
)
 ```
 
 | ВЛАДЕЛЕЦ |
 | :--------------- |
 | АТП1          |
 


12.	Используя операцию UNION получить места работы водителей и организации, продающие товары в центры обслуживания.
```SQL
SELECT АВТОПРЕДПРИЯТИЕ FROM ВОДИТЕЛЬ UNION SELECT ВЛАДЕЛЕЦ FROM "ЦЕНТР ОБСЛУЖИВАНИЯ"
```
 
 | АВТОПРЕДПРИЯТИЕ |
 | :----------------------------- |
 | АО &quot;Автотранс&quot;      |
 | АО &quot;ФорТУНА&quot;          |
 | МП &quot;ФорТУНА&quot;          |
 | АТП1                        |
 

13.	Используя операцию EXISTS ( NOT EXISTS ) реализовать нижеследующие запросы. В случае, если для текущего состояния БД запрос будет выдавать пустое множество строк, требуется указать какие добавления в БД необходимо провести

a)	найти водителей, покупавших все товары, которые не продаются центром обслуживания с максимальным размером комиссионных;

```SQL
WITH searchValues AS
(
    SELECT DISTINCT "ТОВАР ПО ПРЕЙСКУРАНТУ" FROM ЗАКАЗ as za WHERE NOT EXISTS 
    (SELECT "НОМЕР ВЕДОМОСТИ"
    FROM ЗАКАЗ WHERE "ЗАКАЗ"."ТОВАР ПО ПРЕЙСКУРАНТУ" = za."ТОВАР ПО ПРЕЙСКУРАНТУ"
    AND "ЗАКАЗ"."ЦЕНТР ОБСЛ." IN (
    SELECT ИДЕНТИФИКАТОР FROM "ЦЕНТР ОБСЛУЖИВАНИЯ" WHERE КОМИССИОННЫЕ =
    (SELECT MAX(КОМИССИОННЫЕ) FROM "ЦЕНТР ОБСЛУЖИВАНИЯ")))
)
SELECT ФАМИЛИЯ FROM ЗАКАЗ 
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ
JOIN searchValues s ON s."ТОВАР ПО ПРЕЙСКУРАНТУ" = ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ"
WHERE s."ТОВАР ПО ПРЕЙСКУРАНТУ" = ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ"
GROUP BY ФАМИЛИЯ
HAVING COUNT(DISTINCT ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ") = (SELECT COUNT("ТОВАР ПО ПРЕЙСКУРАНТУ") FROM searchValues);
```
 
 | ФАМИЛИЯ |
 | :------------- |
 | Денисов |
 
b)	найти водителей, покупавших товары у всех центров обслуживания, принадлежащих своему предприятию;
 
```SQL
SELECT v.ФАМИЛИЯ FROM ВОДИТЕЛЬ AS v 
JOIN "ЦЕНТР ОБСЛУЖИВАНИЯ" ON v.АВТОПРЕДПРИЯТИЕ = "ЦЕНТР ОБСЛУЖИВАНИЯ".ВЛАДЕЛЕЦ
GROUP BY (v.ИДЕНТИФИКАТОР)
HAVING EXISTS ( SELECT FROM
(
SELECT ВОДИТЕЛЬ as vv, COUNT (DISTINCT "ЦЕНТР ОБСЛ.") FROM ЗАКАЗ
JOIN ВОДИТЕЛЬ ON "ЗАКАЗ".ВОДИТЕЛЬ = ВОДИТЕЛЬ.ИДЕНТИФИКАТОР
JOIN "ЦЕНТР ОБСЛУЖИВАНИЯ" ON "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР = "ЗАКАЗ"."ЦЕНТР ОБСЛ."
WHERE "ЦЕНТР ОБСЛУЖИВАНИЯ".ВЛАДЕЛЕЦ = ВОДИТЕЛЬ.АВТОПРЕДПРИЯТИЕ
GROUP BY ВОДИТЕЛЬ
) as n
WHERE v.ИДЕНТИФИКАТОР = vv AND count=COUNT("ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР)
)
```
 | ФАМИЛИЯ |
 | :------------- |
 | Денисов |

 c)	найти водителей, покупавших товары, закупаемые у своего предприятия,  в центрах обслуживания не имевших заказов после октября;
```SQL
SELECT DISTINCT ФАМИЛИЯ FROM ЗАКАЗ
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ
JOIN ПРЕЙСКУРАНТ ON ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР = ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ"
WHERE ПРЕЙСКУРАНТ."У КОГО ЗАКУПАЕТСЯ" = ВОДИТЕЛЬ."АВТОПРЕДПРИЯТИЕ"
AND EXISTS (SELECT * FROM "ЦЕНТР ОБСЛУЖИВАНИЯ" WHERE ИДЕНТИФИКАТОР = ЗАКАЗ."ЦЕНТР ОБСЛ."
AND ИДЕНТИФИКАТОР NOT IN
(
SELECT DISTINCT("ЦЕНТР ОБСЛ.") FROM "ЗАКАЗ" 
WHERE ДАТА IN ('Ноябрь', 'Декабрь')
))
```
 
 | ФАМИЛИЯ   |
 | :--------------- |
 | Горбунов |
 | Денисов   |
 | Левкин|
 


d)	определить всех водителей, делавших все заказы на итоговую сумму более 150000руб. в сентябре месяце.
```SQL
SELECT ФАМИЛИЯ FROM ВОДИТЕЛЬ WHERE EXISTS
(
SELECT FROM ЗАКАЗ WHERE ВОДИТЕЛЬ = ВОДИТЕЛЬ.ИДЕНТИФИКАТОР AND ДАТА = 'Сентябрь'
)
AND NOT EXISTS
(
SELECT FROM ЗАКАЗ WHERE ВОДИТЕЛЬ = ВОДИТЕЛЬ.ИДЕНТИФИКАТОР AND ДАТА = 'Сентябрь'
AND ИТОГО < 150000
)
``` 
 
 | ФАМИЛИЯ |
 | :------------- |
 | Денисов |
 


14.	Реализовать запросы с использованием аггрегатных функций:
 a)	какие водители приобретали в центрах обслуживания, принадлежащих чужим АТП, товары с максимальной ценой ;
```SQL
SELECT DISTINCT ФАМИЛИЯ FROM ЗАКАЗ as Z JOIN
ВОДИТЕЛЬ AS V ON V.ИДЕНТИФИКАТОР = Z.ВОДИТЕЛЬ
JOIN ПРЕЙСКУРАНТ ON ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР =  Z."ТОВАР ПО ПРЕЙСКУРАНТУ"
WHERE ЦЕНА = (
SELECT MAX (ЦЕНА) FROM ЗАКАЗ 
JOIN ПРЕЙСКУРАНТ ON ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР =  ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ"
WHERE "ЦЕНТР ОБСЛ." = Z."ЦЕНТР ОБСЛ."
) AND "ЦЕНТР ОБСЛ." NOT IN (SELECT ИДЕНТИФИКАТОР FROM "ЦЕНТР ОБСЛУЖИВАНИЯ"
WHERE ВЛАДЕЛЕЦ = V.АВТОПРЕДПРИЯТИЕ)
``` 
 
 | ФАМИЛИЯ   |
 | :--------------- |
 | Горбунов |
 | Денисов   |
 | Попов  |
 | Сергеев   |
 


 b)	определить число заказов у водителя с минимальным размером льгот;
```SQL
SELECT COUNT ("НОМЕР ВЕДОМОСТИ") FROM ЗАКАЗ WHERE ВОДИТЕЛЬ IN
(
SELECT ИДЕНТИФИКАТОР FROM ВОДИТЕЛЬ WHERE ЛЬГОТА =
(
SELECT MIN(ЛЬГОТА) FROM ВОДИТЕЛЬ
)
)
 ```
 
 | count |
 | ----: |
 |3 |
 


c)	какие товары водителями из АТП1 заказывались более 3 раз;

```SQL
SELECT ТОВАР FROM ЗАКАЗ
JOIN ПРЕЙСКУРАНТ ON ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР =  ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ"
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ WHERE
АВТОПРЕДПРИЯТИЕ = 'АТП1'
GROUP BY ТОВАР HAVING COUNT (ТОВАР) >= 3;
```
 
 | ТОВАР |
 | :--------- |
 

 d)	найти среднюю стоимость заказов, в которых заказывали свечи зажигания или товары со ценой менее 8000.
 
 ```SQL
SELECT AVG (ИТОГО) FROM ЗАКАЗ
JOIN ПРЕЙСКУРАНТ ON ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР =  ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ"
WHERE ТОВАР='Свеча зажигания' OR ЦЕНА < 8000
 ```
 
 | avg |
 | -----------------: |
 | 54833.333333333333 |
 


15.	Используя средства группировки реализовать следующие запросы:
a)	вывести названия центров обслуживания вместе с сумарной стоимостью сделанных в этих центрах заказов в том случае, если суммарная стоимость более 200000;

 ```SQL
SELECT НАЗВАНИЕ, SUM(ИТОГО) FROM ЗАКАЗ
JOIN "ЦЕНТР ОБСЛУЖИВАНИЯ" ON "ЦЕНТР ОБСЛУЖИВАНИЯ".ИДЕНТИФИКАТОР = ЗАКАЗ."ЦЕНТР ОБСЛ."
GROUP BY НАЗВАНИЕ HAVING SUM(ИТОГО) > 200000
  ```
  
 НАЗВАНИЕ|    sum
 :----------------------------- | -----:
 Окружная дорога1 | 810000
 Окружная дорога2 | 335400
 АЗС12   | 580900
 


b)	найт среди товаров, заказанных всеми водителями из АО “ФорТУНА”, те; суммарная стоимость заказов на которые, не превышала 300000
 ```SQL
SELECT ТОВАР FROM ЗАКАЗ
JOIN ПРЕЙСКУРАНТ ON ПРЕЙСКУРАНТ.ИДЕНТИФИКАТОР =  ЗАКАЗ."ТОВАР ПО ПРЕЙСКУРАНТУ"
WHERE "ТОВАР ПО ПРЕЙСКУРАНТУ" IN
(
SELECT DISTINCT "ТОВАР ПО ПРЕЙСКУРАНТУ" FROM ЗАКАЗ AS z 
JOIN ВОДИТЕЛЬ as v ON v.ИДЕНТИФИКАТОР = z.ВОДИТЕЛЬ
WHERE v.АВТОПРЕДПРИЯТИЕ LIKE '%ФорТУНА%' -- Хз, АО/МП, там опечатка, кажется
GROUP BY z."НОМЕР ВЕДОМОСТИ", v.ИДЕНТИФИКАТОР
HAVING NOT EXISTS (SELECT FROM ВОДИТЕЛЬ WHERE АВТОПРЕДПРИЯТИЕ LIKE '%ФорТУНА%'
AND NOT EXISTS (SELECT FROM ЗАКАЗ WHERE ВОДИТЕЛЬ=ВОДИТЕЛЬ.ИДЕНТИФИКАТОР AND
"ТОВАР ПО ПРЕЙСКУРАНТУ" = z."ТОВАР ПО ПРЕЙСКУРАНТУ"))
)
GROUP BY ТОВАР HAVING SUM(ИТОГО) <= 300000
  ```
 
 | ТОВАР   |
 | :------------------------------------ |
 | Масло моторное МТ23-12 |
 | Свеча зажигания    |
 


c)	для каждого месяца и центра обслуживания определить суммарную величину стоимости заказов;

 ```SQL
SELECT ДАТА, "ЦЕНТР ОБСЛ.", SUM (ИТОГО) FROM ЗАКАЗ
GROUP BY ДАТА, "ЦЕНТР ОБСЛ."
 ```
 
 ДАТА    | ЦЕНТР ОБСЛ. |    sum
 :--------------- | -------------------: | -----:
 Май |2 |  11400
 Июль    |4 | 223500
 Сентябрь |1 | 324000
 Апрель|4 |  70000
 Июнь    |4 | 114000
 Январь|4 |  48000
 Февраль   |1 | 324000
 Апрель|3 |  21600
 Август|6 |  88000
 Май |6 |  12600
 Февраль   |2 | 324000
 Февраль   |3 |  33300
 Март    |1 | 162000
 Сентябрь |4 | 125400
 Январь|5 | 108000
 


d)	вывести фамилии водителей, заказывавших за весну товаров на общую сумму более 1000000.

 ```SQL
SELECT ФАМИЛИЯ FROM ЗАКАЗ
JOIN ВОДИТЕЛЬ ON ВОДИТЕЛЬ.ИДЕНТИФИКАТОР = ЗАКАЗ.ВОДИТЕЛЬ
WHERE ДАТА IN ('Март', 'Апрель', 'Май')
GROUP BY ФАМИЛИЯ HAVING SUM(ИТОГО) > 1000000
  ```
 
 | ФАМИЛИЯ |
 | :------------- |
 


*db<>fiddle [here](https://dbfiddle.uk/?rdbms=postgres_12&fiddle=2c865126dfa66ea65a461ed75882cb94)*

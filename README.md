# data-base
 
--10.09
```
create table users(
id serial primary key
,name varchar(30)
,password varchar(128)
,email varchar(255)
,created_at timestamp
,updated_at timestamp
,amount decimal(8, 2) -- 10 в степени 8 и 2 знака после запятой
);
```

-- комментирование через два тире


-- Добавление данных
```
insert into "users"(
--"id"
"name"
,password
,"email"
,"created_at"
,"updated_at"
,"amount"
) values
('Olyalya', '123123', 'olga@mail.ru', '2002-07-30 00:00:00', '2017-10-10 00:07:53', 98),
('Leisan', 'qwerty', 'lei@mail.ru', '2002-9-21 00:00:00', '2017-10-10 00:07:53', 97),
('Lyaysan', 'qwerty007', 'lya@mail.ru', '2002-03-01 00:00:00', '2017-10-10 00:07:53', 86),
('Lena', 'qwerty123', 'lenlen@mail.ru', '2002-03-17 00:00:00', '2017-10-10 00:07:53', 77)
;
```
--insert атомарная операция
--либо все данные записываем вместе кучкой, либо по одному в зависимости от бизнес-задачи
--комлпексная вставка сильно быстрее



--узнать количество записей
select count(*) from users;

--показать расположение файлов на жестком диске
show data_directory;

--удалить все записи с таблицы users(не очищает папку, тк Postgres хранит версии)
--авто вакуум удаляет устаревшие данные, индексы
--есть принудительный вакуум(как сборщик мусора в Java)

--репликация - процесс объединения/соединения двух баз данных



delete from users;

--select запрос данных из таблицы
--insert добавление пользователей в таблицы

--ETL процесс - процесс обмена данными между базами данных
--всю базу данных можно разделить на базы для селектов и базы для инсертов


--синтаксис select: * - службное поле, которое говорит:"отобрази мне все поля"
--select *

select
DISTINCT "name" -- уникальный
--,"password" as pass — так можно заменить название колонок
,"password"pass --так тоже работает, но так делать не надо,тк работает не везде
, "amount" * 7 as mult --multiplication - умножение
from users;

select 5*5 as mult;

select 'K'*5 as check; -- так можно проверять


--существуют агригационные функции(например. сумма)

select sum("amount") from "users";

select
"name"
, sum("amount")
from "users"
group by -- считает сумму для каждого имени
"name"

--drop table "users"

--удаление одной записи
--delete from users
--where id = 7


create unique index on users (email);




---------1)

create unique index on users (id, email);

insert into "users" (id, name, password, email, created_at, updated_at, amount)
values ('16','Lena', 'qwerty123', 'lenlen@mail.ru','2002-03-17 00:00:00', '2017-10-10 00:07:53','150')
ON CONFLICT(email) DO UPDATE
SET amount = excluded.amount;


---------2)
create table messages(
id serial primary key
,id_user_from INT
,id_user_to INT
,message varchar(255)
,send_at timestamp
);

insert into "messages"(
id_user_from
,id_user_to
,message
,send_at
) values
('13', '14', 'Hello Leisan', '2021-09-17 18:05:00')
,('13', '14', 'How are you?', '2021-09-17 18:06:00')
,('14', '13', 'i, Olyalya', '2021-09-17 18:06:01')
;

create table actions(
id serial primary key
,id_user_from INT
,id_user_to INT
,action varchar(255)
,updated_at timestamp
);


insert into "actions"(
id_user_from
,id_user_to
,action
,updated_at

) values
('13', '14', 'send message', '2021-09-17 18:05:00')
,('13', '14', 'send message', '2021-09-17 18:06:00')
,('14', '13', 'send message', '2021-09-17 18:06:01')
;

--дз
--1)запрос insert into users нужно преобразовать в паттерн insert или update
--(смотреть будут именно sql-запрос), его нужно проверить
--нужно добавить уникальный индекс в базу данных

--2)создать к юзерам две таблицы(любые, например actions и messages)
--не менее 4-5 полей(столбцов)
--обязательное поле - id user
--так мы сделаем отношение один ко многим
--проверка: мы знаем что у одного пользователя много строчек/сообщений

____



--- 17.09
---------------------
--дз
--написать sql-запросы ко всем Joinам
--из картинки кроме left inclusive
---------------------
--соединение колонок - join
--при селекте создается новая таблица

--два подхода: запросы в бд
--либо два простых запроса и в коде соединить


--в бд перед выполнением sql запроса база данных строит план выполнения sql запросы
--для специализированных данных(например параметры ДНК)может лучше в коде, если
--знаешь как отсортировать данные оптимальнее


--Alias







-- с помощью explain  можем посмотреть план запроса
--scan проходит по строчкам таблицы


explain  select *
from
     "users" u
left join
         "actions" a
         on (
             u."id" = a."id_user_from"
             )
where u."id" = 13 or u."id" = 14;           --логическое выражение(можно использовать и/или)



/*left inclusive*/
select *
from
     "users" u
left join
         "actions" a
         on (
             u."id" = a."id_user_from"
             );

/*left exclusive*/
select *
from
     "users" u
left join
         "actions" a
         on (
             u."id" = a."id_user_from"
             )
where a."id" is NULL;
/*full outer inclusive*/
select *
from
     "users" u
full outer join
         "actions" a
         on (
             u."id" = a."id_user_from"
             );
/*inner join*/
select *
from
     "users" u
inner join
         "actions" a
         on (
             u."id" = a."id_user_from"
             );

/*full outer exclusive*/
select *
from
     "users" u
full outer join
         "actions" a
         on (
             u."id" = a."id_user_from"
             );
/*right inclusive*/
select *
from
     "users" u
right join
         "actions" a
         on (
             u."id" = a."id_user_from"
             );

/*right exclusive*/
select *
from
     "users" u
right join
         "actions" a
         on (
             u."id" = a."id_user_from"
             )
where a."id" is NULL;





--выбрать все сообщения и соответствующие им индексы
--так можно сделать связь многие ко многим
select*
from
     "messages" m
left join
         "users" u
         on (
             m."id_user_from" = u."id"
             )
where u."id" = 13 or u."id" = 14;


/*
users:
id
name
email
role

role:
id
title
permission_id

permission:
id
permission_id
*/

____

/*24.09*/

create table roles(
id serial primary key
,title varchar(30)
);

create table permissions(
id serial primary key
, permission_level varchar(30)
,action varchar(255)
);

create table users(
id serial primary key
,name varchar(30)
,password varchar(128)
,email varchar(255)
);


insert into "users"(
--"id"
"name"
,password
,"email"
) values
('Olyalya', '123123', 'olga@mail.ru'),
('Leisan', 'qwerty', 'lei@mail.ru'),
('Lyaysan', 'qwerty007', 'lya@mail.ru'),
('Lena', 'qwerty123', 'lenlen@mail.ru'),
('June', 'hey', 'june17@bk.ru')
;



insert into "permissions"(
"permission_level"
,"action"
) values
('1', 'drop tables')
,('2', 'update tables')
,('3', 'read tables')
,('4', 'create table')
,('5', 'see table')
,('6', 'create permission')
,('7', 'update permission')
,('8', 'look on permission')
;

insert into "roles"(
"title"
) values
('editor')
,('admin')
,('reader')
;

create table users_roles(
id_user int
,id_role int
);

create table roles_permission(
id_role int
,id_permission int
);

insert into "users_roles"(
--"id"
"id_user"
,"id_role"
,"exeired Date"
) values
('1', '3', 'now')
, ('2', '1', 'NULL')
;



insert into "roles_permission"(
--"id"
"id_role"
,"id_permission"
) values
('1', '4')
, ('2', '2')
;
alter table "users_roles" add column "exeired Date" varchar(30);


insert into "roles_permission"(
--"id"
"id_role"
,"id_permission"
) values
('1', '4')
, ('2', '2')
;



/*по id пользователя все действующие роли и разрешения*/



explain select *
from
     "users" u
left join "users_roles" us
         on (
             u."id" = us."id_user"
             )
left join "roles" r
        on (
            us."id_role" = r."id"
            )
left join "roles_permission" rp
       on(
           r."id" = rp."id_role"
           )
left join "permissions" p
      on(
          rp."id_permission" = p."id"
          )
where "exeired Date" = 'now';




/*
 добавить три таблицы
 documents
 customers - клиент
 employer - таблица сотрудников компании


 организованить permission для владельца документа(первого, кто создал)
 только владелец может клиента трогать


 (продумать структуру таблицы, как можем дальше sql-запросы к ней писать и всё подобное )

 продумать разрешение для собтственника документа
 */
 
____

/* дз */
create table documents(
id serial primary key
,title varchar(30)
,created_at timestamp
);

/* не нужно
create table customers(
id serial primary key
,firstname varchar(30)
,lastname varchar(30)
);

create table employees(
id serial primary key
,firstname varchar(30)
,lastname varchar(30)
,post varchar(30)
);

/* таблица для доступа к документу */
create table employee_documents(
id_employee varchar(30)
, id_document varchar(30)
);

/* связь документа и клиента*/
create table documents_customer(
id_customer varchar(30)
, id_document varchar(30)
);

*/

create unique index on documents (id);
/*
 доделать(без служебных таблиц, через служебные поля)

 плюс добавить select

 select * from doc where owner_id = ?

 update ... where owner id = ? or (role = 'Admin')                            -- роль через джоины мы можем сделать через подзапрос(в скобочках таких пишем '' )

 служебное поле - один ко многим
 уникальность - один к одному
 */



create table documents(
id serial primary key
,title varchar(30)
,created_at timestamp
,owner_id varchar(30)
);

select *
from
     "users" u
left join "documents" d
       on(
             u."id" = d."owner_id"
        )
where d."owner_id" = ?;





select *
from
     "users" u
left join "users_roles" ur
       on(
             u."id" = ur."id_user"
        )
left join "roles" r
       on(
             ur."id_role" = r."id"
        )
where r."title" = 'admin';


insert into "documents" (id, title, owner_id)
values ('2','name_cats','6')
ON CONFLICT(id) DO UPDATE
SET title = excluded.title;


select *
from
     "users" u
left join "users_roles" ur
       on(
             u."id" = ur."id_user"
        )
left join "roles" r
       on(
             ur."id_role" = r."id"
        )
where r."title" == 'admin';
insert into "users"(
--"id"
"name"
,password
,"email"
) values
('Black', '123', 'black@gmail.ru')
,('Yellow', '321', 'yellow@mail.ru')
,('White', 'hello', 'hello@gmail.ru')
;
insert into "roles"(
"title"
) values
('admin')
,('customer')
;

insert into "users_roles"(
"id_user"
,"id_role"
) values
(6, 5)
,(7, 6)
,(8, 6)
;

insert into "documents"(
"title"
,"created_at"
,"owner_id"
) values
('flowers', '2017-10-10 00:07:53', '6')
,('timetable', '2021-10-10 00:07:53', '6')
;

____


 /* 01.10.2021
    два уровня доступа к серверу postgres


pg_hba.conf Добавляет ещё один уровень доступа к постгрису (находится в data)

    host all all ip/mask mds sha 256
    по опредленному хосту ко всем базам данным ко всем серверам подключение к серверу с логином паролем

    по умолчанию ip/mask 127.0.0.1/32 (обращение к своему компьютеру)

    чтобы указать любые адреса то 0.0.0.0/0  -- чтобы подключиться к кому-то ещё
    */



/* 08.10.2021*/
CREATE TABLE public.newtable (
  item varchar NULL,
  id serial NOT NULL,
  description varchar NULL,
  cost_price float8 NULL,
  sell_price float8 NULL
);

INSERT INTO public.newtable
(item, description, cost_price, sell_price)
VALUES('Item1', 'Desc1', 16.03, 20.45);

INSERT INTO public.newtable
(item, description, cost_price, sell_price)
VALUES('Item1', 'Desc1', 17.03, 13.45);
INSERT INTO public.newtable
(item, description, cost_price, sell_price)
VALUES('Item2', 'Desc2', 13.03, 19.45);
INSERT INTO public.newtable
(item, description, cost_price, sell_price)
VALUES('Item2', 'Desc8', 15.03, 25.45);
INSERT INTO public.newtable
(item, description, cost_price, sell_price)
VALUES('Item3', 'Desc3', 18.03, 27.45);
INSERT INTO public.newtable
(item, description, cost_price, sell_price)
VALUES('Item4', 'Desc4', 17.03, 28.45);
INSERT INTO public.newtable
(item, description, cost_price, sell_price)
VALUES('Item5', 'Desc5', 14.03, 17.45);
INSERT INTO public.newtable
(item, description, cost_price, sell_price)
VALUES('Item6', 'Desc6', 19.03, 22.45);
INSERT INTO public.newtable
(item, description, cost_price, sell_price)
VALUES('Item7', 'Desc7', 16.03, 23.45);
INSERT INTO public.newtable
(item, description, cost_price, sell_price)
VALUES('Item8', 'Desc8', 16.03, 20.45);
INSERT INTO public.newtable
(item, description, cost_price, sell_price)
VALUES('Item9', 'Desc9', 16.03, 20.45);


/*
 select создает новую таблицу
 после селекта пишем поля которыые мы добавляем в новую таблице


 */

/*все поля таблицы n
  n - псевдоним таблицы(они существуют и для полей)



  as - явное указание псевдонима
можно и item new_item, но не рекомендуется
  */

select item as new_item
from
   newtable n;



/*выборка только уникальных полей

  лучше использовать реже, тк очень нагружает систему


  (есть ещё один способ проверить на уникальность, о нем позже узнаем)
  */
select
      DISTINCT item, description
from
     newtable n




/*
из поля с рублями пытаемся получить поле с копейками


Без as price_in_kops колонка будет с названием ?column?

Псевдонимы колонок мы прописываем чтобы на бэке было легче их добавить в map

К выборке можно добавить ключ уникальности

C флоатом никогда не работать с деньгами(тк получится цифра несколько нулей после числа а потом не ноль (многие огранизации поэтому деньги в копейках хранят)
1.поэтому часто хранят в интах
2.можно использовать децимал(но его редко используют)


CAST - приведение типов
часто с помощью этой функции кастуют дату в timestamp и наоборот

)
*/
select
    item,
    CAST(sell_price * 100 as int) as price_in_kops
from newtable;


/*агригационные функции:
  -они объединяют все строки в одну, вычисляя что-то
  */
select
    sum(sell_price)
from newtable


/*среднее значение*/
select
    avg(sell_price)
from newtable;

/*тут аккуратнее надо быть, тк если поле Null то оно не посчитается*/
select
    count(sell_price)
from newtable
/*решение(со звездочкой все посчитается)*/
select
    count(*)
from newtable

/*перечисление

  array_agg - Объединение всех строк в одну(массив из данных поля которое в скобочках)
  */
select
    count(*), sum(sell_price), array_agg(cost_price)
from newtable;



/*сортировка по полям item, sell_price */
select
*
from  newtable n
order by
item, sell_price


/*
 asc - сортировка от меньшего к большему
 desc - сортировка от большего к меньшему
 */
select
*
from  newtable n
order by
item desc;

/*ограничили кол-во элементов в выводе - в примере 5 первых элементов*/
select
*
from  newtable n
order by
item desc
limit 5;


/*
limit
offset (это сдвиг. В примере на сколько сдвиг - на 5 записей)
для пагинации(сколько записей на странице)
*/
select
*
from  newtable n
order by
item desc
limit 5
offset 5;

/*
 чтобы из страницы получить оффсет
 */
select
*
from  newtable n
order by
item desc
limit 5
page * offset + 1;


/*
 если пишем лимит оффсет то обязательно пишем ордер бай(тк мы не знаем какая сортировка внутри приложения и нам нужно задать свою)
 */


/*------------------------------------------------------*/
select
*
from  newtable n
where <, <=, <, >=, =, <>



/*pattern matching
  % - любое количество символов

  индекс - служебная информация для ускорение поиска по бд(ведет сама бд):
  когда %es и es% мы можем построить индекс(хорошие паттерны)
  когда e%s и %es% мы не можем построить индекс(плохие паттерны)
  */
select
*
from  newtable n
where
description like '%es%'
order by item desc
limit 20
offset 0;


-- _ - один любой символ(тут тоже скорее всего не создатся индекс)

select
*
from  newtable n
where -- AND, OR, NOR
description like '_e%'
  and sell_price > 20
order by item desc
limit 20
offset 0;

--в коде если фильтры не добавляем то проблема:
select
*
from  newtable n
where
--description like '_e%'
--and sell_price > 20
order by item desc
limit 20
offset 0;

--решение
select
*
from  newtable n
where 1==1
--and description like '_e%'
--and sell_price > 20
order by item desc
limit 20
offset 0;



select
*
from  newtable n
where
description between 'A' and 'D' --выведет все значения тк он обрезает все строки до нужной длины (т.е в нашем случае обрезало от decs и получило d)
order by item desc
limit 20
offset 0;

--C between надо осторожнее. работает не так как > <  а обрезает до нужной длины


select
*
from  newtable n
where
description in ('Desc1', 'Desc3') -- вложенный цикл. (тут смотрит description либо в состоянии 1 либо в состоянии 3)
order by item desc
limit 20
offset 0;


-- если сделать одно из полей NULL, то результат неверный тк нужно писать не sell_price = NULL, а sell_price is NULL (или sell_price is not NULL)
-- в постгрисе false != NULL:
--можно для заполнения таблицу сделать обязательно и дефолтной
select
*
from  newtable n
where
sell_price = NULL
order by item desc
limit 20
offset 0;

/* Вывести  номер зачетки, название хобби и
длительность в месяцах для всех завершенных
хобби, отсортировав данные по зачетке по
возрастанию, в пределах зачетки по длительности по убыванию.

--1.6
select n_z, h_name, d_start, d_finish,
extract(year from age(d_finish, d_start))*12 + extract(month from age(d_finish, d_start))
from public."Students_Hobby" where d_finish is not Null
order by extract(month from d_start)

Вывести  фамилию, имя, зачетку, группу, месяц и год рождения
всех отличников 2-го курса, отсортировав по группе во возрастанию,
в пределах группы по возрастанию даты рождения.

--1.7
select f_name, s_name, n_gr, ball, data_b from students
where ball = 5
order by n_gr ASC, data_b DESC


Вывести  фамилию, имя, зачетку, количество полных лет
для каждого из студентов 2-х заданных групп,
увлекающихся на текущую дату заданным типом хобби,
отсортировав данные по группе по возрастанию,
в пределах группы по возрастанию возраста.


--2.6
select S.s_name, S.f_name, S.n_z, extract(year from age(current_date, S.data_b))
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.h_name = H.h_name and S.n_z = SH.n_z and
S.n_gr in (2011, 2012) and SH.d_finish is null and
H.h_type in ('спорт',
			'искусство')
order by S.n_gr, S.data_b

 Вывести  названия и риск тех хобби,
 которыми увлекаются только отличники
 1,2 или 3 курсов, отсортировав данные
 по убыванию риска.


--2.7
select H.h_name, risk
from public."students" S, public."Students_Hobby" SH,public."hobby" H
where SH.h_name = H.h_name and S.n_z = SH.n_z and ball = '5' and Substring(S.n_gr::text, 1, 1) in ('1', '2', '3')
order by 2 desc

Найти название, тип, риск, длительность
в месяцах самого продолжительного хобби
из действующих, указав номер зачетки
студента и номер его группы.

--3.6
select H.h_name, H.h_type,
       EXTRACT(MONTH FROM AGE(CURRENT_DATE, MIN(SH.d_start))) AS duration_months,
       S.n_z, S.n_gr--, MIN(SH.d_start), SH.d_finish
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.n_z = S.n_z
  and SH.h_name = H.h_name
  and SH.d_finish IS NULL
group by H.h_name, H.h_type, S.n_z--, S.n_gr, SH.d_finish
order by min(SH.d_start)
limit 1;

--Найти все действующие хобби, которыми увлекаются троечники 2-го курса.
--3.8
SELECT H.h_name, H.h_type
FROM public."students" S, public."Students_Hobby" SH, public."hobby" H
WHERE S.n_z = SH.n_z AND SH.h_name = H.h_name AND S.n_gr in (2011, 2012, 2013)
	AND ball >= 2.5 and ball <= 3.5
	AND SH.d_finish IS NULL;


--Найти номера групп, в которых не менее 50% студентов имеют балл не ниже 4.
--3.10
SELECT n_gr
FROM (
    SELECT S.n_gr, COUNT(S.ball >= 4) AS good_grades_count, COUNT(*) AS cnt_students
    FROM public."students" S
    GROUP BY S.n_gr
) AS grades_info
WHERE good_grades_count * 2 >= cnt_students
ORDER BY n_gr
*/

/*
Основные групповые (агрегатные) функции в SQL:
COUNT(): Возвращает количество строк в результате запроса.
SUM(): Вычисляет сумму значений в указанном столбце.
AVG(): Вычисляет среднее значение в указанном столбце.
MIN(): Возвращает минимальное значение в указанном столбце.
MAX(): Возвращает максимальное значение в указанном столбце.
*/

/*
--3.7
Найти всех студентов, чей балл выше среднего,
отсортировав по номеру группы по возрастанию,
в пределах группы по убыванию балла.
--3.8
Найти все действующие хобби, которыми увлекаются
троечники 2-го курса.
--3.9
Найти номера курсов, на которых студенты имеют
в среднем более одного действующего хобби.
--3.10
Найти номера групп, в которых не менее 50%
студентов имеют балл не ниже 4.
--3.11
Для каждого курса подсчитать количество
различных  действующих хобби на курсе.
--3.11
Вывести  фамилию, имя, зачетку, группу,
количество  всех действующих хобби для тех студентов, которые имеют максимальных балл в своей группе.
*/

/*
insert into public."students" values (13, 'Константин', 'Сергеев', '2002.03.12', 3014, 4.0);
insert into public."hobby" values ('плавание', 'спорт', 5);
insert into public."Students_Hobby"
values(9, 10, 'спортивное программирование', '2020.01.01', '2022.02.03')


--1.1
select f_name, s_name, n_z, data_b, ball from students
where ball >= 3 and ball <= 4
and extract (month from data_b) = extract (month from CURRENT_DATE);

--1.5
select h_name, d_start, d_finish from public."Students_Hobby" where d_finish is null
and extract(month from d_start) in (6, 7, 8)
order by extract(month from d_start);

--1.6
select n_z, h_name, d_start, d_finish,
extract(year from age(d_finish, d_start))*12 + extract(month from age(d_finish, d_start))
from public."Students_Hobby" where d_finish is not Null
order by extract(month from d_start)

--для вывода только месяцов
select n_z, h_name, d_start, d_finish,
extract(month from age(d_finish, d_start))
from public."Students_Hobby" where d_finish is not Null
order by extract(month from d_start)

--1.7
select f_name, s_name, n_gr, ball, data_b from students
where ball = 5
order by n_gr ASC, data_b DESC

--2.1
select s_name, f_name, SH.n_z, S.data_b, H.h_name, extract(year from age(d_finish, d_start))*12 + extract(month from age(d_finish, d_start))
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where d_finish is not null and SH.h_name = H.h_name and S.n_z = SH.n_z
order by SH.n_z

--2.2
select H.h_name, H.risk, H.h_type, SH.n_z
from public."hobby" H, public."Students_Hobby" SH, public."students" S
where SH.h_name = H.h_name and S.n_z = SH.n_z and
d_finish is null

--2.3
select distinct S.f_name, S.s_name, S.n_z, S.data_b, S.ball
from public."hobby" H, public."students" S, public."Students_Hobby" SH
where SH.h_name = H.h_name and S.n_z = SH.n_z and
ball >= 4 and ball <= 5 and
extract(month from S.data_b) in (extract(month from CURRENT_DATE),
									 extract(month from CURRENT_DATE) + 1)

--2.4
select S.f_name, S.s_name, S.n_z, S.data_b, SH.h_name
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.h_name = H.h_name and S.n_z = SH.n_z and
SH.d_finish is not null and
extract(year from age(current_date, S.data_b)) > 18 and
extract(month from S.data_b) = extract(month from current_date)

--2.5
select distinct S.f_name, S.s_name, S.n_z, S.data_b, substring(S.n_gr::text, 0, 2)
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where S.ball = 5 and S.n_z not in (select SH.n_z from public."Students_Hobby" SH)
order by substring(S.n_gr::text, 0, 2), data_b desc

--2.6
--select S.f_name, S.s_name, S.n_z, S.data_b, extract(year from age(current_date, S.data_b)), H.h_type, H.h_name, S.n_gr
select S.s_name, S.f_name, S.n_z, extract(year from age(current_date, S.data_b))
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.h_name = H.h_name and S.n_z = SH.n_z and
S.n_gr in (2011, 2012) and SH.d_finish is null and
H.h_type in ('спорт',
			'искусство')
order by S.n_gr, S.data_b


--2.7
select H.h_name, risk
from public."students" S, public."Students_Hobby" SH,public."hobby" H
where SH.h_name = H.h_name and S.n_z = SH.n_z and ball = '5' and Substring(S.n_gr::text, 1, 1) in ('1', '2', '3')
order by 2 desc

--??
--select S.f_name, S.s_name, S.n_z, S.data_b, extract(year from age(current_date, S.data_b)), H.h_type, H.h_name, S.n_gr
select S.s_name, S.f_name, S.n_z, extract(year from age(current_date, S.data_b))
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.h_name = H.h_name and S.n_z = SH.n_z and
S.n_gr in (2011, 2012) and SH.d_finish is null and
H.h_type in ('спорт',
			'искусство')
order by S.n_gr, S.data_b

--хобби которым никто не занимается
select * from hobby
where h_name not in (select distinct h_name from public."Students_Hobby")
order by risk desc

--3.1
insert into public."Students_Hobby"
values(22, 15, 'игра в симфоническом оркестре', '2014.05.14', null);

select H.h_name, H.risk, count(distinct S.n_gr)
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.d_finish is null and S.n_z = SH.n_z and H.h_name = SH.h_name
group by H.h_name, H.risk;

--3.2.1
select S.f_name, S.s_name, S.n_z, S.data_b
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.n_z = S.n_z
      and SH.n_z = S.n_z
      and SH.d_finish is NULL
      and extract(year from age(current_date, S.data_b)) >= 18
group by S.f_name, S.s_name, S.n_z, S.data_b;

--3.2.2
select S.f_name, S.s_name, S.n_z, S.data_b
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.n_z = S.n_z
      and SH.d_finish is NULL
      and extract(year from age(current_date, S.data_b)) >= 18
group by S.f_name, S.s_name, S.n_z, S.data_b
having count(SH.h_name) > 1;


--3.3
select H.h_name, H.risk
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where S.ball = (select max(ball) from public."students")
      and S.n_z = SH.n_z
      and SH.d_finish IS NULL
      and SH.h_name = H.h_name;

-----------------------------
--3.4
select S.n_gr, AVG(ball)
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.n_z = S.n_z
	and SH.h_name = H.h_name
	and SH.d_finish is NULL
	and H.h_type = 'искусство'
group by S.n_gr;

--3.5
select distinct S.n_gr
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.n_z = S.n_z
	and S.ball = 5
    and DATE_PART('month', AGE(current_date, SH.d_start)) >= 5
	and H.h_type = 'спорт'

--3.6
select H.h_name, H.h_type,
       EXTRACT(MONTH FROM AGE(CURRENT_DATE, MIN(SH.d_start))) AS duration_months,
       S.n_z, S.n_gr--, MIN(SH.d_start), SH.d_finish
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.n_z = S.n_z
  and SH.h_name = H.h_name
  and SH.d_finish IS NULL
group by H.h_name, H.h_type, S.n_z--, S.n_gr, SH.d_finish
order by min(SH.d_start)
limit 1;
*/

/* Вывести  номер зачетки, название хобби и
длительность в месяцах для всех завершенных
хобби, отсортировав данные по зачетке по
возрастанию, в пределах зачетки по длительности по убыванию.

--1.6
select n_z, h_name, d_start, d_finish,
extract(year from age(d_finish, d_start))*12 + extract(month from age(d_finish, d_start))
from public."Students_Hobby" where d_finish is not Null
order by extract(month from d_start)

Вывести  фамилию, имя, зачетку, группу, месяц и год рождения
всех отличников 2-го курса, отсортировав по группе во возрастанию,
в пределах группы по возрастанию даты рождения.

--1.7
select f_name, s_name, n_gr, ball, data_b from students
where ball = 5
order by n_gr ASC, data_b DESC


Вывести  фамилию, имя, зачетку, количество полных лет
для каждого из студентов 2-х заданных групп,
увлекающихся на текущую дату заданным типом хобби,
отсортировав данные по группе по возрастанию,
в пределах группы по возрастанию возраста.


--2.6
select S.s_name, S.f_name, S.n_z, extract(year from age(current_date, S.data_b))
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.h_name = H.h_name and S.n_z = SH.n_z and
S.n_gr in (2011, 2012) and SH.d_finish is null and
H.h_type in ('спорт',
			'искусство')
order by S.n_gr, S.data_b

 Вывести  названия и риск тех хобби,
 которыми увлекаются только отличники
 1,2 или 3 курсов, отсортировав данные
 по убыванию риска.


--2.7
select H.h_name, risk
from public."students" S, public."Students_Hobby" SH,public."hobby" H
where SH.h_name = H.h_name and S.n_z = SH.n_z and ball = '5' and Substring(S.n_gr::text, 1, 1) in ('1', '2', '3')
order by 2 desc

Найти название, тип, риск, длительность
в месяцах самого продолжительного хобби
из действующих, указав номер зачетки
студента и номер его группы.

--3.6
select H.h_name, H.h_type,
       EXTRACT(MONTH FROM AGE(CURRENT_DATE, MIN(SH.d_start))) AS duration_months,
       S.n_z, S.n_gr--, MIN(SH.d_start), SH.d_finish
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.n_z = S.n_z
  and SH.h_name = H.h_name
  and SH.d_finish IS NULL
group by H.h_name, H.h_type, S.n_z--, S.n_gr, SH.d_finish
order by min(SH.d_start)
limit 1;

--Найти все действующие хобби, которыми увлекаются троечники 2-го курса.
--3.8
SELECT H.h_name, H.h_type
FROM public."students" S, public."Students_Hobby" SH, public."hobby" H
WHERE S.n_z = SH.n_z AND SH.h_name = H.h_name AND S.n_gr in (2011, 2012, 2013)
	AND ball >= 2.5 and ball <= 3.5
	AND SH.d_finish IS NULL;


--Найти номера групп, в которых не менее 50% студентов имеют балл не ниже 4.
--3.10
SELECT n_gr
FROM (
    SELECT S.n_gr, COUNT(S.ball >= 4) AS good_grades_count, COUNT(*) AS cnt_students
    FROM public."students" S
    GROUP BY S.n_gr
) AS grades_info
WHERE good_grades_count * 2 >= cnt_students
ORDER BY n_gr
*/

/*
Основные групповые (агрегатные) функции в SQL:
COUNT(): Возвращает количество строк в результате запроса.
SUM(): Вычисляет сумму значений в указанном столбце.
AVG(): Вычисляет среднее значение в указанном столбце.
MIN(): Возвращает минимальное значение в указанном столбце.
MAX(): Возвращает максимальное значение в указанном столбце.
*/

/*
--3.7
Найти всех студентов, чей балл выше среднего,
отсортировав по номеру группы по возрастанию,
в пределах группы по убыванию балла.
--3.8
Найти все действующие хобби, которыми увлекаются
троечники 2-го курса.
--3.9
Найти номера курсов, на которых студенты имеют
в среднем более одного действующего хобби.
--3.10
Найти номера групп, в которых не менее 50%
студентов имеют балл не ниже 4.
--3.11
Для каждого курса подсчитать количество
различных  действующих хобби на курсе.
--3.11
Вывести  фамилию, имя, зачетку, группу,
количество  всех действующих хобби для тех студентов, которые имеют максимальных балл в своей группе.
*/

/*
insert into public."students" values (13, 'Константин', 'Сергеев', '2002.03.12', 3014, 4.0);
insert into public."hobby" values ('плавание', 'спорт', 5);
insert into public."Students_Hobby"
values(9, 10, 'спортивное программирование', '2020.01.01', '2022.02.03')


--1.1
select f_name, s_name, n_z, data_b, ball from students
where ball >= 3 and ball <= 4
and extract (month from data_b) = extract (month from CURRENT_DATE);

--1.5
select h_name, d_start, d_finish from public."Students_Hobby" where d_finish is null
and extract(month from d_start) in (6, 7, 8)
order by extract(month from d_start);

--1.6
select n_z, h_name, d_start, d_finish,
extract(year from age(d_finish, d_start))*12 + extract(month from age(d_finish, d_start))
from public."Students_Hobby" where d_finish is not Null
order by extract(month from d_start)

--для вывода только месяцов
select n_z, h_name, d_start, d_finish,
extract(month from age(d_finish, d_start))
from public."Students_Hobby" where d_finish is not Null
order by extract(month from d_start)

--1.7
select f_name, s_name, n_gr, ball, data_b from students
where ball = 5
order by n_gr ASC, data_b DESC

--2.1
select s_name, f_name, SH.n_z, S.data_b, H.h_name, extract(year from age(d_finish, d_start))*12 + extract(month from age(d_finish, d_start))
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where d_finish is not null and SH.h_name = H.h_name and S.n_z = SH.n_z
order by SH.n_z

--2.2
select H.h_name, H.risk, H.h_type, SH.n_z
from public."hobby" H, public."Students_Hobby" SH, public."students" S
where SH.h_name = H.h_name and S.n_z = SH.n_z and
d_finish is null

--2.3
select distinct S.f_name, S.s_name, S.n_z, S.data_b, S.ball
from public."hobby" H, public."students" S, public."Students_Hobby" SH
where SH.h_name = H.h_name and S.n_z = SH.n_z and
ball >= 4 and ball <= 5 and
extract(month from S.data_b) in (extract(month from CURRENT_DATE),
									 extract(month from CURRENT_DATE) + 1)

--2.4
select S.f_name, S.s_name, S.n_z, S.data_b, SH.h_name
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.h_name = H.h_name and S.n_z = SH.n_z and
SH.d_finish is not null and
extract(year from age(current_date, S.data_b)) > 18 and
extract(month from S.data_b) = extract(month from current_date)

--2.5
select distinct S.f_name, S.s_name, S.n_z, S.data_b, substring(S.n_gr::text, 0, 2)
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where S.ball = 5 and S.n_z not in (select SH.n_z from public."Students_Hobby" SH)
order by substring(S.n_gr::text, 0, 2), data_b desc

--2.6
--select S.f_name, S.s_name, S.n_z, S.data_b, extract(year from age(current_date, S.data_b)), H.h_type, H.h_name, S.n_gr
select S.s_name, S.f_name, S.n_z, extract(year from age(current_date, S.data_b))
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.h_name = H.h_name and S.n_z = SH.n_z and
S.n_gr in (2011, 2012) and SH.d_finish is null and
H.h_type in ('спорт',
			'искусство')
order by S.n_gr, S.data_b


--2.7
select H.h_name, risk
from public."students" S, public."Students_Hobby" SH,public."hobby" H
where SH.h_name = H.h_name and S.n_z = SH.n_z and ball = '5' and Substring(S.n_gr::text, 1, 1) in ('1', '2', '3')
order by 2 desc

--??
--select S.f_name, S.s_name, S.n_z, S.data_b, extract(year from age(current_date, S.data_b)), H.h_type, H.h_name, S.n_gr
select S.s_name, S.f_name, S.n_z, extract(year from age(current_date, S.data_b))
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.h_name = H.h_name and S.n_z = SH.n_z and
S.n_gr in (2011, 2012) and SH.d_finish is null and
H.h_type in ('спорт',
			'искусство')
order by S.n_gr, S.data_b

--хобби которым никто не занимается
select * from hobby
where h_name not in (select distinct h_name from public."Students_Hobby")
order by risk desc

--3.1
insert into public."Students_Hobby"
values(22, 15, 'игра в симфоническом оркестре', '2014.05.14', null);

select H.h_name, H.risk, count(distinct S.n_gr)
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.d_finish is null and S.n_z = SH.n_z and H.h_name = SH.h_name
group by H.h_name, H.risk;

--3.2.1
select S.f_name, S.s_name, S.n_z, S.data_b
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.n_z = S.n_z
      and SH.n_z = S.n_z
      and SH.d_finish is NULL
      and extract(year from age(current_date, S.data_b)) >= 18
group by S.f_name, S.s_name, S.n_z, S.data_b;

--3.2.2
select S.f_name, S.s_name, S.n_z, S.data_b
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.n_z = S.n_z
      and SH.d_finish is NULL
      and extract(year from age(current_date, S.data_b)) >= 18
group by S.f_name, S.s_name, S.n_z, S.data_b
having count(SH.h_name) > 1;


--3.3
select H.h_name, H.risk
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where S.ball = (select max(ball) from public."students")
      and S.n_z = SH.n_z
      and SH.d_finish IS NULL
      and SH.h_name = H.h_name;

-----------------------------
--3.4
select S.n_gr, AVG(ball)
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.n_z = S.n_z
	and SH.h_name = H.h_name
	and SH.d_finish is NULL
	and H.h_type = 'искусство'
group by S.n_gr;

--3.5
select distinct S.n_gr
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.n_z = S.n_z
	and S.ball = 5
    and DATE_PART('month', AGE(current_date, SH.d_start)) >= 5
	and H.h_type = 'спорт'

--3.6
select H.h_name, H.h_type,
       EXTRACT(MONTH FROM AGE(CURRENT_DATE, MIN(SH.d_start))) AS duration_months,
       S.n_z, S.n_gr--, MIN(SH.d_start), SH.d_finish
from public."students" S, public."Students_Hobby" SH, public."hobby" H
where SH.n_z = S.n_z
  and SH.h_name = H.h_name
  and SH.d_finish IS NULL
group by H.h_name, H.h_type, S.n_z--, S.n_gr, SH.d_finish
order by min(SH.d_start)
limit 1;
*/

CREATE TABLE "Дилеры" (
    "ИД_Дилера" SERIAL PRIMARY KEY,
    "Название" VARCHAR(100),
    "Процент" DECIMAL(5,2),
    "Комментарии" TEXT
);

CREATE TABLE "Менеджеры" (
    "ИД_Менеджера" SERIAL PRIMARY KEY,
    "ИД_Дилера" INT REFERENCES "Дилеры"("ИД_Дилера"),
    "Имя" VARCHAR(100),
    "Процент" DECIMAL(5,2),
    "Дата_найма" DATE,
    "Комментарии" TEXT,
    "ИД_Родителя" INT
);

CREATE TABLE "Контрагенты" (
    "ИД_Контрагента" SERIAL PRIMARY KEY,
    "Название" VARCHAR(100),
    "Адрес" VARCHAR(255),
    "Телефон" VARCHAR(20),
    "Комментарии" TEXT
);

CREATE TABLE "Контракты" (
    "ИД_Контрагента" INT REFERENCES "Контрагенты"("ИД_Контрагента"),
    "ИД_Менеджера" INT REFERENCES "Менеджеры"("ИД_Менеджера"),
    "Дата_Начала" DATE,
    "Дата_Окончания" DATE,
    PRIMARY KEY ("ИД_Контрагента", "ИД_Менеджера")
);

CREATE TABLE "Банки" (
    "ИД_Банка" SERIAL PRIMARY KEY,
    "Название" VARCHAR(100),
    "Адрес" VARCHAR(255)
);

CREATE TABLE "Группы" (
    "ИД_Группы" SERIAL PRIMARY KEY,
    "Название" VARCHAR(100),
    "Комментарии" TEXT
);

CREATE TABLE "Товары" (
    "ИД_Товара" SERIAL PRIMARY KEY,
    "ИД_Группы" INT REFERENCES "Группы"("ИД_Группы"),
    "Название" VARCHAR(100),
    "Описание" TEXT,
    "Срок_годности" INT
);

CREATE TABLE "Валюты" (
    "ИД_Валюты" SERIAL PRIMARY KEY,
    "Название" VARCHAR(100),
    "Сокращение" VARCHAR(10)
);

CREATE TABLE "Цены" (
    "ИД_Товара" INT REFERENCES "Товары"("ИД_Товара"),
    "Дата_Начала" DATE,
    "Дата_Окончания" DATE,
    "ИД_Валюты" INT REFERENCES "Валюты"("ИД_Валюты"),
    "Значение" DECIMAL(10,2),
    PRIMARY KEY ("ИД_Товара", "Дата_Начала", "ИД_Валюты")
);


CREATE TABLE "Курсы" (
    "ИД_Валюты_От" INT REFERENCES "Валюты"("ИД_Валюты"),
    "ИД_Валюты_К" INT REFERENCES "Валюты"("ИД_Валюты"),
    "Дата_Начала" DATE,
    "Дата_Окончания" DATE,
    "Значение" DECIMAL(10,2),
    PRIMARY KEY ("ИД_Валюты_От", "ИД_Валюты_К", "Дата_Начала")
);

CREATE TABLE "Налоги" (
    "ИД_Налога" SERIAL PRIMARY KEY,
    "Название" VARCHAR(100),
    "Значение" DECIMAL(5,2),
    "Комментарии" TEXT
);

CREATE TABLE "Сделки_Покупки" (
    "ИД_Сделки" SERIAL PRIMARY KEY,
    "ИД_Товара" INT REFERENCES "Товары"("ИД_Товара"),
    "ИД_Контрагента" INT REFERENCES "Контрагенты"("ИД_Контрагента"),
    "ИД_Менеджера" INT REFERENCES "Менеджеры"("ИД_Менеджера"),
    "Дата" DATE,
    "Количество" INT,
    "Стоимость" DECIMAL(10,2),
    "ИД_Налога" INT REFERENCES "Налоги"("ИД_Налога")
);

CREATE TABLE "Сделки_Продажи" (
    "ИД_Сделки" SERIAL PRIMARY KEY,
    "ИД_Товара" INT REFERENCES "Товары"("ИД_Товара"),
    "ИД_Контрагента" INT REFERENCES "Контрагенты"("ИД_Контрагента"),
    "ИД_Менеджера" INT REFERENCES "Менеджеры"("ИД_Менеджера"),
    "Дата" DATE,
    "Количество" INT,
    "Стоимость" DECIMAL(10,2),
    "ИД_Налога" INT REFERENCES "Налоги"("ИД_Налога")
);

INSERT INTO "Дилеры" ("Название", "Процент", "Комментарии") VALUES
('Дилер 1', 5.00, 'Комментарий о дилере 1'),
('Дилер 2', 6.00, 'Комментарий о дилере 2'),
('Дилер 3', 7.00, 'Комментарий о дилере 3'),
('Дилер 4', 8.00, 'Комментарий о дилере 4'),
('Дилер 5', 9.00, 'Комментарий о дилере 5');

INSERT INTO "Дилеры" ("Название", "Процент", "Комментарии") VALUES
('Дилер 6', 5.00, 'Комментарий о дилере 6'),
('Дилер 7', 4.50, 'Комментарий о дилере 7'),
('Дилер 8', 6.00, 'Комментарий о дилере 8');

INSERT INTO "Дилеры" ("Название", "Процент", "Комментарии") VALUES
('Дилер 9', 3.00, 'Комментарий о дилере 9'),
('Дилер 10', 2.50, 'Комментарий о дилере 10'),
('Дилер 11', 4.00, 'Комментарий о дилере 11');

INSERT INTO "Менеджеры" ("ИД_Дилера", "Имя", "Процент", "Дата_найма", "Комментарии", "ИД_Родителя") VALUES
(1, 'Менеджер 1', 10.00, '2024-01-01', 'Комментарий о менеджере 1', NULL),
(1, 'Менеджер 2', 11.00, '2024-02-01', 'Комментарий о менеджере 2', NULL),
(2, 'Менеджер 3', 12.00, '2024-03-01', 'Комментарий о менеджере 3', NULL),
(3, 'Менеджер 4', 13.00, '2024-04-01', 'Комментарий о менеджере 4', NULL),
(4, 'Менеджер 5', 14.00, '2024-05-01', 'Комментарий о менеджере 5', NULL);

INSERT INTO "Менеджеры" ("ИД_Дилера", "Имя", "Процент", "Дата_найма", "Комментарии", "ИД_Родителя") VALUES
(6, 'Менеджер 1', 12.00, '2024-05-01', 'Комментарий о менеджере 6', 1),
(7, 'Менеджер 2', 10.00, '2024-05-02', 'Комментарий о менеджере 7', 3),
(8, 'Менеджер 3', 7.00, '2024-05-02', 'Комментарий о менеджере 8', 4);

INSERT INTO "Менеджеры" ("ИД_Дилера", "Имя", "Процент", "Дата_найма", "Комментарии", "ИД_Родителя") VALUES
(9, 'Менеджер 1', 4.00, '2024-05-03', 'Комментарий о менеджере 9', 3),
(10, 'Менеджер 2', 3.75, '2024-05-03', 'Комментарий о менеджере 10', 3),
(11, 'Менеджер 3', 2.30, '2024-05-03', 'Комментарий о менеджере 11', 4);

INSERT INTO "Контрагенты" ("Название", "Адрес", "Телефон", "Комментарии") VALUES
('Контрагент 1', 'Адрес контрагента 1', '111-111', 'Комментарий о контрагенте 1'),
('Контрагент 2', 'Адрес контрагента 2', '222-222', 'Комментарий о контрагенте 2'),
('Контрагент 3', 'Адрес контрагента 3', '333-333', 'Комментарий о контрагенте 3'),
('Контрагент 4', 'Адрес контрагента 4', '444-444', 'Комментарий о контрагенте 4');

INSERT INTO "Контракты" ("ИД_Контрагента", "ИД_Менеджера", "Дата_Начала", "Дата_Окончания") VALUES
(1, 1, '2024-01-01', '2024-12-31'),
(2, 2, '2024-02-01', '2024-12-31'),
(3, 3, '2024-03-01', '2024-12-31'),
(4, 4, '2024-04-01', '2024-12-31');

INSERT INTO "Банки" ("Название", "Адрес") VALUES
('Банк 1', 'Адрес банка 1'),
('Банк 2', 'Адрес банка 2'),
('Банк 3', 'Адрес банка 3');

INSERT INTO "Группы" ("Название", "Комментарии") VALUES
('Группа 1', 'Комментарий о группе 1'),
('Группа 2', 'Комментарий о группе 2'),
('Группа 3', 'Комментарий о группе 3');

INSERT INTO "Валюты" ("Название", "Сокращение") VALUES
('Рубли', 'RUB'),
('Доллары', 'USD'),
('Евро', 'EUR');

INSERT INTO "Товары" ("ИД_Группы", "Название", "Описание", "Срок_годности") VALUES
(1, 'Товар 1', 'Описание товара 1', 365),
(2, 'Товар 2', 'Описание товара 2', 180),
(3, 'Товар 3', 'Описание товара 3', 90),
(1, 'Товар 4', 'Описание товара 4', 365),
(2, 'Товар 5', 'Описание товара 5', 180),
(3, 'Товар 6', 'Описание товара 6', 90),
(1, 'Товар 7', 'Описание товара 7', 365),
(2, 'Товар 8', 'Описание товара 8', 180),
(3, 'Товар 9', 'Описание товара 9', 90),
(1, 'Товар 10', 'Описание товара 10', 365),
(2, 'Товар 11', 'Описание товара 11', 180),
(3, 'Товар 12', 'Описание товара 12', 90),
(1, 'Товар 13', 'Описание товара 13', 365),
(2, 'Товар 14', 'Описание товара 14', 180),
(3, 'Товар 15', 'Описание товара 15', 90);

INSERT INTO "Цены" ("ИД_Товара", "Дата_Начала", "Дата_Окончания", "ИД_Валюты", "Значение") VALUES
(1, '2024-01-01', '2024-12-31', 1, 100),
(2, '2024-01-01', '2024-12-31', 1, 200),
(3, '2024-01-01', '2024-12-31', 1, 300),
(4, '2024-01-01', '2024-12-31', 1, 400),
(5, '2024-01-01', '2024-12-31', 1, 500),
(6, '2024-01-01', '2024-12-31', 1, 600),
(7, '2024-01-01', '2024-12-31', 1, 700),
(8, '2024-01-01', '2024-12-31', 1, 800),
(9, '2024-01-01', '2024-12-31', 1, 900),
(10, '2024-01-01', '2024-12-31', 1, 1000),
(11, '2024-01-01', '2024-12-31', 1, 1100),
(12, '2024-01-01', '2024-12-31', 1, 1200),
(13, '2024-01-01', '2024-12-31', 1, 1300),
(14, '2024-01-01', '2024-12-31', 1, 1400),
(15, '2024-01-01', '2024-12-31', 1, 1500);

INSERT INTO "Курсы" ("ИД_Валюты_От", "ИД_Валюты_К", "Дата_Начала", "Дата_Окончания", "Значение") VALUES
(1, 2, '2024-01-01', '2024-12-31', 0.013), -- Курс рубля к доллару
(1, 3, '2024-01-01', '2024-12-31', 0.012); -- Курс рубля к евро

ALTER TABLE "Контракты"
ADD CONSTRAINT "FK_Контракты_Менеджеры"
FOREIGN KEY ("ИД_Менеджера")
REFERENCES "Менеджеры" ("ИД_Менеджера");
----------------------------------------------------------------------------------------------
INSERT INTO "Группы" ("Название", "Комментарии") VALUES
('Группа 10', 'Комментарий о группе 10'),
('Группа 13', 'Комментарий о группе 13'),
('Группа 15', 'Комментарий о группе 15');

INSERT INTO "Товары" ("ИД_Группы", "Название", "Описание", "Срок_годности") VALUES
(4, 'Товар 51', 'Описание товара 51', 600),
(5, 'Товар 52', 'Описание товара 52', 250),
(6, 'Товар 53', 'Описание товара 53', 35),
(4, 'Товар 61', 'Описание товара 61', 90),
(5, 'Товар 62', 'Описание товара 62', 120),
(6, 'Товар 63', 'Описание товара 63', 500);

INSERT INTO "Товары" ("ИД_Группы", "Название", "Описание", "Срок_годности") VALUES
(1, 'Товар 55', 'Описание товара 55', 100),
(3, 'Товар 65', 'Описание товара 65', 150),
(4, 'Товар 75', 'Описание товара 75', 350);

INSERT INTO "Цены" ("ИД_Товара", "Дата_Начала", "Дата_Окончания", "ИД_Валюты", "Значение") VALUES
(16, '2024-02-05', '2025-10-05', 1, 10000),
(17, '2024-03-24', '2025-08-24', 2, 200),
(18, '2024-07-03', '2025-12-03', 3, 230),
(19, '2024-08-25', '2025-01-25', 1, 40000),
(20, '2024-09-04', '2024-02-04', 2, 550),
(21, '2024-09-10', '2025-02-10', 3, 1000);

INSERT INTO "Цены" ("ИД_Товара", "Дата_Начала", "Дата_Окончания", "ИД_Валюты", "Значение") VALUES
(22, '2024-09-11', '2025-10-11', 1, 30000),
(23, '2024-09-11', '2025-08-11', 1, 40000),
(24, '2024-09-12', '2025-12-12', 2, 2000);

INSERT INTO "Налоги" ("Название", "Значение", "Комментарии") VALUES
('Налог 1', 13.00, 'Комментарий к налогу 1'),
('Налог 2', 10.55, 'Комментарий к налогу 2'),
('Налог 3', 15.85, 'Комментарий к налогу 3');

INSERT INTO "Сделки_Покупки" ("ИД_Товара", "ИД_Контрагента",
                              "ИД_Менеджера", "Дата", "Количество",
                              "Стоимость", "ИД_Налога") VALUES
(1, 1, 1, '2023-01-03', 5, 1000, 1),
(2, 2, 3, '2023-02-09', 10, 100, 2),
(3, 3, 4, '2023-02-15', 12, 200, 3),
(4, 4, 2, '2023-04-25', 55, 30, 2),
(5, 1, 8, '2023-04-26', 20, 12, 3),
(6, 2, 9, '2023-06-01', 9, 81, 1),
(7, 2, 10, '2023-06-02', 5, 42, 1),
(8, 4, 3, '2023-07-01', 2, 5, 1),
(9, 1, 1, '2023-07-07', 5, 1000, 1),
(10, 2, 3, '2023-09-05', 10, 100, 2),
(11, 3, 4, '2023-10-17', 12, 200, 3),
(12, 4, 2, '2023-11-20', 55, 30, 2),
(13, 1, 8, '2023-01-03', 20, 12, 3),
(14, 2, 7, '2024-02-04', 9, 81, 1),
(15, 2, 10, '2024-03-15', 5, 42, 1),
(16, 4, 6, '2024-04-13', 2, 5, 1),
(17, 1, 1, '2024-05-25', 5, 1000, 1),
(18, 2, 5, '2024-06-24', 10, 100, 2),
(19, 3, 4, '2024-07-12', 12, 200, 3),
(20, 4, 2, '2024-08-03', 55, 30, 2),
(21, 1, 8, '2024-09-05', 20, 12, 3);

INSERT INTO "Сделки_Покупки" ("ИД_Товара", "ИД_Контрагента",
                              "ИД_Менеджера", "Дата", "Количество",
                              "Стоимость", "ИД_Налога") VALUES
(22, 1, 1, '2023-09-05', 11, 100, 1),
(23, 2, 3, '2023-09-05', 4, 300, 1),
(24, 3, 4, '2023-09-06', 3, 400, 2);

UPDATE "Сделки_Покупки"
SET "Дата" = CASE
    WHEN "Дата" = '2023-09-05' THEN '2024-09-05'
    WHEN "Дата" = '2023-09-06' THEN '2024-09-06'
    ELSE "Дата"
END
WHERE "Дата" IN ('2023-09-05', '2023-09-06');

INSERT INTO "Сделки_Продажи" ("ИД_Товара", "ИД_Контрагента",
                              "ИД_Менеджера", "Дата", "Количество",
                              "Стоимость", "ИД_Налога") VALUES
(1, 1, 1, '2024-01-03', 5, 1000, 1),
(2, 2, 3, '2024-02-09', 10, 100, 2),
(3, 3, 4, '2024-02-15', 12, 200, 3),
(4, 4, 2, '2024-04-25', 55, 30, 2),
(5, 1, 8, '2024-04-26', 20, 12, 3),
(6, 2, 9, '2024-06-01', 9, 81, 1),
(7, 2, 10, '2024-06-02', 5, 42, 1),
(8, 4, 3, '2024-07-01', 2, 5, 1),
(9, 1, 1, '2024-07-07', 5, 1000, 1),
(10, 2, 3, '2024-09-05', 10, 100, 2),
(11, 3, 4, '2024-10-17', 12, 200, 3),
(12, 4, 2, '2024-11-20', 55, 30, 2),
(13, 1, 8, '2024-01-03', 20, 12, 3),
(14, 2, 7, '2025-02-04', 9, 81, 1),
(15, 2, 10, '2025-03-15', 5, 42, 1),
(16, 4, 6, '2025-04-13', 2, 5, 1),
(17, 1, 1, '2025-05-25', 5, 1000, 1),
(18, 2, 5, '2025-06-24', 10, 100, 2),
(19, 3, 4, '2025-07-12', 12, 200, 3),
(20, 4, 2, '2025-08-03', 55, 30, 2),
(21, 1, 8, '2025-09-05', 20, 12, 3);


-- Явные курсоры
-- 10.1
-- Явный курсор, возвращающий список всех товаров (id, название товара, название
-- группы товара, стоимость в рублях на дату поступления), поступивших в текущем месяце,
-- чья цена задана в рублях;

DO $$
DECLARE
    rec RECORD;
    cur CURSOR FOR
    SELECT
        "Товары"."ИД_Товара",
        "Товары" ."Название" AS "Название_Товара",
        "Группы"."Название" AS "Название_Группы",
        "Цены"."Значение" AS "Стоимость_В_Рублях"
    FROM
        "Товары"
    JOIN
        "Группы" ON "Товары"."ИД_Группы" = "Группы"."ИД_Группы"
    JOIN
        "Цены"  ON "Товары"."ИД_Товара" = "Цены"."ИД_Товара"
    WHERE
        "Цены"."ИД_Валюты" = 1
        AND "Цены"."Дата_Начала" <= CURRENT_DATE
        AND "Цены"."Дата_Окончания" >= CURRENT_DATE;
BEGIN
    OPEN cur;
    LOOP
        FETCH cur INTO rec;
        EXIT WHEN NOT FOUND;
        RAISE NOTICE 'ID: %, Название товара: %, Название группы: %, Стоимость в рублях: %',
            rec."ИД_Товара", rec."Название_Товара", rec."Название_Группы", rec."Стоимость_В_Рублях";
    END LOOP;
    CLOSE cur;
END $$;

-- 10.2
-- Явный курсор с входными параметрами месяц и валюта, возвращающих список
-- (id, название товаров, название группы товаров, стоимость в рублях
-- на дату поступления) всех товаров, чья стоимость в заданной валюте,
-- поступивших в заданном месяце текущего года

DO $$
    DECLARE
        rec RECORD;
        cur CURSOR (month INT, currency_id INT) FOR
        SELECT
            "Товары"."ИД_Товара",
            "Товары"."Название" AS "Название_Товара",
            "Группы"."Название" AS "Название_Группы",
            "Цены"."Значение" AS "Стоимость_В_Рублях"
        FROM
            "Товары"
        JOIN
            "Группы" ON "Товары"."ИД_Группы" = "Группы"."ИД_Группы"
        JOIN
            "Цены" ON "Цены"."ИД_Товара" = "Товары"."ИД_Товара"
        JOIN
            "Сделки_Покупки" ON  "Сделки_Покупки"."ИД_Товара" = "Товары"."ИД_Товара"
        WHERE
            "Цены"."ИД_Валюты" = currency_id
            AND EXTRACT(MONTH FROM "Сделки_Покупки"."Дата") = month
            AND EXTRACT(YEAR FROM "Сделки_Покупки"."Дата") = EXTRACT(YEAR FROM CURRENT_DATE)
            AND "Цены"."Дата_Начала" <= CURRENT_DATE
            AND "Цены"."Дата_Окончания" >= CURRENT_DATE;
    BEGIN
        -- Пример вызова курсора с параметрами: месяц = 9 (сентябрь), валюта = 1 (рубль)
        OPEN cur(9, 1);

        LOOP
            FETCH cur INTO rec;
            EXIT WHEN NOT FOUND;
            RAISE NOTICE 'ID: %, Название товара: %, Название группы: %, Стоимость в рублях: %',
                    rec."ИД_Товара", rec."Название_Товара", rec."Название_Группы", rec."Стоимость_В_Рублях";
        END LOOP;

        CLOSE cur;
END $$;



-- Неявные курсоры
-- 10.1 Написать неявный курсор, возвращающий объем всех товаров поступивших
-- в текущем месяце, чья цена задана в рублях;

DO $$
DECLARE
    total_quantity INT;
BEGIN
    SELECT SUM("Сделки_Покупки"."Количество") INTO total_quantity
    FROM "Сделки_Покупки"
    JOIN "Цены" ON "Сделки_Покупки"."ИД_Товара" = "Цены"."ИД_Товара"
    WHERE "Цены"."ИД_Валюты" = 1
    AND EXTRACT(MONTH FROM "Сделки_Покупки"."Дата") = EXTRACT(MONTH FROM CURRENT_DATE)
    AND EXTRACT(YEAR FROM "Сделки_Покупки"."Дата") = EXTRACT(YEAR FROM CURRENT_DATE);

    IF total_quantity IS NOT NULL THEN
        RAISE NOTICE 'Объем товаров, поступивших в текущем месяце: %', total_quantity;
    ELSE
        RAISE NOTICE 'Нет данных о товарах, поступивших в текущем месяце.';
    END IF;
END $$;


-- 10.2 Написать неявный курсор для указанных месяца и валюты, повышающий
-- стоимость на 5% для товаров, поступивших в указанной валюте и в заданном
-- месяце текущего года. Сообщить, если вообще не было поступлений в
-- указанный период или не было поступлений в указанной валюте.

DO $$
DECLARE
    v_month INT := 9;
    v_currency_id INT := 1;
    affected_rows INT := 0;
BEGIN
    UPDATE "Цены"
    SET "Значение" = "Значение" * 1.05
    WHERE "ИД_Товара" IN (
        SELECT "Сделки_Покупки"."ИД_Товара"
        FROM "Сделки_Покупки"
        JOIN "Цены" ON "Сделки_Покупки"."ИД_Товара" = "Цены"."ИД_Товара"
        WHERE "Цены"."ИД_Валюты" = v_currency_id  -- Валюта
        AND EXTRACT(MONTH FROM "Сделки_Покупки"."Дата") = v_month  -- Заданный месяц
        AND EXTRACT(YEAR FROM "Сделки_Покупки"."Дата") = EXTRACT(YEAR FROM CURRENT_DATE)  -- Текущий год
    );
    GET DIAGNOSTICS affected_rows = ROW_COUNT;
    IF affected_rows > 0 THEN
        RAISE NOTICE 'Стоимость товаров в валюте % за месяц % повышена на 5%% для % товаров.', v_currency_id, v_month, affected_rows;
    ELSE
        IF (SELECT COUNT(*) FROM "Сделки_Покупки" WHERE EXTRACT(MONTH FROM "Сделки_Покупки"."Дата") = v_month) = 0 THEN
            RAISE NOTICE 'Не было поступлений товаров в указанный месяц.';
        ELSE
            RAISE NOTICE 'Не было поступлений товаров в указанной валюте.';
        END IF;
    END IF;
END $$;

--сделать обработку событий для параментризованных и непаратмериованных курсоров + рефераты в ЛМС

--Неявные курсоры
--13.1
DO $$
DECLARE
    m INTEGER;
BEGIN
    SELECT COUNT(ca."ИД_Контрагента") INTO m
    FROM "Контрагенты" ca
    JOIN "Контракты" c ON c."ИД_Контрагента" = ca."ИД_Контрагента"
    WHERE c."Дата_Начала" >= current_date - INTERVAL '6 months';

    RAISE NOTICE 'кол-во контрагентов = %', m;
END $$;


--13.2
DO $$
DECLARE
    c_id INTEGER;
    start_date DATE;
    end_date DATE;
    contract_duration INTERVAL;
    min_duration INTERVAL := INTERVAL '6 months';
    target_year INTEGER := 2023;
BEGIN
    FOR c_id, start_date, end_date IN
        SELECT c."ИД_Контрагента", c."Дата_Начала", c."Дата_Окончания"
        FROM "Контракты" c
        WHERE EXTRACT(YEAR FROM c."Дата_Начала") = target_year
    LOOP
        contract_duration := end_date - start_date;

        if contract_duration >= min_duration then
            update "Контрагенты"
            set "Комментарии" = 'Контракт продолжительности ' || contract_duration
            where "ИД_Контрагента" = c_id;
        end if;
    end loop;
END $$;




select "Сделки_Покупки"."ИД_Сделки", "Сделки_Покупки"."Дата", "Сделки_Покупки"."Стоимость",
       "Менеджеры"."ИД_Менеджера", "Менеджеры"."Имя"
from "Сделки_Покупки"
join "Менеджеры" on "Сделки_Покупки"."ИД_Менеджера" = "Менеджеры"."ИД_Менеджера"
where "Сделки_Покупки"."Дата" >= CURRENT_DATE - INTERVAL '12 months';











-- 10. Написать код с обработкой исключений, скомпилировать и проверить на тестовых
-- данных следующие функции и процедуры PL/SQL:
-- 1) функция, возвращающая размер налога по его идентификатору и обратную ей функцию;

CREATE OR REPLACE FUNCTION get_tax_value(p_tax_id INT)
RETURNS DECIMAL(5, 2) AS $$
DECLARE
    tax_value DECIMAL(5, 2);
BEGIN
    -- Получение значения налога по идентификатору
    SELECT "Значение"
    INTO tax_value
    FROM "Налоги"
    WHERE "ИД_Налога" = p_tax_id;

    -- Если налог не найден, выбрасываем исключение
    IF NOT FOUND THEN
        RAISE EXCEPTION 'Ошибка при получении значения налога: Налог с указанным идентификатором % не найден', p_tax_id;
    END IF;

    RETURN tax_value;
END;
$$ LANGUAGE plpgsql;

--DROP FUNCTION get_tax_value(integer);

CREATE OR REPLACE FUNCTION get_tax_id_by_value(p_tax_value DECIMAL(5, 2))
RETURNS INT AS $$
DECLARE
    tax_id INT;
BEGIN
    -- Получение идентификатора налога по значению
    SELECT "ИД_Налога"
    INTO tax_id
    FROM "Налоги"
    WHERE "Значение" = p_tax_value;

    -- Если налог не найден, выбрасываем исключение
    IF NOT FOUND THEN
        RAISE EXCEPTION 'Ошибка при получении идентификатора налога: Налог со значением % не найден', p_tax_value;
    END IF;

    RETURN tax_id;
END;
$$ LANGUAGE plpgsql;

--DROP FUNCTION get_tax_id_by_value(numeric);

-- Получение размера налога с идентификатором 1
SELECT get_tax_value(3) AS "Размер налога";

-- Получение идентификатора налога с размером 10.55
SELECT get_tax_id_by_value(13.00) AS "ИД налога";

--2) функция, возвращающая сумму, необходимую к уплате по конкретному
-- налогу для указанного периода и продукта;

CREATE OR REPLACE FUNCTION calculate_tax_payment(
    p_tax_id INT,
    p_product_price DECIMAL(10, 2),
    p_period VARCHAR(50)
)
RETURNS DECIMAL(10, 2) AS $$
DECLARE
    tax_value DECIMAL(5, 2);
    payment_amount DECIMAL(10, 2);
    period_coefficient DECIMAL(5, 2);
BEGIN
    -- Получаем размер налога по его идентификатору
    SELECT "Значение" INTO tax_value
    FROM "Налоги"
    WHERE "ИД_Налога" = p_tax_id;

    -- Если налог не найден, выбрасываем исключение
    IF NOT FOUND THEN
        RAISE EXCEPTION 'Ошибка при расчете: Налог с идентификатором % не найден', p_tax_id;
    END IF;

    -- Устанавливаем коэффициент для периода (пример: в зависимости от периода меняется ставка)

    IF p_period = 'месяц' THEN
        period_coefficient := 1.00;
    ELSIF p_period = 'квартал' THEN
        period_coefficient := 0.95; -- скидка на налог для квартала
    ELSIF p_period = 'год' THEN
        period_coefficient := 0.90; -- скидка на налог для года
    ELSE
        RAISE EXCEPTION 'Ошибка при расчете: Неизвестный период %', p_period;
    END IF;

    -- Расчет суммы, необходимой к уплате с учетом налога и периода
    payment_amount := p_product_price * (tax_value / 100) * period_coefficient;

    RETURN payment_amount;
END;
$$ LANGUAGE plpgsql;


SELECT calculate_tax_payment(1, 1000.00, 'месяц') AS "Сумма к уплате";

--3) Используя пользовательские функции напишите процедуру распечатывающую список необходимых к уплате налогов на реализацию на последний квартал по каждому продукту

CREATE OR REPLACE PROCEDURE print_tax_payments_last_quarter()
LANGUAGE plpgsql AS $$
DECLARE
    product_record RECORD;
    tax_value NUMERIC;
    total_payment NUMERIC;
BEGIN
    -- Проход по всем продуктам
    FOR product_record IN
        SELECT DISTINCT s."ИД_Товара", t."Название"
        FROM "Сделки_Продажи" s
        JOIN "Товары" t ON s."ИД_Товара" = t."ИД_Товара"
        WHERE s."Дата" >= date_trunc('quarter', CURRENT_DATE) - interval '1 day' * extract(day from date_trunc('quarter', CURRENT_DATE)) + interval '3 month'
          AND s."Дата" < date_trunc('quarter', CURRENT_DATE)
    LOOP
        -- Получаем сумму к уплате по каждому налогу за последний квартал
        total_payment := 0;

        -- Получаем значение налога для данного продукта
        SELECT SUM(s."Стоимость" * (n."Значение" / 100))
        INTO total_payment
        FROM "Сделки_Продажи" s
        JOIN "Налоги" n ON s."ИД_Налога" = n."ИД_Налога"
        WHERE s."ИД_Товара" = product_record."ИД_Товара"
          AND s."Дата" >= date_trunc('quarter', CURRENT_DATE) - interval '1 day' * extract(day from date_trunc('quarter', CURRENT_DATE)) + interval '3 month'
          AND s."Дата" < date_trunc('quarter', CURRENT_DATE);

        -- Выводим информацию о продукте и сумме налога
        RAISE NOTICE 'Продукт: %, Сумма к уплате налога: %.2f', product_record."Название", total_payment;
    END LOOP;
END;
$$;

-- Вызов процедуры
CALL print_tax_payments_last_quarter();

--Триггеры

-- Создание представления для отображения данных о продажах
CREATE OR REPLACE VIEW sales_view AS
SELECT
    sp."ИД_Сделки" AS sale_id,
    sp."ИД_Товара" AS product_id,
    sp."ИД_Контрагента" AS client_id,
    t."Название" AS product_name,
    sp."Стоимость" AS price_in_rubles,
    sp."Количество" AS quantity,
    m."Имя" AS manager_name, -- Предполагаем, что у вас есть поле "Имя" в таблице "Менеджеры"
    sp."Дата" AS sale_date,
    n."Значение" AS tax_value -- Значение налога для товара
FROM
    "Сделки_Продажи" sp
JOIN
    "Товары" t ON sp."ИД_Товара" = t."ИД_Товара"
JOIN
    "Налоги" n ON sp."ИД_Налога" = n."ИД_Налога"
JOIN
    "Менеджеры" m ON sp."ИД_Менеджера" = m."ИД_Менеджера"
JOIN
    "Контрагенты" c ON sp."ИД_Контрагента" = c."ИД_Контрагента"; -- Предполагаем, что у вас есть поле "Имя" в таблице "Контрагенты"

-- Функция для проверки наличия товара
CREATE OR REPLACE FUNCTION check_product_availability()
RETURNS TRIGGER AS $$
BEGIN
    -- Проверяем наличие товара
    IF (SELECT "Количество" FROM "Товары" WHERE "ИД_Товара" = NEW."ИД_Товара") < NEW."Количество" THEN
        RAISE EXCEPTION 'Недостаточно товара в наличии для продажи.';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Удаляем триггер, если он существует
DROP TRIGGER IF EXISTS product_availability_trigger ON "Сделки_Продажи";

-- Создаем триггер для проверки наличия товара перед вставкой или обновлением
CREATE TRIGGER product_availability_trigger
BEFORE INSERT OR UPDATE ON "Сделки_Продажи"
FOR EACH ROW EXECUTE FUNCTION check_product_availability();

SELECT tgname FROM pg_trigger WHERE tgrelid = 'Сделки_Продажи'::regclass;

-- Функция для проверки возраста продаж перед удалением
CREATE OR REPLACE FUNCTION check_sales_age()
RETURNS TRIGGER AS $$
BEGIN
    -- Проверяем, если запись была продана более двух лет назад
    IF OLD."Дата" >= CURRENT_DATE - INTERVAL '2 years' THEN
        RAISE EXCEPTION 'Запись о продаже не может быть удалена, так как она моложе двух лет.';
    END IF;
    RETURN OLD;
END;
$$ LANGUAGE plpgsql;

-- Удаляем триггер, если он существует
DROP TRIGGER IF EXISTS sales_age_trigger ON "Сделки_Продажи";

-- Создаем триггер для проверки возраста записей о продажах перед удалением
CREATE TRIGGER sales_age_trigger
BEFORE DELETE ON "Сделки_Продажи"
FOR EACH ROW EXECUTE FUNCTION check_sales_age();


INSERT INTO "Сделки_Продажи" ("ИД_Товара", "ИД_Контрагента", "ИД_Менеджера", "Дата", "Количество", "Стоимость", "ИД_Налога")
VALUES (1, 1, 1, CURRENT_DATE, 10, 1000, 1);

DELETE FROM "Сделки_Продажи" WHERE "ИД_Сделки" = 1;

-- Добавим запись, которая будет старше 2 лет
INSERT INTO "Сделки_Продажи" ("ИД_Товара", "ИД_Контрагента", "ИД_Менеджера", "Дата", "Количество", "Стоимость", "ИД_Налога")
VALUES (1, 1, 1, '2022-01-01', 5, 1000, 1);

-- Теперь попробуем удалить её
DELETE FROM "Сделки_Продажи" WHERE "ИД_Сделки" = (SELECT MAX("ИД_Сделки") FROM "Сделки_Продажи");



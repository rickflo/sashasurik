# ФИО и группа
**Суриков Александр Дмитриевич** \
**ИС 22/9-1** 

## 2 Описание БД

![ТАБЛИЦЫ](123123123.png)
**Эта база данных представляет собой систему управления авиалинияеми, обеспечивая  управление рейсами, перелетам и покупку билетов  . В нее входят такие таблицы как:** 
- aircraft
- departures
- destination_airport
- flights
- passengers


### 2.1 aircraft 
 ![APPOINTMENTS](Appoint.png)
 ![APPOINTMENTS1](Appoint1.png)

> id_aircraft - integer - код самолета\
type_aircraft - varchar(30) - тип самолета\
number_seats - integer - количество мест\
flight_range - integer - дальность полёта

## 2.2 departures 
![CLIENTS](Clients.png)
![CLIENTS1](Clients1.png)

> id_departure - integer - код вылета\
flight - integer - рейс\
departure_time - integer - время вылета\
plane - VARCHAR(30) - самолет\
commander - VARCHAR(30) - командир экипажа\


## 2.3 destination_airports 
![EMPLOYEES](Employees.png)
![EMPLOYEES1](Employees1.png)

> id_airport - integer - код аэропорта\
title - VARCHAR(30) - название\
## 2.4 flights 
![PAYMENTS](Payments.png)
![PAYMENTS1](Payments1.png)

> id_flight - integer - код рейса\
flight_number - integer - номер рейса\
departure_airport - DECIMAL(10,2) - К оплате\
destination_airport - varchar(30) - аэропорт назначения\
flight_duration - varchar(30) - продолжительность полета\
ticket_price - integer - стоимость билета\

## 2.5 passengers 
![REVIEWS](Reviews.png)
![REVIEWS1](Reviews1.png)

> id_passenger - integer - код пассажира\
departure - integer - вылет\
place_number - integer - номер места\
full_name - varchar(30) - ФИО\
passport - integer - паспорт

## 3 UNION 

![UNION](8.png)

```sql
SELECT departure_airport 
FROM flights
UNION
SELECT full_name 
FROM passengers
```
**Этот запрос вернет список аэропортов, с которых вылетают пассажиры и имена пассажиров**

## 4 ORDER BY  
![ORDERBY](orderby.png)

```sql
SELECT full_name
from passengers
order by full_name
```
**Этот запрос выводит сортировку по именам пассажиров. (сразу по алфавиту)**

## 5 HAVING 
![HAVING](havingcount.png)

```sql
SELECT departure_airport, destination_airport, flight_duration
FROM flights
GROUP BY departure_airport
HAVING flight_duration LIKE '1:30'
```

**Этот запрос сначала группирует записи по работникам и считает количество услуг этих работников. После этого фильтрует тех работников, которые предоставили больше 0 услуг**

## 6 ВЛОЖЕННЫЕ ЗАПРОСЫ

### 6.1 В SELECT
![SELECT](select.png)

```sql
SELECT e.firstname, e.lastname,
    (SELECT MAX(price) FROM services WHERE employeeid = e.employeeid) AS max_price
FROM employees e;
```

**Этот запрос использует SELECT для получения максимальной цены услуги сотрудника. Это позволяет подставлять максимальную цену услуги для каждого работника в результате выборки**



### 6.2 В WHERE
![WHERE](Where.png)

```sql
SELECT e.firstname, e.lastname
FROM employees e
WHERE e.employeeid IN (
    SELECT employeeid FROM services WHERE price > 1500
);
```
**Этот запрос использует SELECT в WHERE для фильтра работников, которые предоставили хотя бы 1 услугу дороже 1500. Происходит это благодаря выборке айди сотрудников из таблицы услуг, где цена выше 1500**

## 7 ОКОННЫЕ ФУНКЦИИ

### 7.1 АГРЕГАТНЫЕ ФУНКЦИИ

![Uploading image.png…]()


```sql

SELECT Min(ticket_price) AS МинимальнаяЦена, Max(ticket_price) AS МаксимальнаяЦена, AVG(ticket_price) AS СредняяЦена
FROM flights

```
**Вывод минимальной, максимальной, средней цены за билет**

### 7.2 РАНЖИРУЮЩИЕ ФУНКЦИИ
![RANK](rank.png)

```sql
SELECT flight_id, flight_number, RANK() OVER (ORDER BY flight_number) aS flight_rank
FROM flights

```

**Запрос использует ранжирующую функцию для присвоения ранга каждому платежу на основе номера рейса . Ранги упорядочены по убыванию номера рейса. Результат покажет ранг каждого номера рейса вместе с номером рейса**


### 7.3 ФУНКЦИИ СМЕЩЕНИЯ
![LEAD](Lead.png)

```sql
SELECT flight_id, ticket_price, LEAD(ticket_price) OVER (ORDER BY flight_id) AS next_flight
FROM flights;
```
**Используется для получения значения из следующей строки в порядке сортировки. Этот запрос выводит айди платежа, стоимость билета и следующую стоимость билета**

## 8 JOIN

### 8.1 INNER JOIN

![INNER](2.png) 

```sql

SELECT Clients.Firstname, Reviews.ReviewsText
FROM Clients
JOIN Reviews USING (clientid)

```
**Этот запрос выводит имена клиентов и отзыв, который они написали**

### 8.2 LEFT JOIN

![LEFT](3.png)

```sql

SELECT Appointments.EmployeeID
FROM Appointments
LEFT JOIN Employees USING (employeeid)

```
**Этот запрос выводит айди работников**

### 8.3 RIGHT JOIN 

![RIGHT](4.png)

```sql

SELECT Reviews.ClientID, gender
FROM Reviews
RIGHT JOIN Clients USING (clientid)

```
**Этот запрос выводит айди клиентов и их гендер**

### 8.4 FULL OUTER JOIN

![FULLOUTER](5.png)

```sql

SELECT Services.Price, employeeid
FROM Services
FULL OUTER JOIN Employees USING (employeeid)

```

**Этот запрос выводит айди работника и его цену за услугу стрижки**

### 8.5 CROSS JOIN

![CROSS](6.png)

**Этот запрос выводит совпадение из таблиц клиенты и работники, то есть совпадающее имя клиента и работника - Василий**

## 9 CASE

![CASE](7.png)

**Этот запрос выполняет следующие действия:\
Если имя клиента начинается на 'В', то вывод 1. Если имя клиента начинается на А, то вывод 2, иначе выводит 0**

## 10 WITH

![WITH](WITH.png)

```sql
WITH HighPayments AS (
    SELECT paymentid, appointmentid, paymentamount
    FROM payments
    WHERE paymentamount > 1200
)
SELECT a.datetime, a.clientid, p.paymentamount
FROM appointments a
JOIN HighPayments p ON a.appointmentid = p.appointmentid;
```

**Этот запрос создает временную таблицу, которая содержит информацию о платежах больше 1200. После этого соединяет эту таблицу с таблицей записей Appointments, чтобы отобразить дату, время приема, айди клиента и сумму платежа**



```
SELECT HYESOS MAX 
```

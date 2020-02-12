CUCM calls analyzer
=====================

**CUCM** (Cisco Unified Communications Manager) - телефонная станция, которая является системой для обработки вывозов на базе программного обеспечения. CUCM работает с такими элементами сетей передачи голоса поверх протокола IP (VoIP) как: шлюзы, телефонные аппараты, мосты для конференцсвязи, голосовая почта, видеоконференцсвязью и многими другими.

### Интерфейс

![Интерфейс Web-морды](https://user-images.githubusercontent.com/31792522/74311631-c0e43780-4d91-11ea-8f40-9e73f709d0ed.jpg)

### Как использовать?

**Важно**: система написана в процедурном стиле, в дальнейшем будет переведена на ООП

### Оптимизация базы данных

Чтобы ускорить выборку нужных данных, прибегаем к созданию индексов для определённых полей в таблице.

* dateTimeOrigination (дата и время звонка, в формате EPOCH UTC)
* dateTimeDisconnect (время завершения сеанса связи, разговора)
* dateTimeConnect (время установления сеанса связи, разговора)

Выполняем SQL запросы для создания индексов

```sql
CREATE INDEX index_dateTimeOrigination ON нужная_таблица(dateTimeOrigination);
CREATE INDEX index_dateTimeDisconnect ON нужная_таблица(dateTimeDisconnect);
CREATE INDEX index_dateTimeConnect ON нужная_таблица(dateTimeConnect);
```

**Результат работы индексов (реальный пример)**

Для сравнения результата выполним простой SQL запрос к базе, до внедрния индексов и после

```sql
SELECT `callingPartyNumber`,`originalCalledPartyNumber`,`finalCalledPartyNumber`,`dateTimeOrigination`,`dateTimeConnect`,`dateTimeDisconnect`,`duration` 
FROM `cdr` 
WHERE `dateTimeOrigination` >= 1577836800 AND `dateTimeDisconnect` <= 1580342400;
```

Выполняем SQL запрос (без индексов)

![Без индексов](https://user-images.githubusercontent.com/31792522/74313064-c8591000-4d94-11ea-899e-96c722da15c1.jpg)

Выполняем ещё раз SQL запрос (после создания индексов)

Кэш перед повторным запросом был очищен

![После индексов](https://user-images.githubusercontent.com/31792522/74313077-cd1dc400-4d94-11ea-9e74-dc0687fa0d02.jpg)


### Обновления

**Версия 0.1**

Раздел: Текущий месяц, Предыдущий месяц

Теперь внутренняя функция SQL - UNIX_TIMESTAMP(), выполняет преобразование даты из формата ГГГГ-ММ-ДД ЧЧ:ММ в UNIX (EPOCH)
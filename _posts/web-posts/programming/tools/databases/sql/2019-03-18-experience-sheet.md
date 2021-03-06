---
layout: post
title: SQL experience sheet
date:   2019-04-02 12:48:00 +0100
categories: [tools, sql]
tags: [programming, tools, sql, experience-sheet]
---
### DDL Queries
* Show all dbs - `SHOW DATABASES;`
* Select one - `USE {$db};`
* Show tables in a db - `SHOW TABLES;`
* Show description of DB - `DESCRIBE {$db}`

##### Create new table
~~~ sql
CREATE TABLE table_name (
	id numeric,
	description text);
~~~
<!--more-->
### DML Queries
#### Update
~~~ sql
UPDATE table_redacted
	SET state = "IMPORTED"
	WHERE state = "EXPORTED_FAILED";
~~~

#### Selects
##### join
~~~ sql
SELECT *
	FROM data_transaction t JOIN
		transaction_item i
	ON(t.data_transaction_number
		= i.data_transaction_id)
	WHERE t.data_transaction_number = 123456;
~~~
##### and / or preference order
The category HAS to be `SG` OR `SH` + all the previous conditions.
~~~ sql
SELECT COUNT(*), type, operator, category, state
	FROM prefix.table_redacted
	WHERE type = "S"
	AND operator != "ASD"
	AND (category = "SG"
	OR category = "SH")
	LIMIT 200;
~~~

##### having
~~~ sql
SELECT title, start_date, COUNT(*)
	FROM appointment
	GROUP BY title, start_date
	HAVING COUNT(*) > 1
	ORDER BY COUNT(*) DESC;
~~~

##### date like
~~~ sql
SELECT COUNT(*)
	FROM invoice_position
	WHERE reference_date LIKE "%2019-01%";
~~~

##### between
~~~ sql
SELECT id, state
	FROM invoice_position
	WHERE id BETWEEN 140 AND 144;
~~~

#### Subqueries
Find all Items from all Invoices, which belong to a transaction with a card_uid `0410ed522d5e81`  

~~~ sql
SELECT *
FROM transaction_item
WHERE data_transaction_id IN (
	SELECT data_transaction_number
		FROM data_transaction
		WHERE card_uid = "0410ed522d5e81"
);
~~~

Delete all invoices except the ones with the card uids I want
~~~ sql
DELETE
FROM data_transaction
WHERE data_transaction_number NOT IN(
	SELECT data_transaction_number
	FROM
	(SELECT data_transaction_number
		FROM data_transaction
		WHERE card_uid = "0410ed522d5e81"
		OR card_uid = "0464cd3a2b5e80"
	) invoice
);
~~~
### Reference
[https://www.w3schools.in/mysql/ddl-dml-dcl/](https://www.w3schools.in/mysql/ddl-dml-dcl/)

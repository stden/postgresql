PostgreSQL
==========

Мои примеры PostgreSql

 Инструменты
-------------
 * OpenServer - http://open-server.ru/download.html
 * pgAdmin (администрирование) - http://www.pgadmin.org/download/


 Создание базы данных
----------------------
```
CREATE DATABASE test
  WITH OWNER = postgres
       ENCODING = 'UTF8'
       TABLESPACE = pg_default
       LC_COLLATE = 'Russian_Russia.1251'
       LC_CTYPE = 'Russian_Russia.1251'
       CONNECTION LIMIT = -1;
```

 Создание таблицы и индекса
----------------------------
```
-- DROP TABLE mind;

CREATE TABLE mind
(
  pk bigserial NOT NULL, -- ID логического высказывания
  expr text, -- s-выражение lisp в виде строки
  CONSTRAINT pk PRIMARY KEY (pk )
)
WITH (
  OIDS=FALSE
);
ALTER TABLE mind
  OWNER TO postgres;
COMMENT ON COLUMN mind.pk IS 'ID логического высказывания';
COMMENT ON COLUMN mind.expr IS 's-выражение lisp в виде строки';


-- Index: mind_idx

-- DROP INDEX mind_idx;

CREATE INDEX mind_idx
  ON mind
  USING gin
  (to_tsvector('english'::regconfig, expr) );
```
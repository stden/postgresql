PostgreSQL
==========

Мои примеры PostgreSql

Инструменты
-----------
 * OpenServer - http://open-server.ru/download.html
 * pgAdmin (администрирование) - http://www.pgadmin.org/download/




Создание базы данных
--------------------
```sql
CREATE DATABASE test
  WITH OWNER = postgres
       ENCODING = 'UTF8'
       TABLESPACE = pg_default
       LC_COLLATE = 'Russian_Russia.1251'
       LC_CTYPE = 'Russian_Russia.1251'
       CONNECTION LIMIT = -1;
```

Создание таблицы и индекса
--------------------------
```sql
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


-- Индекс для быстрого полнотекстового поиска
-- Index: mind_idx

-- DROP INDEX mind_idx;

CREATE INDEX mind_idx
  ON mind
  USING gin
  (to_tsvector('english'::regconfig, expr) );
```

Ищем слово friend в поле expr таблицы mind
------------------------------------------
```sql
-- Запрос
SELECT expr
FROM mind
WHERE to_tsvector('english', expr) @@ to_tsquery('english', 'friend');
```

Генерация случайных чисел в диапазоне
-------------------------------------
```sql
CREATE OR REPLACE FUNCTION random_range(INTEGER, INTEGER)
RETURNS INTEGER
LANGUAGE SQL
AS $$
    SELECT ($1 + FLOOR(($2 - $1 + 1) * random() ))::INTEGER;
$$;
```

Генерация случайного текста
---------------------------
```sql
CREATE OR REPLACE FUNCTION random_text_simple(length INTEGER)
RETURNS TEXT
LANGUAGE PLPGSQL
AS $$
DECLARE
    possible_chars TEXT := '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ';
    output TEXT := '';
    i INT4;
    pos INT4;
BEGIN

    FOR i IN 1..length LOOP
        pos := random_range(1, length(possible_chars));
        output := output || substr(possible_chars, pos, 1);
    END LOOP;

    RETURN output;
END;
$$;
```

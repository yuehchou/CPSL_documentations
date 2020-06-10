# Connect Postgres on AWS RedShift

## Steps for Changing Uppercae to Lowercase in Postgres

Becasue of avoiding uppercase name in the schema, table and column, we need to do the following steps.

1. Copy definition statement from tables in Postgres, it will be

```SQL
CREATE TABLE "WOSINT"."TABLES_A"(
    "AAA" ...,
    "BBB" ...,
    ...
    PRIMARY KEY (...)
);
```

2. Change uppercase to lowercase in Postgres, SQL code should be

```SQL
CREATE VIEW wosint_report_view.vw_tables_a as (
SELECT
    "AAA" as "aaa",
    "BBB" as "bbb",
    ...
    FROM "WOSINT"."TABLES_A"
);
```

## Steps for Creating New View to Redshift

1. You need to login AWS Glue and find the table "_tables_a_" in "_AWS Glue/Data catalog/Databases/Tables_".

2. For all date columns in "_tables_a_", you need to change their type from "_timestamp_" to "_bigint_" and save.

3. Back to Redshift, run SQL code as following.

```SQL
CREATE VIEW vw_full_tables_a as
    SELECT aaa, bbb, ...
    FROM pgfedrate.vw_tables_a
    UNION ALL SELECT aaa, bbb,
                     ...,
                     (timestamp 'epoch' + (xxx_date/1000000) * interval '1 second') AS xxx_date,
                     ... FROM cpsl_wos_wosarch.tables_a
    with no schema binding;
```

4. Finally, you can check the schema "_public_" whether the table "_vw_full_tables_a_" exists.
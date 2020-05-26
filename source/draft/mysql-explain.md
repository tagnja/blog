Title: MySQL explain

## Meaning Key of Columns in explain result

id

select_type

- SIMPLE

table: table names

partitions

type

- ref
- index: MySQL plans to scan an index.

possible_keys: your index names

key: your index names

key_len

ref

- const

rows

filtered

Extra

- Using index: it means use covering index, or fetch data from index rather than physical rows.
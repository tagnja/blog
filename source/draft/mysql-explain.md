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

- Using index (only show): it means storage engine use covering index, or fetch all data from index file rather than physical rows.
- Using index condition:
- Using where: it means the MySQL server is applying a WHERE filter after the storage engine returns the rows.
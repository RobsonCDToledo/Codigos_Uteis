Comando SQL que gera um mapa com todos os Datasets suas tabelas e os schemas de todas as tabelas (Colunas e tipos) de dentro do projeto atual.


````sql
SELECT
  cfp.table_schema AS dataset,
  cfp.table_name,
  t.table_type,
  t.creation_time,
  cfp.column_name,
  cfp.data_type,
  cfp.description AS column_description
FROM `seu-projeto.region-southamerica-east1.INFORMATION_SCHEMA.COLUMN_FIELD_PATHS` cfp
LEFT JOIN `seu-projeto.region-southamerica-east1.INFORMATION_SCHEMA.TABLES` t
  ON  cfp.table_schema = t.table_schema
  AND cfp.table_name = t.table_name
WHERE cfp.field_path = cfp.column_name      
ORDER BY
  cfp.table_schema,
  cfp.table_name
````

Comando SQL que gera um mapa com todas as tabelas e seus schemas (Colunas e tipos) de um dataset especifico.

````sql
SELECT
  table_name,
  column_name,
  data_type,
  description
FROM `seu-projeto.dataset.INFORMATION_SCHEMA.COLUMN_FIELD_PATHS`
WHERE field_path = column_name
ORDER BY table_name
````

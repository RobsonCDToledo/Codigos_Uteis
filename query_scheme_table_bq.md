Código SQL para retornar listar e retornar o schema de campos das tabelas que existem dentro do seu projeto no BQ



´´´´SQL
SELECT
  table_name,
  column_name,
  data_type,
  description
FROM `seu-projeto.cimed_raw.INFORMATION_SCHEMA.COLUMN_FIELD_PATHS`
WHERE field_path = column_name
ORDER BY table_name
´´´´

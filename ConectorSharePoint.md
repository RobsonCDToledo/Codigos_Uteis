# 🧭 Objetivo

Demonstrar como criar uma conexão direta e performática com pastas do SharePoint, utilizando o conector SharePoint.Contents() no Power Query.
Este método evita a necessidade de filtros demorados, reduz o tempo de carregamento e elimina a criação automática de parâmetros no Power BI.

# ⚙️ Parâmetro do Site

Primeiro, crie um parâmetro no seu modelo de dados:

Nome do parâmetro: pLinkSiteSharepoint

### Valor:
```PowerQuery
https://grupocimed.sharepoint.com/sites/SeuSite
``` 

### 💡 Dica:
A depender da arquitetura do OneDrive/SharePoint da sua empresa, a primeira pasta pode aparecer como:

Shared Documents, ou

Documentos Compartilhados

Recomendo executar o conector apenas até o primeiro nível (SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion=15])) para identificar o nome correto da biblioteca antes de seguir adiante.

### 🚀 Conector Performático – Navegação Direta
```PowerQuery
let
    Fonte =
        SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion = 15]) /* Conector base do site */
            {[Name="Shared Documents"]}[Content]                    /* Biblioteca principal */
            {[Name="Modelos - Power BI"]}[Content]                  /* Subpasta 1 */
            {[Name="_PBI - Direct BigQuery"]}[Content]              /* Subpasta 2 */
            /* Acrescente mais níveis se necessário */
in
    Fonte
```
### 🔍 Resultado

Esse método retorna diretamente o conteúdo da pasta final, sem precisar filtrar ou expandir manualmente todas as camadas anteriores.

### ✅ Vantagens:

Navegação hierárquica direta e limpa;

Evita leitura desnecessária de milhares de arquivos;

Totalmente compatível com SharePoint Online (API v15);

Melhora o desempenho das consultas no Power BI e Power Query.

### 📘 Expandindo Arquivo .xlsx Manualmente (Sem Parâmetros Automáticos)

Agora que você já está dentro da pasta desejada, pode acessar diretamente o conteúdo do arquivo Excel sem deixar o Power BI criar parâmetros automáticos.

Exemplo:

```PowerQuery
let
    Fonte =
        SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion = 15])
            {[Name="Shared Documents"]}[Content]
            {[Name="Sub_Pasta1"]}[Content]
            {[Name="Sub_Pasta2"]}[Content]
            {[Name="Sub_Pasta3"]}[Content],

    /*Leitura do arquivo Excel sem parâmetros automáticos*/
    Planilhas = Table.AddColumn(Fonte, "ExcelTables", each Excel.Workbook([Content], true)),

    /*Seleciona a planilha desejada*/
    Dados = Planilhas{[Item="SuaPlanilha", Kind="AbaDaPlanilha"]}[Data]
in
    Dados
```

### 🧠 Boas Práticas Recomendadas

Evite SharePoint.Files() – ele percorre o site inteiro antes de achar o arquivo.

Prefira SharePoint.Contents() – acessa diretamente o caminho exato.

Padronize nomes de pastas e arquivos (sem acentos ou espaços duplos).

Utilize parâmetros centralizados (pLinkSiteSharepoint, pArquivoAlvo, etc.).

Documente a hierarquia de acesso dentro do seu modelo ou README.md.

### 🧩 Exemplo Comparativo

| Método | Função usada | Desempenho | Recomendado |
|:--------|:-------------|:------------|:-------------|
| ❌ Tradicional | `SharePoint.Files()` | Lento, carrega todos os arquivos do site | ❌ Não |
| ✅ Otimizado | `SharePoint.Contents()` | Rápido, acessa apenas o necessário | ✅ Sim |

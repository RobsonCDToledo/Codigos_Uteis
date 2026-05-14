# 🧭 Objetivo

Demonstrar como criar uma conexão direta e performática com pastas do SharePoint, utilizando o conector `SharePoint.Contents()` no Power Query.

Este método evita a necessidade de filtros demorados, reduz o tempo de carregamento e elimina a criação automática de parâmetros no Power BI.

---

# ⚙️ Parâmetro do Site

Primeiro, crie um parâmetro no seu modelo de dados:

## Nome do parâmetro

```PowerQuery
pLinkSiteSharepoint
```

## Valor

```PowerQuery
https://seudominio.sharepoint.com/sites/SeuSite
```

---

# 💡 Dica

A depender da arquitetura do OneDrive/SharePoint da sua empresa, a primeira pasta pode aparecer como:

- `Shared Documents`
ou
- `Documentos Compartilhados`

Recomendo executar o conector apenas até o primeiro nível abaixo para identificar o nome correto da biblioteca antes de seguir adiante:

```PowerQuery
SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion=15])
```

---

# 🚀 Conector Performático – Navegação Direta

```PowerQuery
let
    Fonte =
        SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion = 15]) // Conector base do site 
            {[Name="Biblioteca principal"]}[Content]                // Biblioteca principal 
            {[Name="Subpasta 1"]}[Content]                          // Subpasta 1
            {[Name="Subpasta 2"]}[Content]                          // Subpasta 2
            // Acrescente mais níveis se necessário 
in
    Fonte
```

---

# 🔍 Resultado

Esse método retorna diretamente o conteúdo da pasta final, sem precisar filtrar ou expandir manualmente todas as camadas anteriores.

---

# ✅ Vantagens

- Navegação hierárquica direta e limpa;
- Evita leitura desnecessária de milhares de arquivos;
- Totalmente compatível com SharePoint Online (API v15);
- Melhora o desempenho das consultas no Power BI e Power Query.

---

# 📘 Expandindo Arquivo `.xlsx` Manualmente (Sem Parâmetros Automáticos)

Agora que você já está dentro da pasta desejada, pode acessar diretamente o conteúdo do arquivo Excel sem deixar o Power BI criar parâmetros automáticos.

## Exemplo

```PowerQuery
let
    Fonte = 
        SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion = 15])
            {[Name="Biblioteca principal"]}[Content]
            {[Name="SubPasta1"]}[Content]
            {[Name="SubPasta2"]}[Content]
            {[Name="SubPasta3"]}[Content]
            {[Name="SubPasta4"]}[Content]            // Pasta Final
            {[Name="SeuArquivo.xlsx"]}[Content],     // Arquivo que você vai trabalhar 

    // Lê o conteúdo do Excel direto do binário retornado acima
    Planilhas = Excel.Workbook(Fonte, true),

    // Expande as planilhas e seleciona a aba desejada
    Dados = Planilhas{[Item="AbaDoArquivo", Kind="Sheet"]}[Data]
in
    Dados
```

---

# 📘 Expandindo Arquivo `.csv` Manualmente (Arquivo Único)

O mesmo conceito pode ser utilizado para arquivos `.csv`, utilizando a função `Csv.Document()`.

## Exemplo

```PowerQuery
let
    Fonte = 
        SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion = 15])
            {[Name="Biblioteca principal"]}[Content]
            {[Name="SubPasta1"]}[Content]
            {[Name="SubPasta2"]}[Content]
            {[Name="Arquivo.csv"]}[Content],

    // Converte o binário do CSV em tabela
    Dados = Csv.Document(
        Fonte,
        [
            Delimiter = ";",
            Encoding = 65001,
            QuoteStyle = QuoteStyle.Csv
        ]
    ),

    // Promove primeira linha para cabeçalho
    Cabecalho = Table.PromoteHeaders(Dados, [PromoteAllScalars=true])
in
    Cabecalho
```

---

# 📘 Expandindo Arquivo `.txt` Manualmente (Arquivo Único)

Arquivos `.txt` também podem ser lidos diretamente utilizando `Csv.Document()`, já que muitos arquivos texto utilizam delimitadores.

## Exemplo

```PowerQuery
let
    Fonte = 
        SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion = 15])
            {[Name="Biblioteca principal"]}[Content]
            {[Name="SubPasta1"]}[Content]
            {[Name="SubPasta2"]}[Content]
            {[Name="Arquivo.txt"]}[Content],

    // Leitura do arquivo TXT delimitado
    Dados = Csv.Document(
        Fonte,
        [
            Delimiter = "|",
            Encoding = 1252,
            QuoteStyle = QuoteStyle.None
        ]
    ),

    Cabecalho = Table.PromoteHeaders(Dados, [PromoteAllScalars=true])
in
    Cabecalho
```

---

# 📂 Conectando Múltiplos Arquivos `.csv` em uma Pasta

Quando existir mais de um arquivo `.csv` dentro da pasta, você pode combinar todos os arquivos manualmente sem utilizar o assistente automático do Power BI.

## Exemplo

```PowerQuery
let
    Fonte =
        SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion = 15])
            {[Name="Biblioteca principal"]}[Content]
            {[Name="SubPastaCSV"]}[Content],

    // Filtra apenas arquivos CSV
    ArquivosCSV =
        Table.SelectRows(
            Fonte,
            each Text.EndsWith([Name], ".csv")
        ),

    // Lê o conteúdo de cada arquivo
    LerArquivos =
        Table.AddColumn(
            ArquivosCSV,
            "Dados",
            each Csv.Document(
                [Content],
                [
                    Delimiter = ";",
                    Encoding = 65001,
                    QuoteStyle = QuoteStyle.Csv
                ]
            )
        ),

    // Combina os dados
    Expandido =
        Table.Combine(LerArquivos[Dados]),

    // Promove cabeçalhos
    Cabecalho =
        Table.PromoteHeaders(Expandido, [PromoteAllScalars=true])
in
    Cabecalho
```

---

# 📂 Conectando Múltiplos Arquivos `.txt` em uma Pasta

O mesmo processo pode ser utilizado para múltiplos arquivos `.txt`.

## Exemplo

```PowerQuery
let
    Fonte =
        SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion = 15])
            {[Name="Biblioteca principal"]}[Content]
            {[Name="SubPastaTXT"]}[Content],

    // Filtra apenas arquivos TXT
    ArquivosTXT =
        Table.SelectRows(
            Fonte,
            each Text.EndsWith([Name], ".txt")
        ),

    // Lê o conteúdo dos arquivos
    LerArquivos =
        Table.AddColumn(
            ArquivosTXT,
            "Dados",
            each Csv.Document(
                [Content],
                [
                    Delimiter = "|",
                    Encoding = 1252,
                    QuoteStyle = QuoteStyle.None
                ]
            )
        ),

    // Combina todas as tabelas
    Expandido =
        Table.Combine(LerArquivos[Dados]),

    // Promove cabeçalhos
    Cabecalho =
        Table.PromoteHeaders(Expandido, [PromoteAllScalars=true])
in
    Cabecalho
```

---

# 🧠 Boas Práticas Recomendadas

- Evite `SharePoint.Files()` — ele percorre o site inteiro antes de achar o arquivo;
- Prefira `SharePoint.Contents()` — acessa diretamente o caminho exato;
- Padronize nomes de pastas e arquivos (sem acentos ou espaços duplos);
- Utilize parâmetros centralizados (`pLinkSiteSharepoint`, `pArquivoAlvo`, etc.);
- Documente a hierarquia de acesso dentro do seu modelo ou `README.md`;
- Sempre filtre extensões (`.csv`, `.txt`, `.xlsx`) antes de expandir arquivos em massa;
- Prefira combinar arquivos manualmente para evitar criação automática de funções auxiliares desnecessárias.

---

# 🧩 Exemplo Comparativo

| Método | Função usada | Desempenho | Recomendado |
|:--|:--|:--|:--|
| ❌ Tradicional | `SharePoint.Files()` | Lento, carrega todos os arquivos do site | ❌ Não |
| ✅ Otimizado | `SharePoint.Contents()` | Rápido, acessa apenas o necessário | ✅ Sim |

---

# 🚀 Versão Mais Otimizada

Caso não precise verificar etapas intermediárias como nome do arquivo ou pastas, você pode resumir todas as etapas da versão anterior em uma única etapa ainda mais performática.

---

# 📘 Arquivo `.xlsx`

```PowerQuery
let
    Fonte = 
        Excel.Workbook(
            SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion = 15])
                {[Name="Biblioteca principal"]}[Content]
                {[Name="SubPasta1"]}[Content]
                {[Name="SubPasta2"]}[Content]
                {[Name="SubPasta3"]}[Content]
                {[Name="SubPasta4"]}[Content]
                {[Name="SeuArquivo.xlsx"]}[Content],
        true
        ){[Item="AbaDoArquivo", Kind="Sheet"]}[Data]
in
    Fonte
```

---

# 📘 Arquivo `.csv`

```PowerQuery
let
    Fonte =
        Table.PromoteHeaders(
            Csv.Document(
                SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion = 15])
                    {[Name="Biblioteca principal"]}[Content]
                    {[Name="SubPastaCSV"]}[Content]
                    {[Name="Arquivo.csv"]}[Content],
                [
                    Delimiter = ";",
                    Encoding = 65001,
                    QuoteStyle = QuoteStyle.Csv
                ]
            ),
            [PromoteAllScalars=true]
        )
in
    Fonte
```

---

# 📘 Arquivo `.txt`

```PowerQuery
let
    Fonte =
        Table.PromoteHeaders(
            Csv.Document(
                SharePoint.Contents(pLinkSiteSharepoint, [ApiVersion = 15])
                    {[Name="Biblioteca principal"]}[Content]
                    {[Name="SubPastaTXT"]}[Content]
                    {[Name="Arquivo.txt"]}[Content],
                [
                    Delimiter = "|",
                    Encoding = 1252,
                    QuoteStyle = QuoteStyle.None
                ]
            ),
            [PromoteAllScalars=true]
        )
in
    Fonte
```

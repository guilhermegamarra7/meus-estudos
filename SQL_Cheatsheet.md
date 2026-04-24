*Cheatsheet mantido por [Guilherme Gamarra]. Contribuições são bem-vindas!* 🚀

# 📘 SQL Cheatsheet

> Referência rápida dos principais comandos e conceitos de SQL.

---

## 📌 Índice

1. [Data Types mais frequentes](#1-data-types-mais-frequentes)
2. [SELECT](#2-select)
3. [WHERE](#3-where)
4. [ORDER BY](#4-order-by)
5. [Operadores AND, OR e NOT](#5-operadores-and-or-e-not)
6. [INSERT INTO](#6-insert-into)
7. [Valores NULL](#7-valores-null)
8. [UPDATE](#8-update)
9. [DELETE e DROP](#9-delete-e-drop)
10. [REPLACE](#10-replace)
11. [CAST](#11-cast)
12. [CTE (Common Table Expression)](#12-cte-common-table-expression)

---

## 1. Data Types mais frequentes

| Tipo | Descrição | Exemplo |
|------|-----------|---------|
| `TEXT` | Texto de comprimento variável (sem limite definido) | `'Olá Mundo'` |
| `VARCHAR(n)` | Texto de comprimento variável com limite `n` | `VARCHAR(100)` |
| `CHAR(n)` | Texto de comprimento **fixo** com tamanho `n` | `CHAR(10)` |
| `INT` / `INTEGER` | Número inteiro (sem casas decimais) | `42`, `-7` |
| `BIGINT` | Inteiro de grande magnitude | `9999999999` |
| `SMALLINT` | Inteiro de pequena magnitude (economiza espaço) | `32767` |
| `FLOAT` / `REAL` | Número de ponto flutuante (precisão simples) | `3.14` |
| `DECIMAL(p, s)` / `NUMERIC(p, s)` | Número decimal com precisão exata (`p` dígitos, `s` decimais) | `DECIMAL(10, 2)` → `99.99` |
| `BOOLEAN` | Valor verdadeiro ou falso | `TRUE`, `FALSE` |
| `DATE` | Data (sem hora) | `'2024-01-15'` |
| `TIME` | Hora (sem data) | `'14:30:00'` |
| `DATETIME` / `TIMESTAMP` | Data e hora combinadas | `'2024-01-15 14:30:00'` |
| `BLOB` | Dados binários brutos (imagens, arquivos) | — |

> 💡 **Dica:** Prefira `VARCHAR` a `TEXT` quando souber o comprimento máximo. Use `DECIMAL` em vez de `FLOAT` para valores monetários (maior precisão).

---

## 2. SELECT

Serve para selecionar colunas de uma tabela.

```sql
-- Seleciona todas as colunas da tabela Customers
SELECT * FROM Customers;

-- Seleciona apenas as colunas CustomerName e City
SELECT CustomerName, City FROM Customers;
```

---

## 3. WHERE

Usado para filtrar linhas com base em uma condição.

```sql
-- Seleciona as cidades onde o valor é 'Brasil'
SELECT City FROM Customers
WHERE City = 'Brasil';
```

---

## 4. ORDER BY

Ordena os resultados por uma ou mais colunas.

```sql
-- Ordena os produtos do mais caro para o mais barato
SELECT * FROM Products
ORDER BY Price DESC;

-- Ordena os produtos do mais barato para o mais caro
SELECT * FROM Products
ORDER BY Price ASC;
```

> 💡 `DESC` = decrescente (maior → menor) | `ASC` = crescente (menor → maior, padrão)

---

## 5. Operadores AND, OR e NOT

Combinam ou negam condições dentro do `WHERE`.

```sql
-- AND: ambas as condições devem ser verdadeiras
SELECT City FROM Customers
WHERE City = 'Brasil' AND CustomerName LIKE 'G%';
-- Retorna clientes do Brasil cujo nome começa com 'G'

-- OR: ao menos uma condição deve ser verdadeira
SELECT City FROM Customers
WHERE City = 'Brasil' OR City = 'Japão';
-- Retorna clientes do Brasil ou do Japão

-- NOT: nega a condição
SELECT City FROM Customers
WHERE NOT City = 'Brasil';
-- Retorna todos os clientes que NÃO são do Brasil
```

---

## 6. INSERT INTO

Usado para adicionar novos registros em uma tabela.

```sql
-- Inserção em todas as colunas (na ordem definida pela tabela)
INSERT INTO Customers
VALUES ('Cardinal', 'Tom B. Erichsen', 'Skagen 21', 'Stavanger', '4006', 'Norway');

-- Inserção em colunas específicas (mais seguro e recomendado)
INSERT INTO Customers (CustomerName, City, Country)
VALUES ('Cardinal', 'Stavanger', 'Norway');
```

> ⚠️ Ao omitir colunas no segundo formato, as colunas não mencionadas receberão `NULL` ou seu valor padrão (`DEFAULT`).

---

## 7. Valores NULL

`NULL` **não é zero** nem string vazia — representa a **ausência de um valor**.

```sql
-- Verifica onde o campo Address é nulo
SELECT CustomerName, ContactName, Address
FROM Customers
WHERE Address IS NULL;

-- Verifica onde o campo Address não é nulo
SELECT CustomerName, ContactName, Address
FROM Customers
WHERE Address IS NOT NULL;
```

> ⚠️ Nunca use `= NULL` para comparar — isso **não funciona**. Sempre use `IS NULL` ou `IS NOT NULL`.

---

## 8. UPDATE

Atualiza ou modifica um ou mais valores em uma tabela existente.

```sql
-- Atualiza o nome do contato e a cidade do cliente com ID 1
UPDATE Customers
SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
WHERE CustomerID = 1;
```

> ⚠️ **Sempre use `WHERE` com `UPDATE`!** Sem ele, **todos** os registros da tabela serão alterados.

---

## 9. DELETE e DROP

`DELETE` remove linhas de uma tabela. `DROP` remove a tabela inteira.

```sql
-- Deleta apenas o cliente especificado
DELETE FROM Customers
WHERE CustomerName = 'Alfreds Futterkiste';

-- (!) DELETA TODOS OS REGISTROS da tabela (a tabela continua existindo)
DELETE FROM Customers;

-- Para deletar a tabela inteira (estrutura + dados), use DROP
DROP TABLE Customers;
```

> ⚠️ `DELETE FROM` sem `WHERE` apaga todos os dados, mas mantém a tabela. `DROP TABLE` apaga tudo — inclusive a estrutura. Ambas são **irreversíveis**.

---

## 10. REPLACE

Serve para limpar dados ou padronizar formatos dentro de uma string.

```sql
-- Remove o texto ' PONTOS' do campo CR de todos os alunos
SELECT *,
REPLACE(CR, ' PONTOS', '')
FROM Alunos;
```

> 💡 `REPLACE(coluna, 'texto_a_remover', 'substituto')` — para apenas remover, use `''` como substituto.

---

## 11. CAST

Converte o tipo de dado de uma coluna. Útil quando uma coluna foi criada como `TEXT` mas contém números e você precisa fazer operações matemáticas.

```sql
-- Limpa o campo CR e converte de TEXT para INTEGER
SELECT nome,
CAST(REPLACE(CR, ' PONTOS', '') AS INTEGER) AS CR_LIMPO
FROM Alunos;
```

> 💡 `CAST(valor AS tipo)` — tipos comuns: `INTEGER`, `FLOAT`, `TEXT`, `DATE`. Combinado com `REPLACE`, é muito útil para limpar e converter dados "sujos" de uma só vez.

---

## 12. CTE (Common Table Expression)

Criação de tabelas temporárias dentro de uma consulta. Serve para organizar consultas complexas passo a passo, tornando o código mais legível e modular.

```sql
-- Passo 1: Limpa o texto da coluna CR
WITH Alunos_L AS (
  SELECT
    Codigo,
    Nome,
    Curso,
    REPLACE(CR, ' PONTOS', '') AS CR_TEXTO
  FROM Alunos
),

-- Passo 2: Converte CR_TEXTO para número
Alunos_C AS (
  SELECT
    Codigo,
    Nome,
    Curso,
    CAST(CR_TEXTO AS INTEGER) AS CR_NUM
  FROM Alunos_L
),

-- Passo 3: Classifica o desempenho de cada aluno
Alunos_D AS (
  SELECT
    Codigo,
    Nome,
    Curso,
    CASE
      WHEN CR_NUM >= 60 THEN 'REGULAR'
      ELSE 'ABAIXO DO DESEMPENHO'
    END AS Desempenho
  FROM Alunos_C
)

-- Consulta final usando a última CTE
SELECT * FROM Alunos_D;
```

> 💡 Cada bloco `WITH nome AS (...)` cria uma tabela temporária que só existe durante aquela consulta. Você pode encadear quantas CTEs precisar, referenciando a anterior na próxima.

---


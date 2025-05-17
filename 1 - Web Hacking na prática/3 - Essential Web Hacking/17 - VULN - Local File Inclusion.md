## Vulnerabilidade: LFI – Local File Inclusion

### O que é

LFI (Inclusão de Arquivo Local) é uma vulnerabilidade que ocorre quando uma aplicação web permite que um usuário forneça o caminho de um arquivo no servidor, e esse arquivo é carregado diretamente pelo código da aplicação, sem validação adequada.

Isso pode permitir que um atacante acesse arquivos sensíveis do sistema operacional, como `/etc/passwd` em servidores Linux, ou arquivos de configuração da aplicação.

---

## Exemplo prático com PHP vulnerável

### Código vulnerável:

```php
<?php
  $page = $_GET['page'];
  include($page);
?>
```

Neste código, o parâmetro `page` é passado diretamente para a função `include()` sem qualquer sanitização ou validação.

---

## Como o ataque funciona

A URL legítima seria:

```
https://site.com/index.php?page=about.php
```

Um atacante pode alterar essa URL para tentar incluir um arquivo local do sistema:

```
https://site.com/index.php?page=../../../../etc/passwd
```

Ou, com filtro de extensões, pode tentar algo como:

```
https://site.com/index.php?page=../../../../etc/passwd%00
```

O `%00` é o caractere nulo usado para truncar a extensão em algumas versões antigas do PHP (essa técnica foi mitigada em versões mais recentes).

---

## Impacto

- Leitura de arquivos sensíveis como:
    
    - `/etc/passwd` (Linux)
        
    - `C:\Windows\win.ini` (Windows)
        
    - Arquivos de configuração da aplicação (`config.php`, `.env`)
        
- Possível execução de código malicioso, se o atacante conseguir fazer upload de arquivos e depois incluí-los (em casos de RFI ou upload inseguro)
    
- Acesso a credenciais, tokens, caminhos internos, e falhas de configuração
    

---

## Prevenção

- Nunca usar entradas do usuário diretamente em funções como `include()`, `require()`, `fopen()`, `file_get_contents()`, etc.
    
- Implementar uma **lista branca** de páginas permitidas (ex: `about.php`, `home.php`)
    
- Usar caminhos absolutos e não concatenar strings de forma insegura
    
- Bloquear uso de diretórios relativos como `../` usando validação ou substituição de strings
    
- Desabilitar a exibição de erros detalhados em produção
    
- Utilizar frameworks que não expõem arquivos diretamente via parâmetros
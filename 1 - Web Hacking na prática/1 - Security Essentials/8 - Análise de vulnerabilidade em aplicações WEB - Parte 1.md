# Falhas Mais Comuns em Aplicações WEB

_Most common failures in WEB applications_

## TOP 10

1. Injection
    
2. Broken Authentication
    
3. Sensitive Data Exposure
    
4. XML External Entities (XXE)
    
5. Broken Access Control
    
6. Security Misconfiguration
    
7. Cross-Site Scripting (XSS)
    
8. Insecure Deserialization
    
9. Using Components with Known Vulnerabilities
    
10. Insufficient Logging & Monitoring
    

---

## 1. Injection

**Descrição:**  
Uma aplicação fica vulnerável a injeção quando:

- Os dados fornecidos pelo usuário não são validados, filtrados ou sanitizados.
    
- Consultas dinâmicas ou chamadas não parametrizadas são usadas diretamente em interpretadores (SQL, Command, LDAP).
    
- Dados enviados pelo usuário são utilizados diretamente ou concatenados em comandos.
    

**Exemplo:**  
Um aplicativo usa dados inseguros enviados pelo usuário em uma consulta SQL:

```php
$query = "SELECT * FROM noticias WHERE id=" . $_GET['id'];
```

O atacante manipula o parâmetro `id` no navegador:

```
-1 or 1=1#
```

Assim, o comportamento do sistema é alterado, permitindo ações não autorizadas.  
Exemplo prático:  
`https://blog.nubank.com.br/noticias=-1 or 1=1#`

---

## 2. Broken Authentication

**Descrição:**  
Falhas na autenticação, gestão de identidade e gerenciamento de sessões expõem aplicações a ataques como:

- Ataques automatizados (credential stuffing, brute force).
    
- Uso de senhas padrão, fracas ou conhecidas.
    
- Recuperação de senha ineficaz.
    
- Armazenamento de senhas em texto claro ou com hashes fracos.
    
- Má gestão de tempo de sessão ou invalidação de IDs de sessão.
    

**Exemplo:**

- Credential Stuffing: listas de senhas conhecidas usadas em brute force.
    
- Credenciais fracas como `admin/admin`.
    
- Sessões permanecem ativas sem expiração adequada em computadores públicos.
    

---

## 3. Sensitive Data Exposure

**Descrição:**  
Dados sensíveis (senhas, números de cartão de crédito, informações pessoais) podem ser expostos por:

- Transmissão de dados em texto claro (HTTP, SMTP, FTP).
    
- Criptografia fraca ou antiquada.
    
- Não validação de certificados SSL/TLS.
    

**Exemplo:**

- Aplicativo criptografa cartões no banco de dados, mas falhas de SQL Injection expõem dados em texto claro.
    
- Senhas armazenadas com hash MD5 (sem salt) podem ser facilmente quebradas com o uso de GPUs.
    

---

## 4. XML External Entities (XXE)

**Descrição:**  
Aplicações que aceitam entradas XML não confiáveis podem ser vulneráveis se:

- Definições de tipo de documento (DTDs) estiverem ativadas.
    
- Utilizarem tecnologias como SOAP ou SAML que lidam com XML.
    

**Exemplo:**  
Manipulação de XML para ler arquivos do servidor:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE crowsec [
<!ELEMENT root ANY >
<!ENTITY entidade SYSTEM "file:///etc/passwd" >
]>
<root>&entidade;</root>
```

---

## 5. Broken Access Control

**Descrição:**  
O controle de acesso inadequado permite:

- Modificação de URLs ou estados internos para burlar ACLs.
    
- Alteração de chaves primárias e acesso a dados de outros usuários.
    
- Elevação de privilégios (ex.: agir como administrador).
    
- Manipulação de tokens JWT para obtenção de acessos indevidos.
    

---

## 6. Security Misconfiguration

**Descrição:**  
Aplicativos vulneráveis a má configuração de segurança apresentam:

- Proteções de segurança insuficientes em partes da stack.
    
- Serviços desnecessários ativos (portas, páginas, contas).
    
- Senhas padrão mantidas.
    
- Mensagens de erro que revelam informações técnicas detalhadas.
    

**Exemplo:**  
Frameworks como **Laravel** e **Django** com modo _debug_ ativado podem expor:

- Chaves de criptografia.
    
- Credenciais de acesso a banco de dados.
    

---

## 7. Cross-Site Scripting (XSS)

**Descrição:**  
Tipos de XSS:

- **Reflected XSS**: Entrada maliciosa é refletida de volta para o usuário em uma resposta.
    
- **Stored XSS**: Dados maliciosos armazenados e apresentados posteriormente a outros usuários.
    
- **DOM XSS**: Manipulação dinâmica de dados no lado do cliente (navegador).
    

**Exemplo:**  
Código PHP vulnerável:

```php
<?php
$nome = $_GET['nome'];
$mensagem = "Olá <b>{$nome}</b>";
```

O atacante injeta código HTML/JavaScript manipulando a URL.

---

## 8. Insecure Deserialization

**Descrição:**  
Desserialização insegura permite:

- **Execução de Código**: Manipulando a estrutura de objetos para executar código no servidor.
    
- **Violação de Controle de Acesso**: Modificando objetos serializados para acessar dados ou funções não autorizadas.
    

**Exemplo:**  
Manipulação de objetos enviados ao servidor para alterar seu comportamento durante ou após a desserialização.

---

## 9. Using Components with Known Vulnerabilities

**Descrição:**  
Aplicações e APIs podem se tornar vulneráveis se usarem componentes (bibliotecas, frameworks, módulos) que possuem vulnerabilidades conhecidas.

**Exemplos comuns incluem:**

- Utilizar versões desatualizadas de bibliotecas ou frameworks que possuem falhas de segurança públicas.
    
- Não monitorar vulnerabilidades conhecidas em componentes utilizados no projeto.
    
- Aplicações que não verificam regularmente atualizações de segurança dos seus componentes.
    
- Dependências transitivas (dependências das dependências) também podem introduzir riscos.
    

**Exemplo:**

- Um site utiliza uma versão antiga do framework **Struts** que possui uma vulnerabilidade conhecida de RCE (_Remote Code Execution_). Um atacante explora essa falha para executar comandos arbitrários no servidor.
    

---

## 10. Insufficient Logging & Monitoring

**Descrição:**  
A falta de registro e monitoramento adequado de atividades de segurança dentro de aplicações e infraestrutura permite que ataques passem despercebidos.

**Vulnerabilidades comuns incluem:**

- Falta de registro de eventos de segurança relevantes (como tentativas de login inválidas, alterações de permissões, etc.).
    
- Logs que não são monitorados ou revisados regularmente.
    
- Falha em gerar alertas em tempo hábil quando atividades suspeitas ou anômalas são detectadas.
    
- Logs armazenados sem segurança adequada, podendo ser acessados por atacantes.
    

**Exemplo:**

- Um invasor realiza um ataque de força bruta para descobrir senhas de usuários. Como o sistema não registra tentativas de login falhas ou não alerta os administradores, o ataque permanece indetectável por longos períodos.
    

---

# Resumo Final - TOP 10 OWASP

|Ordem|Vulnerabilidade|
|:-:|---|
|1|Injection|
|2|Broken Authentication|
|3|Sensitive Data Exposure|
|4|XML External Entities (XXE)|
|5|Broken Access Control|
|6|Security Misconfiguration|
|7|Cross-Site Scripting (XSS)|
|8|Insecure Deserialization|
|9|Using Components with Known Vulnerabilities|
|10|Insufficient Logging & Monitoring|

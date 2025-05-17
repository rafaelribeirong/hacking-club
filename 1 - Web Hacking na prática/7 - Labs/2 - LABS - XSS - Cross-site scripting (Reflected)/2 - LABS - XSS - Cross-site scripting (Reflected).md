[[8 - VULN - XSS - Cross-site scripting (Reflected)]]
## Procedimento Técnico – Identificação de XSS (Cross-site Scripting) Refletido

### 1. Objetivo

Verificar a existência de vulnerabilidade do tipo XSS refletido em uma aplicação web e demonstrar sua exploração.

### 2. Ambiente

- Alvo: `http://10.10.0.7/`
    
- Ferramentas: Navegador web (Firefox/Chrome), Burp Suite (opcional), navegador com DevTools habilitado.
    

### 3. Etapas

#### 3.1 Acesso ao Formulário

1. Acesse o site alvo via navegador:
    
    ```
    http://10.10.0.7/
    ```
    
2. Identifique a presença de formulário HTML com os seguintes campos:
    
    - `name`
        
    - `email`
        
    - `message`
        

#### 3.2 Envio de Dados e Análise da Resposta

1. Preencha o formulário com dados arbitrários e envie.  
    Exemplo de requisição após envio:
    
    ```
    http://10.10.0.7/?name=aaaa&email=teste@exemplo.com&message=teste
    ```
    
2. Observe se os dados são refletidos na página HTML, ou seja, se a resposta da aplicação exibe os valores inseridos.
    

#### 3.3 Teste de Injeção HTML

1. Insira tags HTML simples no campo `message` (exemplo: `<b>teste</b>`) e envie o formulário.
    
2. Se o texto aparecer em **negrito**, indica que o site está aceitando HTML sem sanitização.
    

#### 3.4 Teste de XSS Refletido

1. Substitua o conteúdo do campo `message` por um payload de XSS básico:
    
    ```html
    <script>alert("este é um teste XSS")</script>
    ```
    
2. Se um alerta for exibido no navegador, isso confirma a execução do script e a presença da vulnerabilidade.
    

#### 3.5 Validação via URL

1. O mesmo payload pode ser testado diretamente via URL, codificando os caracteres especiais:
    
    ```
    http://10.10.0.7/?message=%3Cscript%3Ealert(%22teste%20XSS%22)%3C%2Fscript%3E
    ```
    
2. Se o script for executado, a falha é confirmada.
    

### 4. Impacto

Permite a execução de código arbitrário no navegador da vítima. Com isso, é possível:

- Roubar cookies de sessão.
    
- Redirecionar usuários para páginas de phishing.
    
- Injetar malwares.
    
- Registrar tudo que o usuário digita (keylogging).
    

### 5. Recomendação de Mitigação

- Escapar ou sanitizar todas as entradas refletidas no HTML.
    
- Utilizar frameworks seguros que previnem XSS por padrão.
    
- Ativar o uso de cookies com as flags `HttpOnly` e `Secure`.
    
- Implementar Content Security Policy (CSP).
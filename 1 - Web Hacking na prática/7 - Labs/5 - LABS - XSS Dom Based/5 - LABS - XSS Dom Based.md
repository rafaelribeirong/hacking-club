[[10 - VULN - XSS - Cross-site scripting (Dom based)]]

##  Procedimento para Identificação de Vulnerabilidade DOM-Based XSS

### 1. **Objetivo**

Detectar e validar a presença de vulnerabilidades DOM-Based XSS em aplicações web, com foco na manipulação direta do DOM via parâmetros da URL.

---

### 2. **Pré-Requisitos**

- Navegador com console de desenvolvedor (ex: Chrome, Firefox)
    
- Permissão/autorização para testes
    
- Ferramentas opcionais: Burp Suite, OWASP ZAP, extensão _HackTools_, _XSS Radar_
    

---

### 3. **Passos Detalhados**

#### 3.1 Identifique possíveis pontos de entrada

- Acesse a aplicação web.
    
- Procure campos de entrada que **não disparam requisições ao servidor**.
    
- Exemplo comum: campos de **pesquisa**, filtros, tags ou parâmetros de URL como `?search=`, `?query=`, `?msg=`, etc.
    

#### 3.2 Inspecione o comportamento do frontend

- Insira um valor simples no parâmetro da URL:
    
    ```
    http://exemplo.com/?search=teste
    ```
    
- Observe se esse valor aparece na tela após carregamento da página.
    

#### 3.3 Verifique o código-fonte da página

- Pressione F12 e vá para a aba "Elements" e "Sources".
    
- Busque por **funções JavaScript** que utilizem:
    
    - `document.write()`
        
    - `innerHTML`
        
    - `eval()`
        
    - `location.hash`, `location.search`, `document.URL`, `document.location`
        

Exemplo:

```javascript
const param = new URLSearchParams(window.location.search);
const query = param.get('search');
document.getElementById('searchmessage').innerHTML = `Resultados para: ${query}`;
```

#### 3.4 Teste para HTML Injection

- Acesse a URL com HTML simples:
    
    ```
    http://exemplo.com/?search=<b style="color:red">vermelho</b>
    ```
    
- Se o texto aparecer formatado em vermelho, já há **injeção de HTML**.
    

#### 3.5 Teste para execução de script (DOM XSS)

- Teste com um script simples:
    
    ```
    http://exemplo.com/?search=<script>alert("DOM XSS")</script>
    ```
    
- Caso a página execute o alerta, a vulnerabilidade DOM XSS está confirmada.
    

#### 3.6 Tente injeções mais discretas

- Para evitar bloqueios por filtros, use variações:
    
    ```html
    <img src=x onerror=alert('DOM XSS')>
    <svg/onload=alert('DOM XSS')>
    <script>confirm(document.domain)</script>
    ```
    

---

### 4. **Exemplos de Locais Comuns para DOM XSS**

Além de campos de pesquisa, outras áreas vulneráveis podem incluir:

- Campos de feedback que atualizam sem recarregar a página
    
- Parâmetros de URL usados em dashboards ou páginas SPA (Single Page Applications)
    
- Campos de filtro, ordenação ou mensagens em tempo real
    
- Campos usados em frameworks JS como AngularJS, Vue.js (com má configuração)
    

---

### 5. **Recomendações de Mitigação**

- Evitar o uso de `innerHTML`, `document.write`, `eval` com entradas do usuário
    
- Usar `textContent` ao invés de `innerHTML`
    
- Aplicar sanitização com ferramentas como:
    
    - [`DOMPurify`](https://github.com/cure53/DOMPurify)
        
- Implementar políticas de segurança:
    
    - **CSP (Content-Security-Policy)** que restrinja a execução de scripts externos/injetados
        

---

### 6. **Registro e Relatório**

Caso a vulnerabilidade seja confirmada:

- Documente a URL testada
    
- Inclua print da execução (ex: alerta disparado)
    
- Copie o trecho vulnerável do JavaScript
    
- Reforce o risco e sugira as correções mencionadas
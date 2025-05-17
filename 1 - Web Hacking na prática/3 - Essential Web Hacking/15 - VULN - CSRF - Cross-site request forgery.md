## O que é CSRF?

**Cross-Site Request Forgery** (ou **falsificação de requisição entre sites**) é uma vulnerabilidade que permite que um site malicioso **engane um navegador autenticado** a fazer ações em outro site legítimo **sem o consentimento do usuário**.

---

## Como funciona (visão geral)

1. O usuário está **logado** em um sistema legítimo (ex: **portal RH da empresa**).
    
2. O navegador do usuário armazena um **cookie de sessão** válido (ex: `JSESSIONID`, `PHPSESSID`, etc.).
    
3. O atacante cria um **site controlado por ele** com um formulário ou requisição maliciosa.
    
4. Quando o usuário logado visita esse site malicioso, **o navegador automaticamente envia o cookie da sessão** para o domínio da empresa.
    
5. A aplicação do lado da empresa **aceita e executa a requisição como se fosse legítima**, porque:
    
    - Ela **não verifica a origem da requisição** (falha no backend),
        
    - E o navegador **envia os cookies automaticamente**.
        

---

## Cenário prático — Seu exemplo

- Usuário do RH está autenticado no site: `https://portalth.empresa.com.br`
    
- Ele acessa um site malicioso criado por um atacante:
    
    ```
    https://malicioso.com
    ```
    
- Nesse site malicioso, há um código escondido que tenta fazer uma requisição como esta:
    

```html
<form action="https://portalth.empresa.com.br/api/atualizar_salario" method="POST">
  <input type="hidden" name="usuario_id" value="1234">
  <input type="hidden" name="salario" value="99999">
  <input type="submit">
</form>

<script>
  document.forms[0].submit();
</script>
```

Quando o RH acessa esse site malicioso (sem saber), **essa requisição é automaticamente enviada para o portal da empresa**.  
O navegador dele **envia os cookies de sessão válidos**, e a aplicação **processa o pedido como se fosse legítimo**, **aumentando o salário do atacante**.

---

## Por que essa vulnerabilidade acontece?

A CSRF **existe por dois motivos combinados**:

1. **Comportamento padrão do navegador:**
    
    - O navegador **sempre envia cookies automaticamente** para o domínio original da requisição.
        
    - Isso acontece mesmo que a requisição seja disparada de um site de terceiro.
        
2. **Falta de validação no backend:**
    
    - A aplicação **não valida se a requisição veio de uma origem confiável** (ex: `Origin`, `Referer`, ou **token CSRF**).
        
    - A aplicação **confia apenas no cookie de sessão**, o que **não é suficiente**.
        

---

## Como prevenir CSRF corretamente

1. **Token anti-CSRF (synchronizer token pattern):**
    
    - O backend gera um token único por sessão e o insere em cada formulário HTML.
        
    - Esse token precisa estar presente no corpo da requisição e ser validado no backend.
        
2. **Verificar `Origin` e/ou `Referer` no servidor:**
    
    - Bloquear requisições que **não vierem do domínio oficial**.
        
3. **Cookies com flag `SameSite`:**
    
    - `SameSite=Strict` ou `SameSite=Lax` evita que cookies sejam enviados em requisições de outros sites.
        
    
    Exemplo de cabeçalho seguro:
    
    ```
    Set-Cookie: JSESSIONID=abc123; Secure; HttpOnly; SameSite=Strict
    ```
    
4. **Usar métodos `POST` com validação (evitar `GET` para ações críticas)**
    

---

## Resumo técnico do fluxo CSRF (em tópicos):

- A vítima está logada no `portalth.empresa.com.br`.
    
- O navegador tem um cookie válido da sessão.
    
- Ela visita um site malicioso com uma requisição automática oculta.
    
- O navegador envia o cookie para `portalth.empresa.com.br`.
    
- O servidor **não percebe que a requisição veio de fora**.
    
- Ação é executada com os privilégios da vítima (RH, admin etc.).
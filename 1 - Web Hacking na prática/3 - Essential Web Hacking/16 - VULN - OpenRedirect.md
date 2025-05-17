## O que é Open Redirect?

**Open Redirect** (ou redirecionamento aberto) é uma vulnerabilidade em aplicações web que permite que um atacante redirecione um usuário para qualquer site externo, usando uma funcionalidade legítima do sistema — sem que o servidor valide ou bloqueie esse redirecionamento.

Em outras palavras: o sistema aceita redirecionar o usuário para qualquer link informado na URL, mesmo que esse link leve a um site malicioso.

---

## Exemplo prático com código HTTP 301 (redirecionamento permanente)

Suponha que o Nubank use uma URL para redirecionar o usuário após login:

```
https://login.nubank.com.br/redirecionar?url=https://nubank.com.br/home
```

Agora imagine que um atacante envie um link assim:

```
https://login.nubank.com.br/redirecionar?url=https://nubank-seguranca.com/login-fake
```

Ao acessar esse link, o servidor do Nubank pode responder com:

```
HTTP/1.1 301 Moved Permanently
Location: https://nubank-seguranca.com/login-fake
```

O navegador segue esse redirecionamento automaticamente, levando o usuário a um site falso que simula o Nubank.

---

## Por que isso é um problema?

Essa falha é perigosa porque:

- O link começa com um domínio legítimo (`nubank.com.br`), o que faz o usuário confiar.
    
- O navegador redireciona automaticamente, sem mostrar o destino real.
    
- O atacante pode aplicar um golpe com **engenharia social**: o usuário acredita que está no site oficial, mas está em uma página falsa.
    
- É comum em **campanhas de phishing**, principalmente via e-mail, SMS ou WhatsApp.
    

---

## Exemplo de ataque com Open Redirect

O atacante envia um e-mail para o RH da empresa ou para clientes com o seguinte link:

```
Clique para acessar sua conta: 
https://login.nubank.com.br/redirecionar?url=https://nubank-confirmacao-conta.com
```

- O usuário, ao clicar, é redirecionado automaticamente para o site falso.
    
- No site fake, insere login, senha, CPF ou número do cartão.
    
- O atacante captura os dados e executa fraudes.
    

---

## Como mitigar esse tipo de falha?

1. **Validar o parâmetro de redirecionamento**
    
    - Permitir apenas redirecionamento para páginas internas.
        
    - Exemplo: permitir `/home`, `/perfil`, e não permitir URLs externas.
        
2. **Evitar redirecionamento baseado em parâmetro**
    
    - Substituir `?url=` por identificadores internos.
        
3. **Exibir uma página de aviso antes do redirecionamento**
    
    - Informar ao usuário: “Você está sendo redirecionado para um site externo. Deseja continuar?”
        
4. **Utilizar lista de domínios confiáveis**
    
    - Aplicar lista branca (whitelist) no backend.
        
5. **Monitorar e registrar redirecionamentos**
    
    - Criar alertas para URLs externas sendo acessadas via redirecionamento.
        

---

## Resumo

| Item            | Descrição                                                 |
| --------------- | --------------------------------------------------------- |
| Vulnerabilidade | Redirecionamento aberto (Open Redirect)                   |
| Consequência    | Phishing, roubo de dados, perda de confiança              |
| Exemplo prático | `https://login.nubank.com.br/redirecionar?url=site-falso` |
| Técnica usada   | Engenharia social                                         |
| Prevenção       | Validação de URLs, páginas de aviso, bloqueio de externos |

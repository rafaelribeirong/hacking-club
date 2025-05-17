## 🔗 Links de Referência

1. **Event-Driven Architecture na AWS (Arquitetura Orientada a Eventos)**  
    Explica como projetar sistemas baseados em eventos usando os serviços da AWS.  
    [https://aws.amazon.com/pt/event-driven-architecture/](https://aws.amazon.com/pt/event-driven-architecture/)
    
2. **EDA em Arquitetura de Microsserviços - Medium**  
    Um artigo introdutório e direto ao ponto sobre como aplicar a arquitetura orientada a eventos com microsserviços.  
    [https://medium.com/@marcelomg21/event-driven-architecture-eda-em-uma-arquitetura-de-micro-servi%C3%A7os-1981614cdd45](https://medium.com/@marcelomg21/event-driven-architecture-eda-em-uma-arquitetura-de-micro-servi%C3%A7os-1981614cdd45)
    
3. **O que são Cookies – Cloudflare**  
    Explica o conceito de cookies, privacidade e rastreamento no contexto web.  
    [https://www.cloudflare.com/pt-br/learning/privacy/what-are-cookies/](https://www.cloudflare.com/pt-br/learning/privacy/what-are-cookies/)
    

---

## Conceitos Fundamentais sobre Arquitetura de Sistemas, Front-end, Back-end e Comunicação via API

### Chamadas por Rota em vez de Arquivo Estático

Em aplicações modernas, é comum acessarmos rotas em vez de arquivos diretamente. Por exemplo:

- Em vez de acessar `index.html` ou `pagina.php`, acessamos rotas como `/home` ou `/api/v1/produtos`.
    

Essas rotas são processadas por frameworks no **back-end**, que interpretam a requisição e retornam o conteúdo apropriado, geralmente em **JSON**, **HTML** ou outro tipo de resposta. O tipo de conteúdo enviado ao servidor (e recebido) é identificado pelo cabeçalho **Content-Type** (como `application/json` ou `text/html`).

---

### Front-end vs. Back-end

**Front-end** é a parte visual da aplicação – aquilo que o usuário interage. Exemplos:

- Sites e sistemas web
    
- Aplicativos mobile
    
- Aplicações desktop
    

Tecnologias comuns no front-end:

- **HTML, CSS, JavaScript**
    
- Frameworks/libraries: **React**, **Vue.js**, **Angular**, **Svelte**
    
- Aplicações desktop: **Electron**, **Tauri**
    
- Aplicações mobile híbridas: **React Native**, **Flutter**
    

**Importante:**  
A lógica de negócio **nunca deve ficar no front-end**, pois isso compromete a segurança e o controle do sistema.

---

**Back-end** é responsável por:

- Processamento de dados
    
- Regras de negócio
    
- Acesso ao banco de dados (CRUD)
    
- Segurança (autenticação/autorização)
    
- Integração com outros serviços (pagamentos, envio de e-mails, etc.)
    

Linguagens comuns no back-end:

- **Node.js**, **Python (Django/Flask)**, **Java (Spring)**, **PHP (Laravel)**, **Ruby**, **Go**
    

---

### Monólito vs. Microsserviços

**Monólito** é uma aplicação única, onde tudo (front, controle e banco) está junto. Exemplo clássico com três camadas:

1. **View (apresentação)** → Front-end
    
2. **Controle (negócio)** → Autenticação, NFe, Financeiro, etc.
    
3. **Dados** → Banco de dados
    

Problemas com monólitos:

- Escalabilidade limitada (precisa aumentar recursos do mesmo servidor: RAM/CPU)
    
- Dependência entre módulos (um afeta o outro)
    
- Difícil de manter e evoluir com o tempo
    

---

**Microsserviços** são uma evolução:

- Cada serviço é independente
    
- Comunicação entre eles via API
    
- Cada serviço tem **seu próprio banco de dados**
    
- Exemplo: Serviço de pedidos, estoque, NFe, pagamentos
    

**API Gateway**: Componente que recebe as requisições dos usuários e as direciona para o serviço correspondente (estoque, pagamento, pedidos, etc.)

---

### Comunicação Assíncrona com Message Broker

Um problema dos microsserviços é a **requisição síncrona** (um depende da resposta do outro). Solução: usar **mensageria assíncrona** com _Message Broker_.

Exemplo de tópicos:

- `order_created`
    
- `payment_created`
    
- `nfe_created`
    
- `item_removed_from_stock`
    

Esses eventos são enviados para um _broker_ como **Kafka**, **RabbitMQ**, **AWS SNS/SQS**. Outros serviços "escutam" esses eventos e agem de acordo, sem depender da resposta imediata do outro serviço.

---

### Reverse Proxy e Proxy

**Proxy comum**: utilizado para controle de acesso à internet.  
Exemplo: A empresa quer bloquear o acesso ao Twitter no horário de trabalho. O proxy intercepta as requisições e bloqueia ou permite com base em regras configuradas.

**Reverse Proxy**: faz o caminho inverso – recebe requisições externas e redireciona para servidores internos. Exemplo:  
O usuário acessa `meusite.com`, e o reverse proxy envia a requisição para `app01.interno`, `app02.interno`, etc.

Ferramentas comuns de Reverse Proxy:

- **NGINX**
    
- **HAProxy**
    
- **Traefik**
    

---
### **Resumo Geral do Conteúdo**

A aula aborda os seguintes **tópicos centrais**:

1. **Conceito de HTTP e sua natureza "stateless"**
    
    - Cada requisição HTTP é independente. O servidor não sabe que é o mesmo usuário em requisições diferentes.
        
    - Exemplo: entrar no `/contato` e depois em `/ajuda` são requisições separadas sem vínculo entre si.
        
2. **Como sites reconhecem usuários (mesmo sem login)**
    
    - Exemplo: Mercado Livre mantém seu carrinho mesmo sem você estar logado.
        
    - Isso é feito através de **cookies**.
        
3. **Explicação dos cookies:**
    
    - Campos de um cookie: `nome`, `valor`, `data de expiração`, `domínio`, `path`, `secure`, `httpOnly`, `sameSite`.
        
    - Tipos:
        
        - **Session Cookie**: desaparece ao fechar o navegador.
            
        - **Persistent Cookie**: persiste após o navegador ser fechado.
            
    - Flags de segurança:
        
        - `secure`: só em HTTPS.
            
        - `httpOnly`: não acessível por JavaScript.
            
        - `sameSite`: restringe envio para sites de terceiros (proteção contra CSRF).
            
4. **Criação de cookies:**
    
    - Pelo **navegador (front-end)**.
        
    - Pelo **servidor (back-end)** através do cabeçalho `Set-Cookie` na resposta HTTP.
        
5. **Casos práticos e riscos:**
    
    - Cookies são usados para rastrear comportamento, como aumento de preço em passagens (tracking de usuário).
        
    - Compartilhar tela com cookies expostos pode levar a **roubo de sessão**.
        
    - Exemplo de ataque possível: roubo de cookie via CSS (Side-channel).
        
6. **Ferramentas usadas no navegador (devtools):**
    
    - Aba "Application" para visualizar cookies.
        
    - Aba "Network" para ver os headers HTTP e o `Set-Cookie`.
        
7. **Dica prática:**
    
    - Antes de comprar passagens, **limpe os cookies** ou use aba anônima para evitar rastreamento e manipulação de preços.
        

---

### 📋 Explicação Técnica e Estruturada

|Conceito|Explicação|
|---|---|
|**Stateless**|O protocolo HTTP não mantém estado entre requisições. Cada acesso ao servidor é isolado.|
|**Cookies**|Pequenos arquivos armazenados no navegador para manter estado, como sessões, carrinhos de compras etc.|
|**Session Cookie**|Cookie temporário que some ao fechar o navegador.|
|**Persistent Cookie**|Tem data de expiração definida, persiste mesmo após fechar o navegador.|
|**Headers HTTP**|`Set-Cookie` é o header usado pelo servidor para criar cookies no navegador do usuário.|
|**Flags de segurança**|`Secure`, `HttpOnly`, `SameSite` – usadas para proteger cookies contra ataques.|
|**Risco de segurança**|Cookies podem ser roubados (ex: exposição via tela ou falha de configuração), resultando em sequestro de sessão.|
|**Devtools**|Ferramentas de desenvolvedor no navegador usadas para visualizar e manipular cookies, ver headers e analisar segurança.|

---

### Aulas citadas (aparentemente do curso):

- Aula sobre **cookies**.
    
- Aula sobre **como roubar cookies com CSS** (ataque avançado – _CSS exfiltration_).
    
- Aulas práticas sobre **segurança em aplicações web**.
    

---

### Dica prática de segurança

> Nunca compartilhe sua tela com cookies visíveis. Alguém pode copiar os dados do cookie e usar para sequestrar sua sessão em sites logados.

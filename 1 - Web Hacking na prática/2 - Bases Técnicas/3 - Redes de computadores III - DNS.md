## O que é DNS?

**DNS** significa **Domain Name System (Sistema de Nomes de Domínio)**.  
Ele é como a **agenda de contatos da internet**: em vez de você decorar o número (IP), você decora um nome (como `google.com` ou `crowsec.com.br`), e o DNS faz a tradução desse nome para um número (IP) que o computador realmente entende.

> Exemplo:  
> Você digita `www.crowsec.com.br`, mas o navegador precisa do IP correspondente, como `104.21.74.113`.  
> O DNS entra em ação e faz essa conversão.

🔗 Referência: [O que é DNS e como a Internet funciona | Cloudflare](https://www.cloudflare.com/pt-br/learning/dns/what-is-dns/)

---

## Por que o DNS é comparado a um CEP?

Assim como você não decora o número de cada casa, mas sabe o nome da rua e número, o DNS permite que você **não precise decorar números IPs**, apenas nomes de sites.  
Por trás de cada site, existe um número IP.

---

## Como é composto um nome de domínio?

Exemplo: `www.crowsec.com.br`

- `www` = subdomínio
    
- `crowsec` = nome do domínio
    
- `.com.br` = TLD (Top-Level Domain)
    

---

### O que é TLD (Top-Level Domain)?

São os **domínios de nível superior**. Veja alguns exemplos:

|TLD|Significado|
|---|---|
|.com|Comercial|
|.br|Brasil|
|.com.br|Comercial no Brasil|
|.uk|Reino Unido|
|.us|Estados Unidos|
|.org|Organizações sem fins lucrativos|
|.me|Pessoal (muito usado para portfólios)|

---

## Etapas da Resolução de DNS (fluxo)

1. O usuário digita `www.crowsec.com.br`.
    
2. O navegador pergunta ao roteador local: “Quem sabe onde está esse domínio?”
    
3. O roteador consulta os **Root Servers**: “Quem cuida do `.br`?”
    
4. Os Root Servers respondem: “Pergunte ao servidor TLD `.br` (ex: `a.dns.br`).”
    
5. O roteador consulta o **TLD do .br**, que responde com o **Name Server (NS)** responsável pelo domínio (`crowsec.com.br`).
    
6. O roteador pergunta ao **NS do domínio**, que responde com o **endereço IP** (`104.21.74.113`).
    
7. O navegador se conecta diretamente ao IP e carrega o site.
    

> Nenhuma conexão real é feita com o DNS – a conexão é feita com o IP. O navegador apenas usa o nome (DNS) para facilitar nossa vida.

---

## Tipos de registros DNS

|Registro|O que faz|Explicação simples|
|---|---|---|
|A|Aponta para um **IP v4**|Endereço numérico (ex: 104.21.74.113)|
|AAAA|Aponta para um **IP v6**|Versão nova do IP (endereços longos)|
|CNAME|Um **alias**, aponta para outro domínio|“www” pode apontar para “site.principal.com”|
|MX|Servidor de **e-mail**|Onde chegam e-mails do domínio|
|TXT|Texto usado para **verificações e segurança**|SPF, DKIM, etc. ficam aqui|
|NS|**Name Server** do domínio|Quem responde por esse domínio|
|SOA|**Start of Authority**|Define o “dono” e configurações do domínio|
|PTR|Aponta um IP para um nome (DNS reverso)|Usado em servidores de e-mail para validar IP|

---

## O que é SPF?

**SPF (Sender Policy Framework)** é um tipo de verificação de segurança via DNS.

> Serve para informar **quais servidores podem enviar e-mails em nome do seu domínio**.

Isso ajuda a proteger contra **falsificação de e-mails (phishing)**.

🔗 Saiba mais sobre autenticação de e-mails e DNS no guia da Cloudflare: [O que é DNS | Cloudflare](https://www.cloudflare.com/pt-br/learning/dns/what-is-dns/)

---

## E se os servidores DNS do `.br` caírem?

Se os servidores do **.br** pararem de funcionar, **todos os sites com terminações .br** ficarão inacessíveis, porque o navegador não conseguirá resolver o IP para os domínios.

Isso já aconteceu no passado por incidentes como ataques DDoS, prejudicando a comunicação de milhares de sites brasileiros.
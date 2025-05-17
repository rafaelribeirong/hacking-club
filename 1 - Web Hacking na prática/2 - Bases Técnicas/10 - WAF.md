## O que é um Firewall?

Um **firewall tradicional** atua como uma **barreira de segurança entre sua rede interna e o tráfego externo** (como a internet). Ele analisa e filtra pacotes de rede com base em regras como IPs, portas e protocolos.

### Para que serve:

- Bloquear acessos indevidos
    
- Permitir apenas tráfego autorizado
    
- Impedir escaneamentos de portas ou tentativas de acesso direto
    

### Exemplo de fluxo com firewall:

```
[ Usuário / Atacante ] ---> [ FIREWALL ] ---> [ Servidor ]
                        (regras de IP, porta e protocolo)
```

---

## O que é WAF (Web Application Firewall)?

Um **WAF** é um tipo específico de firewall que **protege aplicações web** (como sites, APIs e sistemas online). Ele atua **no nível da aplicação HTTP/HTTPS**, diferente do firewall tradicional que atua no nível da rede.

### Para que serve:

- Detectar e bloquear ataques como:
    
    - SQL Injection
        
    - Cross-Site Scripting (XSS)
        
    - Command Injection
        
    - LFI/RFI (inclusão de arquivos)
        
    - Bypass de autenticação
        
- Proteger formulários, sessões, cookies e APIs
    

---

## Diferença entre Firewall e WAF

|Firewall Tradicional|Web Application Firewall (WAF)|
|---|---|
|Atua na camada de rede|Atua na camada da aplicação (HTTP/HTTPS)|
|Bloqueia por IP, porta|Bloqueia por conteúdo da requisição|
|Protege a infraestrutura|Protege o sistema web e os dados do app|

---

## Esquema visual do funcionamento

```
[ Navegador / Atacante ]
           |
           v
   +------------------+
   |     FIREWALL     |   (bloqueia IPs, portas, protocolos)
   +------------------+
           |
           v
      +-----------+
      |    WAF    |     (analisa requisições HTTP)
      +-----------+
           |
           v
     [ Aplicação Web ]
```

---

## Exemplo prático:

### Requisição maliciosa:

```
GET /login.php?user=admin' OR '1'='1
```

- **Firewall tradicional** não detecta nada errado, pois é apenas uma string em HTTP.
    
- **WAF** analisa o conteúdo e identifica uma tentativa de **SQL Injection**.
    
- Resultado: a requisição é **bloqueada pelo WAF**.
    

---

## Ferramentas populares de WAF

- ModSecurity (Apache/Nginx)
    
- Cloudflare WAF
    
- AWS WAF
    
- Imperva
    
- F5 Advanced WAF
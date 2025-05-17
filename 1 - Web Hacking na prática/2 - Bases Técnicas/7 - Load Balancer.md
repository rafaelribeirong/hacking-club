## O que é um Load Balancer?

Um **load balancer** (balanceador de carga) é um componente de infraestrutura que **distribui o tráfego de rede** (requisições dos usuários) entre vários **servidores** de aplicação ou web. O objetivo é **otimizar o desempenho, aumentar a disponibilidade e garantir a continuidade do serviço** mesmo que um dos servidores falhe.

---

### Como funciona um Load Balancer?

Imagine que 1 milhão de usuários estão tentando acessar o site da Amazon. Se todo esse tráfego for direcionado para um único servidor, ele pode ficar **sobrecarregado** e cair. O load balancer evita isso.

Ele funciona como uma "recepcionista" que fica na frente de vários servidores e toma decisões como:

- Para qual servidor enviar cada nova requisição.
    
- Se um servidor estiver fora do ar, redirecionar o tráfego para outro.
    
- Monitorar a saúde dos servidores (health checks).
    

---

### Principais tipos de Load Balancing

1. **Round Robin** – distribui as requisições de forma sequencial entre os servidores.
    
2. **Least Connections** – direciona para o servidor com menos conexões ativas.
    
3. **IP Hash** – usa o IP do cliente para decidir o servidor (útil para manter sessões).
    
4. **Health Checks** – se um servidor falhar, ele é removido temporariamente da fila.
    

---

### Exemplo prático: Amazon

A Amazon, como outros grandes sites, **não roda em um único servidor**. Ela tem **múltiplas réplicas do mesmo site** (cópias da aplicação web) rodando em diferentes servidores espalhados globalmente (inclusive em diferentes regiões geográficas).

#### O que acontece se um servidor da Amazon cair?

- O load balancer detecta que aquele servidor **parou de responder**.
    
- Ele **remove automaticamente esse servidor** da lista de distribuição.
    
- As novas requisições dos usuários são encaminhadas para os **servidores saudáveis**.
    
- O usuário final **nem percebe** que houve falha.
    

Isso garante **alta disponibilidade** e **resiliência** da aplicação.

---

### Por que isso é importante para Pentest?

Como pentester, entender o funcionamento de um load balancer te ajuda a:

- **Identificar variações** de comportamento entre réplicas de servidores.
    
- Explorar **inconsistências** entre versões de aplicação se nem todos os servidores estão atualizados.
    
- Detectar **falhas de configuração** em réplicas diferentes (ex: headers de segurança ausentes em apenas um).
    
- Usar ferramentas com suporte a múltiplas requisições para tentar mapear os diferentes servidores atrás do balanceador.
    
- Avaliar se o load balancer está revelando informações sensíveis, como **respostas diferentes em erros 500**, redirecionamentos, ou **headers distintos**.
    

---

### Ferramentas que ajudam no mapeamento

- `ffuf`, `dirsearch`, `Burp Suite` com opções de repetição.
    
- Comparação de headers HTTP (ex: usando `curl -I`) em várias requisições.
    
- Ataques de **race condition** podem ter comportamentos diferentes dependendo da instância que processa o request.
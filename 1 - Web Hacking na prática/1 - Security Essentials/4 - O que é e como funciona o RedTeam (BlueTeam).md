# Círculo Virtuoso na Segurança da Informação

O conceito de _Círculo Virtuoso_ na segurança da informação envolve dois times distintos que atuam de forma sinérgica:

- **Blue Team:**  
    Responsável por criar, monitorar e manter sistemas de detecção e proteção contra ameaças cibernéticas.
    
- **Red Team:**  
    Responsável por simular ataques para burlar os controles e defesas implementados pelo Blue Team, fornecendo feedback sobre falhas e melhorias necessárias.
    

> **Nota:** O Red Team existe justamente porque o Blue Team existe — sua função é validar e desafiar as defesas estabelecidas.

---

# Red Team

O Red Team desempenha o papel de adversário (inimigo ou competidor), avaliando a segurança sob a perspectiva de quem tenta comprometer o ambiente.

É utilizado em diversos setores, como:

- Segurança cibernética
    
- Segurança de aeroportos
    
- Forças armadas
    
- Agências de inteligência
    

O objetivo principal é identificar vulnerabilidades reais que possam ser exploradas e testar a eficácia dos sistemas de defesa.

---

# Atividades Comuns do Red Team

1. **Testes de Controles de Acesso:**
    
    - Clonagem de cartões de acesso
        
    - Burlar segurança física
        
    - Ataques internos (_Insider Threat_)
        
    - Ataques de _Tailgating_ (entrada não autorizada aproveitando distrações)
        
2. **Ataques em Redes Wireless:**
    
    - Criação de redes Wi-Fi falsas (_Rogue AP_, _Evil Twin_)
        
    - Execução de ataques de engenharia social em redes wireless
        
3. **Ataques Leste-Oeste:**  
    (Movimentação lateral dentro da rede)
    
    - Ataques de camadas 3 e 4 (L3/L4), como _Man-in-the-Middle_
        
    - Testes de movimentação lateral
        
    - Avaliação de proteções internas (regras de firewall, IPS, SIEM)
        
4. **Bypass de Proteções:**
    
    - Uso de túneis DNS (_DNS tunneling_) ou ICMP para driblar regras
        
    - Bypass de firewalls e sistemas de prevenção de intrusões (IPS)
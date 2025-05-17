Metodologia Geral para Identificação de Ativos e Vulnerabilidades

> Utilizada entre pesquisadores de segurança para estruturar testes e identificação de falhas.

---

##  1. Recon

**Objetivo:**  
Identificar ativos de TI relacionados ao alvo.

### Atividades:

- Coleta de domínios e IPs.
    
- Ferramentas e técnicas OSINT.
    

---

## 2. Identificação e Análise

**Objetivo:**  
Mapear subdomínios, conteúdos e aplicações para avaliação de segurança.

### Etapas:

- **Scope e Out of Scope**
    
    - Definir o que será analisado.
        
    - Exemplo de escopo:
        
        - `*.starlink.com`
            
        - `*.starlinkisp.net`
            
        - `2620:134:b000::/40`
            
        - `192.31.243.0/26`
            
- **Subdomain Discovery**
    
    - Descobrir subdomínios relevantes.
        
    - Exemplo:
        
        - `sea-cgnat-158.starlinkisp.net`
            
        - `vpnserver.starlinkisp.net`
            
- **Content Discovery**
    
    - Encontrar arquivos e dados sensíveis.
        
    - Exemplo:
        
        - `https://www.starlink.com/environment.js`
            
- **Application Discovery**
    
    - Identificar softwares e aplicações de terceiros que possam estar desatualizados/vulneráveis.
        
- **Filtro e Validação**
    
    - Confirmar se os ativos estão ativos e operando (ex: checar servidores HTTP com **httpx**).
        

---

## 3. Exploração e PoC (Proof of Concept)

**Objetivo:**  
Identificar e validar vulnerabilidades.

### Atividades:

- **Vulnerability Assessment**
    
    - Processo geralmente manual no cenário Bug Bounty.
        
    - Baseado em guias como o **WSTG** (OWASP Web Security Testing Guide).
        
- **Validação e PoC**
    
    - Comprovação prática da vulnerabilidade encontrada.
        
    - Documentação de evidências (prints, vídeos, logs).
        

---

## 4. Report

**Objetivo:**  
Registrar todas as descobertas e recomendações de forma clara para o time ou cliente.

---

# Resumão das Fases

|Fase|Ação Principal|Exemplos/Ferramentas|
|:--|:--|:--|
|Recon|Identificar ativos|OSINT, Amass|
|Identificação e Análise|Mapear subdomínios, conteúdos, aplicações|Sublist3r, httpx|
|Exploração e PoC|Encontrar e validar vulnerabilidades|OWASP WSTG, provas de conceito|
|Report|Documentar resultados|Relatórios técnicos|

Aqui está o texto completo, com as etapas **Plan (Planejamento)** e **Code (Codificação)** claramente separadas e organizadas:

---

### **Anexos & Links**  
- [OWASP Proactive Controls](https://owasp.org/www-project-proactive-controls/)  
- [OWASP SAMM (Software Assurance Maturity Model)](https://owasp.org/www-project-samm/)  
- [OWASP Application Security Verification Standard (ASVS)](https://owasp.org/www-project-application-security-verification-standard/)  

---

### **13 - DevSecOps I**  
#### **Segurança de Aplicações**  

**Objetivo:** Entender como integrar segurança no desenvolvimento de aplicações (Web, APIs, Mobile).  

**Fluxo Tradicional (Passado):**  
1. Infra criava servidores físicos/VMs (manual).  
2. Devs enviavam o código para operação configurar.  
3. Processo lento e propenso a erros.  

**DevSecOps:** Cultura que automatiza e integra segurança em todas as etapas.  

---

### **Etapa 1: Plan (Planejamento)**  
**O que acontece:**  
- Análise de requisitos (funcionais/não funcionais).  
- Estudo de viabilidade (técnica/econômica).  
- Design da arquitetura (fluxo, componentes).  

**Ações de Segurança:**  
1. **Modelagem de Ameaças** (ex: metodologia STRIDE):  
   - Identificar riscos (ex: usuário acessar dados de outro).  
2. **Definir controles** (ex: autenticação, criptografia).  
3. **Documentar requisitos de segurança** (ex: "O sistema deve validar inputs contra SQL Injection").  

---

### **Etapa 2: Code (Codificação)**  
**O que acontece:**  
- Desenvolvedores escrevem o código.  
- Uso de **Version Control (Git/GitLab/GitHub)**.  
- Fluxo comum: **Git Flow** (branches como `main`, `develop`, `feature`).  

**Ações de Segurança:**  
1. **Pull Requests (PRs):**  
   - Código revisado antes de mesclar (evitar vulnerabilidades).  
2. **SAST (Static Application Security Testing):**  
   - Ferramentas analisam o código em busca de falhas (XSS, SQL Injection).  
   - Ideal: Rodar **durante o desenvolvimento** ou no PR.  

---

### **Fluxo DevOps com Segurança (Resumo)**  
```  
Plan → Code → Build → Test → Release → Deploy → Operate → Monitor  
↑ (Segurança em TODAS as etapas)  
```  

**Exemplo Prático:**  
- Na etapa **Plan**, exigir HTTPS no design.  
- Na etapa **Code**, usar SAST para bloquear PRs com código vulnerável.  

--- 

### **Observações Finais**  
- **Quanto antes a segurança for inserida, menor o custo de correção.**  
- Ferramentas como OWASP ASVS e SAMM ajudam a padronizar práticas seguras.  


---

#### **A. Plan (Planejamento)**

**Objetivo:** Definir requisitos e arquitetura com segurança embutida.  
**Atividades:**

- **Análise de Requisitos de Segurança:**
    
    - Exemplo: "O sistema deve seguir OWASP ASVS nível 2 para proteção contra injeções."
        
- **Modelagem de Ameaças (STRIDE):**
    
    - **S**poofing (autenticação), **T**ampering (integridade), **R**epudiation (logs), **I**nformation Disclosure (vazamento), **D**enial of Service (disponibilidade), **E**levation of Privilege (autorização).
        
- **Frameworks de Referência:**
    
    - OWASP SAMM (para maturidade em segurança).
        

**Saídas:**

- Documento de arquitetura segura.
    
- Políticas de segurança (ex: "Todos os dados devem ser criptografados em trânsito e em repouso").
    

---

#### **B. Code (Codificação)**

**Objetivo:** Garantir que o código seja seguro desde a escrita.  
**Práticas:**

- **Git Flow + Pull Requests (PRs):**
    
    - Branch `main` protegida; mudanças exigem revisão.
        
- **Revisão de Código (Code Review):**
    
    - Checklist de segurança (ex: "Validar inputs, evitar hardcoded secrets").
        
- **Ferramentas SAST:**
    
    - Exemplos: SonarQube, Checkmarx, Semgrep.
        
    - Identificam vulnerabilidades (XSS, SQLi) **durante o desenvolvimento**.
        

**Novos Tópicos:**

- **Secrets Management:**
    
    - Uso de ferramentas como HashiCorp Vault ou AWS Secrets Manager para evitar credenciais no código.
        
- **Padrões de Codificação Segura:**
    
    - OWASP Proactive Controls (ex: "Use parameterized queries para evitar SQLi").
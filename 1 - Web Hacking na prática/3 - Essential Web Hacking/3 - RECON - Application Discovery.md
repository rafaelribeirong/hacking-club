### **Application Discovery Workflow**  
*(Unificação de ferramentas para reconhecimento completo)*  

#### **1. Enumeração de Subdomínios**  
```bash
subfinder -d exemplo.com -silent -o subdomains.txt
```

#### **2. Filtragem de Subdomínios Ativos (HTTP/HTTPS)**  
```bash
cat subdomains.txt | httpx -status-code -silent -o active_subdomains.txt
```
*Opções úteis:*  
- `-ports 80,443,8080,8443` (testar portas específicas)  
- `-path /admin` (verificar paths específicos)  

#### **3. Descoberta de URLs (GAU + Hakrawler)**  
```bash
cat active_subdomains.txt | gau | sort -u > urls.txt
```
*Ou com Hakrawler:*  
```bash
cat active_subdomains.txt | hakrawler -subs -u > urls.txt
```

#### **4. Descoberta de Parâmetros (ParamSpider + GF/Arjun)**  
```bash
python3 paramspider.py -d exemplo.com -o params.txt
```
*Filtro com GF para padrões interessantes:*  
```bash
cat urls.txt | gf xss | tee xss_params.txt
```

#### **5. Content Discovery (HTTPX + SecretFinder)**  
```bash
cat urls.txt | httpx -silent -status-code -title -tech-detect -o content_scan.txt
```
*Busca por segredos/arquivos:*  
```bash
secretfinder -i https://exemplo.com -e -o secrets.txt
```

---

### **Comando Unificado (Pipeline Poderosa)**  
```bash
subfinder -d exemplo.com -silent | httpx -silent -ports 80,443 | gau | tee urls.txt | paramspider -q | gf xss | tee final_urls_params.txt
```

---

### **Ferramentas por Categoria**  
| **Categoria**          | **Ferramentas**                | **Função**                          |  
|-------------------------|--------------------------------|-------------------------------------|  
| **Subdomínios**         | SubFinder, Amass               | Enumeração de subdomínios           |  
| **URL Discovery**       | GAU, Hakrawler, Waybackurls    | Coleta de URLs históricas/ativas    |  
| **Parameter Discovery** | ParamSpider, Arjun, GF         | Extração de parâmetros dinâmicos    |  
| **Content Discovery**   | HTTPX, Aquatone, SecretFinder  | Análise de conteúdo/tecnologias     |  

---

### **Dicas Extras**  
- **Parallelização**: Use `interlace` para acelerar scans:  
  ```bash
  cat subdomains.txt | interlace -tL -threads 50 "httpx -title -status-code -silent -url _target_" -o results.txt
  ```  
- **Validação**: Sempre filtre resultados com `sort -u` para evitar duplicatas.  
- **Organização**: Salve saídas em pastas por alvo/data (ex: `exemplo.com/scan_20240508/`).  

---

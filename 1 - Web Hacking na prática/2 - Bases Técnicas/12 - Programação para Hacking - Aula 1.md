### Por que programação é importante no hacking?

- A programação é a base técnica para entender como aplicações funcionam.
    
- Sem conhecimento de código, o pentester fica limitado a ferramentas automáticas.
    
- Conhecendo código, você consegue:
    
    - Escrever seus próprios scripts de automação
        
    - Explorar falhas como XSS, SQL Injection, RCE, entre outras
        
    - Entender o que acontece no back-end de aplicações web
        

---

### Como uma infraestrutura funciona?

- Servidores, redes, banco de dados, aplicações web e APIs fazem parte da arquitetura.
    
- Para atacar ou defender, é necessário entender a estrutura completa.
    

---

### Exemplo: por que saber JavaScript?

- Vulnerabilidades como **XSS (Cross-site Scripting)** exigem conhecimento de JavaScript para explorar ou se defender.
    
- Muitas páginas web interagem dinamicamente com o usuário usando scripts do lado do cliente.
    

---

## Roteiro de estudo (linha de aprendizado sugerida)

### Fundamentos:

1. **Arquitetura de computadores**
    
    - CPU, memória, I/O, como programas são executados
        
2. **Sistemas Operacionais**
    
    - Foco em Linux (bash, permissões, processos, redes)
        
3. **Redes**
    
    - Modelo OSI, TCP/IP, HTTP, DNS, DHCP
        
4. **Protocolos Web**
    
    - HTTP/HTTPS, cookies, headers, sessões
        

### Linguagens de Programação úteis:

- **C**: Baixo nível, ideal para exploits, buffer overflow
    
- **Python**: Scripts de automação, integração com ferramentas de pentest
    
- **JavaScript**: Essencial para XSS, manipulação de páginas web
    
- **PHP**: Ainda amplamente usado em servidores web; importante para identificar falhas em aplicações legadas
    
- **Bash**: Shell scripts no Linux, automações e interações diretas com o sistema
    
- **PowerShell**: Para ambientes Windows
    

---

### Como o código é interpretado?

- **Navegador (cliente)**:
    
    - Interpreta HTML, CSS e JavaScript
        
- **Servidor**:
    
    - Recebe requisições e processa linguagens como PHP, Python (Django/Flask), Node.js (JavaScript), C#, etc.
        

**Exemplo prático:**

> O navegador envia uma requisição HTTP para `https://site.com/login`  
> O servidor interpreta os dados, processa o login em PHP (ou outra linguagem), e responde com HTML renderizado
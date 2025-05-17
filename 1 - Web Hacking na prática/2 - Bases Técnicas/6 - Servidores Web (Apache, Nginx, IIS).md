## Servidores Web: Nginx e Apache

Existem diversos servidores web utilizados no mundo todo, mas neste material vamos focar nos **dois mais populares**: **Nginx** e **Apache**. Vamos entender como eles funcionam, como interpretam linguagens de programação, APIs, como são configurados, onde ficam os arquivos importantes e outras informações relevantes para profissionais de cibersegurança e pentest.

---

### Nginx

#### O que é o Nginx?

O **Nginx** (pronuncia-se "engine-x") é um servidor web leve, de alto desempenho, amplamente utilizado como servidor HTTP, proxy reverso, balanceador de carga e proxy de e-mail (IMAP/POP3).

Ele é conhecido pela alta performance, uso eficiente de recursos e grande capacidade de lidar com muitas conexões simultâneas (ideal para aplicações modernas).

#### Como o Nginx funciona?

- Trabalha com arquitetura orientada a eventos (event-driven), ao contrário do Apache que utiliza processos ou threads.
    
- Ideal para servir conteúdo estático (como imagens, HTML, CSS).
    
- Pode encaminhar requisições dinâmicas (como PHP, Python) para outros serviços como o PHP-FPM, FastCGI, etc.
    
- Suporta múltiplos hosts virtuais (virtual hosts) para atender diferentes domínios no mesmo servidor.
    

#### Onde ficam as configurações?

- O arquivo principal de configuração geralmente está em:
    
    ```
    /etc/nginx/nginx.conf
    ```
    
- As configurações de sites são armazenadas em:
    
    ```
    /etc/nginx/sites-available/
    ```
    
    E ativadas via link simbólico em:
    
    ```
    /etc/nginx/sites-enabled/
    ```
    

#### Onde acessar os logs?

- Logs de acesso:
    
    ```
    /var/log/nginx/access.log
    ```
    
- Logs de erro:
    
    ```
    /var/log/nginx/error.log
    ```
    

#### O que mais é importante saber sobre o Nginx?

- Suporta SSL/TLS, headers de segurança, rate limiting e outras proteções.
    
- Pode ser usado como WAF (Firewall de Aplicações Web) com ferramentas como o ModSecurity integrado via Nginx.
    
- Excelente para atuar como proxy reverso, protegendo servidores de aplicação e melhorando a escalabilidade.
    

---

### Apache HTTP Server

#### O que é o Apache?

O **Apache HTTP Server**, também conhecido como **httpd**, é um dos servidores web mais antigos e utilizados no mundo. Ele é altamente modular e configurável, funcionando bem tanto para aplicações simples quanto complexas.

#### Como o Apache funciona?

- Usa modelo baseado em processos ou threads para tratar requisições.
    
- Suporta módulos dinâmicos para lidar com diferentes linguagens e recursos (como `mod_php`, `mod_ssl`, etc).
    
- Ideal para servidores que executam scripts PHP, Perl ou Python com suporte nativo.
    

#### Onde ficam as configurações?

- Arquivo principal:
    
    ```
    /etc/httpd/httpd.conf  (CentOS/RedHat)
    /etc/apache2/apache2.conf  (Debian/Ubuntu)
    ```
    
- Configuração de sites:
    
    ```
    /etc/apache2/sites-available/
    /etc/apache2/sites-enabled/
    ```
    

#### Onde acessar os logs?

- Logs de acesso:
    
    ```
    /var/log/apache2/access.log
    ```
    
- Logs de erro:
    
    ```
    /var/log/apache2/error.log
    ```
    

#### ModSecurity – WAF no Apache

- ModSecurity é um módulo de firewall de aplicação web (WAF) amplamente utilizado com o Apache.
    
- Permite definir regras de segurança personalizadas para proteger contra SQL Injection, XSS, e outras vulnerabilidades comuns.
    
- É altamente configurável e pode trabalhar com conjuntos de regras prontos, como o OWASP Core Rule Set (CRS).
    
- Arquivo de configuração (pode variar):
    
    ```
    /etc/modsecurity/modsecurity.conf
    ```
    

---

### Como interpretam linguagens de programação e APIs?

Tanto o Nginx quanto o Apache não executam código PHP, Python, etc., diretamente. Eles:

- Encaminham as requisições para interpretadores ou motores externos, como:
    
    - `php-fpm` (PHP)
        
    - `uwsgi` (Python)
        
    - `gunicorn` (Python)
        
    - FastCGI
        
- Para APIs REST, eles recebem a requisição HTTP, e repassam ao backend da aplicação que a interpreta e responde (geralmente uma aplicação web em Flask, Django, Node.js, Laravel etc).
 
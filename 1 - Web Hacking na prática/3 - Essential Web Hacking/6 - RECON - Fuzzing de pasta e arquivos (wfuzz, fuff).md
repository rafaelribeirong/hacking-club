## O que é FUZZING?

**Fuzzing** é uma técnica de segurança usada para descobrir arquivos, diretórios, parâmetros e outras partes ocultas de uma aplicação web. A ideia é enviar várias requisições diferentes para a aplicação com o objetivo de encontrar algo que normalmente não é visível ou acessível ao usuário comum.

### Exemplos do que o fuzzing pode encontrar:

- Arquivos como `backup.zip`, `config.php`, `admin.php`
    
- Diretórios como `/admin`, `/old-site`, `/test`
    
- Parâmetros ocultos em URLs como `?id=`, `?file=`, `?lang=`
    

## Ferramenta: Wfuzz

Uma das ferramentas mais utilizadas para fuzzing é o **Wfuzz**. Ela permite automatizar o envio dessas requisições para descobrir o que está escondido no servidor.

---

## Comando básico para fuzzing de diretórios ou arquivos:

```bash
wfuzz -c -z file,wordlist.txt --hc 404 http://site.com/FUZZ
```

### Explicação do comando:

- `-c`: ativa cores no terminal, deixando a leitura mais fácil
    
- `-z file,wordlist.txt`: indica que vai usar uma wordlist de palavras (por exemplo, nomes de arquivos ou diretórios)
    
- `--hc 404`: ignora as respostas que retornam o código 404 (Not Found), ou seja, só mostra as que existem
    
- `http://site.com/FUZZ`: o `FUZZ` é o ponto onde cada palavra da wordlist será testada
    

---

## Teste baseado em **range** (por exemplo, para testar parâmetros numéricos):

```bash
wfuzz -c -z range,1-100 http://site.com/page.php?id=FUZZ
```

Esse comando testa os valores de `id` de 1 até 100.

---

## Tornar o fuzzing mais rápido: usando múltiplas threads

Você pode usar o parâmetro `-t` para definir o número de threads (requisições simultâneas). Exemplo:

```bash
wfuzz -c -t 20 -z file,wordlist.txt --hc 404 http://site.com/FUZZ
```

Neste exemplo, o Wfuzz usará 20 threads para acelerar o processo.

---

## Wordlists recomendadas

Uma das coleções de wordlists mais recomendadas para fuzzing é a **SecLists**, que pode ser clonada do GitHub:

```bash
git clone https://github.com/danielmiessler/SecLists.git
```

Dentro dela, existe uma pasta chamada `Discovery/Web-Content/` com várias listas:

- `raft-small-words.txt`
    
- `raft-large-directories.txt`
    
- `common.txt`
    

As listas _Raft_ são excelentes, com nomes em minúsculo, diferentes tamanhos e focadas em arquivos e diretórios web.

---
Perfeito, Rafael! Aqui está o conteúdo revisado, **sem o uso de emojis**:

---

## PortScan – Como funciona?

O **PortScan** (varredura de portas) é uma técnica usada para descobrir **quais portas estão abertas em um servidor**. Isso é importante porque portas abertas indicam que há serviços em execução — o que pode representar oportunidades ou riscos, dependendo do contexto.

---

## Ferramenta utilizada: **Nmap**

O `nmap` é uma das ferramentas mais conhecidas para realizar esse tipo de varredura. Ele permite identificar:

- Portas abertas
    
- Serviços que estão rodando nessas portas
    
- Versões dos serviços
    
- Detecção de sistema operacional
    
- Scripts para enumeração mais profunda
    

---

### Nmap
### Exemplo 1: Scan básico

```bash
nmap 192.168.0.0 -v
```

Esse comando realiza um escaneamento simples nas portas padrão do alvo, mostrando resultados detalhados (`-v` para verbose).

---

### Exemplo 2: Scan sem enviar pacotes ICMP

Em cenários com **firewall**, é comum que pacotes ICMP (ping) sejam bloqueados. Por isso, podemos desabilitá-los:

```bash
nmap -Pn -sV 192.168.0.0 -vv
```

- `-Pn`: não envia pacotes ICMP (ignora a etapa de descoberta do host)
    
- `-sV`: tenta descobrir a versão dos serviços rodando nas portas
    
- `-vv`: saída mais detalhada
    

---

### Exemplo 3: Scan com enumeração profunda

Esse comando faz tudo: descobre portas, versões dos serviços e executa scripts de enumeração automática:

```bash
nmap -Pn -sV -A 192.168.0.0 -vv
```

- `-A`: ativa uma varredura completa, incluindo:
    
    - Detecção de sistema operacional
        
    - Script scanning com NSE
        
    - Traceroute
        

---

### Revisando

- Um port scan ajuda a mapear os serviços expostos em um alvo
    
- O `nmap` é a ferramenta mais usada para isso
    
- É possível fazer scans simples ou avançados, dependendo do seu objetivo
    
- Em ambientes protegidos, é comum usar o `-Pn` para contornar bloqueios de ICMP
    

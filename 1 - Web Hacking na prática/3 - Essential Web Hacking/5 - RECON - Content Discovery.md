## Utilizando Aquatone com Subfinder e Httpx para Reconhecimento Visual

### O que é o Aquatone?

**Aquatone** é uma ferramenta de reconhecimento que coleta capturas de tela (screenshots) de sites, organizando os resultados em um relatório visual e interativo em HTML. Ela é especialmente útil durante o reconhecimento em **testes de invasão**, pois permite identificar de forma rápida e visual:

- Aplicações web ativas
    
- Similaridades entre páginas
    
- Tecnologias utilizadas
    
- Pontos interessantes para análise manual
    

> **Nota importante:** O Aquatone exige uma **interface gráfica** para funcionar corretamente, pois utiliza um navegador headless para capturar os screenshots. Isso significa que **não funcionará corretamente em uma VPS sem interface gráfica**.

---

### Fluxo de uso com Subfinder

Podemos integrar o **Subfinder** para descobrir subdomínios e, em seguida, utilizar o Aquatone para capturar as telas dos serviços encontrados:

#### Passo 1: Descobrir subdomínios com Subfinder

```bash
subfinder -d nubank.com.br -silent
```

O flag `-silent` remove informações extras e retorna apenas os subdomínios.

#### Passo 2: Enviar os subdomínios diretamente para o Aquatone

```bash
subfinder -d nubank.com.br -silent | ./aquatone
```

Isso faz com que o Aquatone colete os subdomínios e tente acessar os serviços HTTP/HTTPS em cada um, tirando screenshots e organizando o resultado.

#### Passo 3: Abrir o relatório no navegador

Após o processamento, o Aquatone gera um relatório na pasta `aquatone`:

```bash
firefox aquatone/reporting/aquatone_report.html
```

Esse relatório permite visualizar as páginas agrupadas por similaridade, facilitando a identificação de padrões, clones de sistemas, painéis administrativos e outras informações úteis.

---

### Complemento: Httpx

Para um fluxo mais controlado, você pode utilizar o **httpx** entre o `subfinder` e o `aquatone`, garantindo que apenas hosts que realmente possuem resposta HTTP/HTTPS sejam processados:

```bash
subfinder -d nubank.com.br -silent | httpx -silent | aquatone
```

---

### Por que isso é importante em um pentest?

- **Reconhecimento visual rápido:** Ajuda a mapear os serviços web ativos de forma mais intuitiva.
    
- **Identificação de superfícies de ataque:** Painéis de login, interfaces administrativas e sistemas expostos podem ser identificados rapidamente.
    
- **Agrupamento inteligente:** Aquatone agrupa páginas semelhantes, o que ajuda a reduzir ruído e focar em alvos únicos.
    
- **Tecnologias expostas:** É possível identificar frameworks, servidores e CMS utilizados nos sistemas.
    

---
A integração de **Subfinder**, **Httpx** e **Aquatone** é extremamente eficaz para a fase de **reconhecimento passivo e visual** em testes de intrusão. Automatizar esse fluxo proporciona agilidade e melhor compreensão da superfície de ataque disponível, principalmente em alvos com muitos subdomínios.


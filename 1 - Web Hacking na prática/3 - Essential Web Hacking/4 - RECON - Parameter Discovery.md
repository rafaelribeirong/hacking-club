## Descoberta de Parâmetros

A etapa de descoberta de parâmetros é essencial durante um pentest, especialmente ao buscar oportunidades de exploração como XSS (Cross-Site Scripting). A seguir, destacamos algumas ferramentas úteis para essa finalidade:

- [ParamSpider](https://github.com/devanshbatham/ParamSpider)
    
- [kxss](https://github.com/tomnomnom/hacks/tree/master/kxss)
    
- [gf (Gf Patterns)](https://github.com/tomnomnom/gf)
    
- [Parth](https://github.com/s0md3v/Parth)
    
- [TomNomNom Hacks](https://github.com/tomnomnom/hacks)
    

### ParamSpider

O **ParamSpider** é uma ferramenta escrita em Python, usada para encontrar URLs conhecidas com parâmetros em um domínio e seus subdomínios. Antes de utilizá-la, é necessário instalar as dependências listadas no arquivo `requirements.txt`:

```bash
pip install -r requirements.txt
```

#### Exemplo de uso básico:

```bash
python3 paramspider.py -d nubak.com.br
```

Esse comando coleta automaticamente URLs com parâmetros dentro do domínio `nubak.com.br` e seus subdomínios.

É possível também excluir subdomínios específicos com a flag `-e`:

```bash
python3 paramspider.py -d nubak.com.br -e admin.nubak.com.br
```

O destaque do ParamSpider é sua capacidade de revelar uma grande quantidade de parâmetros, que podem ser usados para testes de fuzzing ou validação de vulnerabilidades.

---

### Verificando Parâmetros Refletidos com kxss

Após obter as URLs com parâmetros, podemos verificar se algum desses parâmetros é refletido na resposta utilizando o **kxss**:

```bash
python3 paramspider.py -d nubak.com.br | kxss
```

O `kxss` irá analisar cada URL e identificar quais parâmetros são refletidos na resposta. Isso é extremamente importante porque um parâmetro refletido pode indicar uma **superfície de ataque para XSS**.

#### Por que isso é importante?

Se conseguimos refletir uma string como `"><`, por exemplo, é possível injetar um HTML ou JavaScript malicioso na página:

```html
"><script>alert('XSS')</script>
```

Esse tipo de vulnerabilidade pode permitir ao atacante executar scripts arbitrários no navegador da vítima, resultando em roubo de cookies, sequestro de sessão e outras ações maliciosas.

---

O uso combinado de ferramentas como **ParamSpider** e **kxss** automatiza a descoberta e verificação de parâmetros refletidos, acelerando a identificação de possíveis vetores de **Cross-Site Scripting**. Essa abordagem é essencial em avaliações de segurança focadas em aplicações web.

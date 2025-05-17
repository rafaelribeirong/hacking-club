# OWASP Test Guide e Checklist de Testes

O **OWASP Web Security Testing Guide (WSTG)** é um dos materiais mais importantes para quem realiza testes de segurança em aplicações web.  
Ele é um guia completo que descreve metodologias, técnicas e boas práticas para testar a segurança de sistemas.

Além do guia em si, a OWASP também disponibiliza uma **planilha de checklist**, onde você pode acompanhar e registrar a realização de cada teste de segurança proposto.  
Essa planilha é extremamente útil para:

- Organizar os testes realizados.
    
- Garantir que nenhum aspecto importante seja esquecido.
    
- Ajudar no planejamento e rastreabilidade dos testes.
    
- Padronizar avaliações de segurança em diferentes projetos.
    

Cada linha da planilha representa um teste específico, como "Testar injeção SQL", "Testar controle de acesso em APIs", "Testar falhas de autenticação", entre outros.

---

# OWASP Zed Attack Proxy (ZAP)

O **OWASP ZAP** (Zed Attack Proxy) é uma ferramenta gratuita e de código aberto desenvolvida pela OWASP para encontrar vulnerabilidades de segurança em aplicações web.

**Principais características do ZAP:**

- **Interceptação de tráfego**: você pode capturar e modificar requisições HTTP/S feitas entre o navegador e o servidor.
    
- **Spider**: explora automaticamente os links do site para mapear toda a aplicação.
    
- **Active Scan**: executa ataques ativos para encontrar vulnerabilidades conhecidas.
    
- **Passive Scan**: analisa o tráfego sem modificar as requisições, identificando falhas de segurança passivas.
    
- **Fuzzing**: injeta várias cargas de teste em campos de entrada para identificar vulnerabilidades.
    

ZAP é indicado tanto para quem está começando quanto para quem é profissional de segurança ofensiva.

---

# Como Atacar Um Único Parâmetro no ZAP

Quando você identifica um **parâmetro vulnerável** (por exemplo, `id` em `https://site.com/item?id=5`), você pode focar seus testes apenas nesse campo.

**Passos básicos para atacar um único parâmetro:**

1. **Capturar a requisição** usando a função "Manual Request Editor" ou "Request Editor" (Interceptação ou History).
    
2. **Editar manualmente o valor do parâmetro** e inserir payloads de ataque, como SQL Injection (`' OR '1'='1`) ou XSS (`<script>alert(1)</script>`).
    
3. **Fazer um Fuzzing só no parâmetro**:
    
    - Botão direito na requisição ➔ **Attack ➔ Fuzz**.
        
    - No campo do parâmetro, insira diferentes payloads pré-configurados (como injeções de SQL, XSS, LFI, etc.).
        
    - Rodar o ataque e analisar o comportamento da resposta.
        

Assim, você ataca só o que realmente importa, sem desperdiçar tempo ou recursos.

---

# Automatizando Ataques no ZAP - "Automated Scan"

No modo **Automated Scan**, o ZAP:

- Faz o Spider para descobrir todas as páginas e parâmetros do site.
    
- Roda o Active Scan automaticamente, atacando tudo que encontrar.
    

**Importante:**  
É uma maneira rápida de fazer um teste geral, mas pode gerar falso positivo ou deixar passar falhas específicas que exigiriam análise manual.

Dica: o Automated Scan é excelente para obter uma visão inicial do que pode estar vulnerável.

---

# Plugins e Extensões no ZAP

O ZAP possui uma loja de plugins integrada chamada **ZAP Marketplace**, onde você pode adicionar funcionalidades extras.

**Exemplos de plugins que você pode testar:**

- **Scripts**: criar scripts personalizados para exploração ou automação de testes.
    
- **AJAX Spider**: melhor exploração de sites dinâmicos (SPA, aplicações modernas em JavaScript).
    
- **Passive Scanner Rules**: mais regras para o escaneamento passivo.
    
- **Active Scanner Rules**: adiciona mais ataques automatizados no Active Scan.
    
- **Forced Browse**: tenta encontrar arquivos e diretórios ocultos na aplicação.
    
- **WS (WebSockets)**: análise de segurança em comunicação WebSocket.
    

Adicionar plugins melhora bastante a capacidade de identificação de vulnerabilidades e aumenta o nível dos seus testes.

---

# Resumo

|Item|Descrição|
|:-:|---|
|OWASP Test Guide|Guia de metodologias de teste de segurança web + checklist|
|ZAP|Ferramenta gratuita para pentest web|
|Teste de Parâmetro Único|Fuzzing e ataques focados em um parâmetro específico|
|Automated Scan|Spider + Active Scan automáticos|
|Plugins|Extensões para novos tipos de testes e técnicas|

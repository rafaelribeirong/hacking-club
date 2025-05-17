## O que é Code Review de Segurança?

Code review de segurança é o processo de analisar sistematicamente o código-fonte de um aplicativo para identificar vulnerabilidades potenciais antes que sejam exploradas. Diferente de testes funcionais, foca especificamente em problemas de segurança.

## Principais Tópicos para Analisar

### 1. Injeção (SQL, OS, LDAP, etc.)
- **Procurar por**: Concatenção direta de strings em consultas
- **Exemplo perigoso**: 
  ```python
  query = "SELECT * FROM users WHERE username = '" + user_input + "'"
  ```
- **Solução**: Usar prepared statements/parameterized queries

### 2. Autenticação e Gerenciamento de Sessão
- Senhas armazenadas em plain text
- Falta de proteção contra brute force
- Tokens de sessão inseguros

### 3. Cross-Site Scripting (XSS)
- **Procurar por**: Saída não sanitizada de user input
- **Exemplo perigoso**:
  ```javascript
  document.write('<div>' + userComment + '</div>');
  ```
- **Solução**: Sanitização/escaping de output

### 4. Comparações Inseguras
- **Procurar por**: == em vez de === em JavaScript
- Comparação de hash de senhas sem função segura

### 5. Validação de Input
- Falta de validação de tipos, formato, comprimento
- Aceitação de input malicioso (../ em paths)

### 6. Gerenciamento de Erros
- Mensagens de erro que vazam informações sensíveis
- Falta de tratamento adequado de exceções

## Técnicas de Análise

### 1. Análise Estática
- **Ferramentas**: SonarQube, Checkmarx, Fortify
- **Vantagens**: Automatizada, cobre todo o código
- **Limitações**: Falsos positivos/negativos

### 2. Análise Manual
- **Técnicas**:
  - Seguir o fluxo de dados (data flow analysis)
  - Análise reversa a partir de pontos de entrada
  - Busca por padrões conhecidos (grepping)

### 3. Bootloader/Bootstrap Analysis
- Verificar:
  - Carregamento seguro de classes
  - Rotas inicializadas corretamente
  - Models com validações adequadas
  - Inicialização de serviços críticos

## Ferramentas Úteis

1. **SonarQube**: [https://www.sonarqube.org/](https://www.sonarqube.org/)
2. **OWASP Code Review Guide**: [https://owasp.org/www-project-code-review-guide/](https://owasp.org/www-project-code-review-guide/)
3. **Semgrep**: [https://semgrep.dev/](https://semgrep.dev/)
4. **Bandit** (Python): [https://bandit.readthedocs.io/](https://bandit.readthedocs.io/)

## Checklist para Code Review

1. [ ] Todas as entradas de usuário são validadas?
2. [ ] Consultas ao banco usam parameterized queries?
3. [ ] Outputs são sanitizados contra XSS?
4. [ ] Configurações sensíveis estão hardcoded?
5. [ ] Existem verificações de autorização adequadas?
6. [ ] Erros são tratados sem vazar informações?
7. [ ] Dependências estão atualizadas?
8. [ ] Existem credenciais no código ou versionamento?

## Fluxo Recomendado

1. Identificar todos os pontos de entrada (APIs, forms, etc.)
2. Mapear o fluxo de dados através do sistema
3. Verificar cada transformação/processamento do dado
4. Identificar onde dados não confiáveis podem afetar componentes críticos
5. Documentar todas as descobertas e recomendações

Lembre-se: Code review de segurança deve ser parte do processo de desenvolvimento, não apenas uma atividade pontual.
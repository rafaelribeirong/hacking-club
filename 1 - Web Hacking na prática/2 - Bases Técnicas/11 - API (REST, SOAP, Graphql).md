## API (REST, SOAP, GraphQL)

Quando usamos um aplicativo no celular, como o WhatsApp ou o Uber, ele se comunica com servidores para enviar e receber informações. Essa comunicação acontece através de **APIs** (Application Programming Interfaces). O mesmo ocorre com aplicações web — o **front-end** conversa com o **back-end** por meio de APIs.

Durante o pentest, entender como essas APIs funcionam é essencial para identificar falhas como:

- Quebra de autenticação
    
- Controle de acesso inadequado
    
- Injeção de comandos (ex: SQLi, XXE, etc.)
    

---

## REST (Representational State Transfer)

É o padrão de API mais usado atualmente, baseado em **requisições HTTP** como `GET`, `POST`, `PUT` e `DELETE`.

### Exemplo prático de API REST:

**Listar usuários**

```
GET https://api.crowsec.com.br/users
```

**Listar usuário com ID 1**

```
GET https://api.crowsec.com.br/users/1
```

**Criar um novo usuário**

```
POST https://api.crowsec.com.br/users
Payload (corpo da requisição):
{
  "name": "Carlos",
  "password": "senha@123"
}
```

**Atualizar um usuário existente**

```
PUT https://api.crowsec.com.br/users/1
Payload:
{
  "name": "Carlos Silva"
}
```

**Excluir um usuário**

```
DELETE https://api.crowsec.com.br/users/1
```

---

## GraphQL

GraphQL é uma **linguagem de consulta para APIs** desenvolvida pelo Facebook em 2012. Ao contrário do REST, onde cada endpoint retorna dados fixos, o GraphQL permite ao cliente **especificar exatamente quais dados deseja**.

### Características:

- Usualmente usa **requisições POST**.
    
- O cliente envia uma **query** dizendo o que quer.
    
- Menos requisições para obter vários dados.
    

### Exemplo prático:

**Requisição para listar nome e e-mail dos usuários:**

```
POST https://api.crowsec.com.br/graphql
Payload:
{
  "query": "{ users { name email } }"
}
```

**Resposta:**

```json
{
  "data": {
    "users": [
      { "name": "Carlos", "email": "carlos@exemplo.com" },
      { "name": "João", "email": "joao@exemplo.com" }
    ]
  }
}
```

---

## SOAP (Simple Object Access Protocol)

Foi o padrão dominante no passado, muito usado em sistemas corporativos. Baseia-se em **XML** e também usa HTTP para enviar e receber dados.

### Estrutura de uma mensagem SOAP:

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header>
    <!-- Cabeçalho com autenticação, tokens, etc -->
  </soap:Header>
  <soap:Body>
    <!-- Conteúdo principal, como ações ou requisições -->
  </soap:Body>
</soap:Envelope>
```

### Componentes:

- **SOAP Envelope**: estrutura geral da mensagem
    
- **SOAP Header**: informações adicionais (ex: autenticação)
    
- **SOAP Body**: onde está o conteúdo da ação solicitada
    

---

## Por que isso importa no pentest?

Entender como as APIs funcionam te ajuda a:

- Criar requisições customizadas
    
- Simular manipulação de dados (ex: IDOR)
    
- Analisar tokens de sessão e autenticação
    
- Descobrir métodos vulneráveis expostos (ex: PUT aberto)
    
- Explorar GraphQL introspection se habilitado
    
- Analisar mensagens SOAP em busca de XXE, injeção de XML, etc.
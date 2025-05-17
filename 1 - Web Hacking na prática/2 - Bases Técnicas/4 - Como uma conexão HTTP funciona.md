# HTTP – HyperText Transfer Protocol

## Referências:

- [RFC 2616 – HTTP/1.1](https://datatracker.ietf.org/doc/html/rfc2616)
    
- [O que é HTTP – Cloudflare](https://www.cloudflare.com/pt-br/learning/ddos/glossary/hypertext-transfer-protocol-http/)
    
- [Como funciona a internet – Cloudflare](https://www.cloudflare.com/pt-br/learning/network-layer/how-does-the-internet-work/)
    

---

## O que é um protocolo?

Um **protocolo** é um conjunto de **regras e padrões pré-estabelecidos** que definem **como determinada comunicação deve ocorrer em uma rede**.  
Exemplos de protocolos:

- **TCP (Transmission Control Protocol)**: define como a conexão e a entrega confiável de dados deve acontecer.
    
- **IP (Internet Protocol)**: responsável pelo endereçamento e roteamento dos pacotes.
    
- **HTTP (HyperText Transfer Protocol)**: define como a comunicação entre **cliente e servidor web** deve acontecer.
    

**Importante:** os protocolos funcionam **em conjunto**. Por exemplo, o HTTP funciona sobre o TCP/IP, sem que um anule o outro.

---

## O que é RFC?

**RFC (Request for Comments)** é o documento que define como um protocolo deve funcionar.  
Cada protocolo, como o HTTP, tem uma RFC que descreve detalhadamente suas especificações.  
Para aprender HTTP, o ideal é começar pela **RFC 2616**, que trata da versão 1.1 do protocolo.

---

## Modelo Cliente/Servidor e P2P

- O **HTTP** segue o modelo **cliente-servidor**, onde o cliente faz uma requisição e o servidor responde com os dados.
    
- Já em um modelo **P2P (peer-to-peer)**, dois clientes se conectam diretamente, como no caso de compartilhamento de arquivos entre usuários.
    

---

## Estrutura da Requisição e Resposta HTTP

### Requisição HTTP:

```
METHOD URI VERSION
Header-Name: Header-Value
Header-Name: Header-Value

[CORPO - opcional]
```

Exemplo:

```
GET / HTTP/1.1
Host: www.google.com
User-Agent: Mozilla/5.0
```

### Resposta HTTP:

```
VERSION STATUS_CODE MESSAGE
Header-Name: Header-Value

[CORPO - conteúdo da resposta]
```

Exemplo:

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 3050
```

---

## Principais Métodos HTTP

- **GET**: utilizado para **consultar** dados do servidor. Exemplo: carregar uma página.
    
- **POST**: utilizado para **enviar dados** e criar novos recursos no servidor. Exemplo: enviar um formulário.
    
- **PUT**: utilizado para **atualizar** completamente um recurso.
    
- **PATCH**: utilizado para **atualizar parcialmente** um recurso.
    
- **DELETE**: utilizado para **remover** um recurso.
    
- **OPTIONS**: retorna quais métodos são permitidos no recurso. Usado com CORS.
    
- **TRACE**: retorna a requisição recebida pelo servidor. Usado para teste e diagnóstico.
    

---

## Principais Headers HTTP

- **Host**: nome do host sendo acessado (obrigatório em HTTP/1.1). Exemplo: `Host: www.exemplo.com`
    
- **Connection**: define se a conexão será encerrada ou mantida após a resposta.
    
    - `close`: encerra a conexão após a resposta.
        
    - `keep-alive`: mantém a conexão ativa para múltiplas requisições.
        
- **Content-Type**: informa o tipo de dado enviado. Exemplo: `application/json`, `text/html`.
    
- **Content-Length**: indica o tamanho do corpo da mensagem.
    
- **User-Agent**: identifica o agente (navegador, sistema, script) que está fazendo a requisição.  
    Exemplo: `Mozilla/5.0`, `curl/7.68.0`.
    

---

## Códigos de Status HTTP

### 1xx – Informativos

- **100 Continue**: o servidor recebeu parte da requisição e o cliente deve continuar.
    
- **101 Switching Protocols**: mudança de protocolo, como para WebSocket.
    

### 2xx – Sucesso

- **200 OK**: requisição bem-sucedida.
    
- **201 Created**: recurso criado com sucesso.
    
- **204 No Content**: requisição bem-sucedida, sem corpo de resposta.
    

### 3xx – Redirecionamento

- **301 Moved Permanently**: o recurso foi movido permanentemente.
    
- **302 Found (Temporary Redirect)**: redirecionamento temporário.
    

### 4xx – Erro do Cliente

- **400 Bad Request**: erro de sintaxe na requisição.
    
- **401 Unauthorized**: autenticação requerida.
    
- **403 Forbidden**: acesso negado.
    
- **404 Not Found**: recurso não encontrado.
    

### 5xx – Erro do Servidor

- **500 Internal Server Error**: erro inesperado no servidor.
    
- **502 Bad Gateway**: resposta inválida de outro servidor.
    
- **503 Service Unavailable**: serviço temporariamente indisponível.
    
- **522 Connection Timed Out** (Cloudflare): tempo limite esgotado na conexão com o servidor de origem.
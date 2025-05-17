Command injection > FALHA DE SEGURANÇA QUE AFETA SISTEMAS web
permite que executamos um comando dentro do sistema enviando uma requisição HTTP


criar um exemplo de um index.php de uma falha do pq acontece e existe essa vulnerabilidade
exemplo no ph se der um system('id);  system('pwd); ele roda o comando id

essa falha é mt comum acontecer em roteadores


---

Ligando a maquina 
entrar no ip da maquina

no campo de pesquisa por exemplo  fizemos pesquisa para o 8.8.8.8

deppois testei 

8.8.8.8;id

ele executou o comando podemos ver que ele retornou na tela

na caixinha tem limite de caracteres para digitar entao vamos em

especionar e clinamos na caixinha com setinha e colocamos pra especionar a caixa de digitar do site

tamanho minimo é 7 e maximo era 10

entao agora mudamos para conseguir digitar mais


entao mandamos agora 8.8.8.8;ls -la

litando a raiz
8.8.8.8;ls -la /

e achamos a flag 

8.8.8.8; cat /flag.txt
# microsservice-email

Projeto de estudo com dois microsserviços em **Java + Spring Boot** que se comunicam de forma assíncrona via **RabbitMQ**.

Quando um usuário é cadastrado, o sistema envia automaticamente um e-mail de boas-vindas para ele.

## Como funciona

```
Cliente ──POST /users──▶ [ ms-user ] ──mensagem──▶ RabbitMQ ──▶ [ ms-email ] ──SMTP──▶ Caixa de entrada
                             │                                       │
                         PostgreSQL                              PostgreSQL
```

1. O **ms-user** recebe o cadastro (nome e e-mail), valida os dados e salva o usuário no banco.
2. Em seguida, publica uma mensagem na fila `default.email` com os dados do e-mail de boas-vindas.
3. O **ms-email** consome essa mensagem, envia o e-mail via SMTP (Gmail) e registra o envio no banco com o status `SENT` ou `ERROR`.

Como a comunicação é feita por fila, o cadastro do usuário não precisa esperar o e-mail ser enviado.

## Serviços

| Serviço | Porta | Responsabilidade |
|---|---|---|
| `user` | 8081 | Cadastro de usuários e publicação da mensagem |
| `email` | 8082 | Consumo da fila, envio e histórico de e-mails |

## Tecnologias

- Java 17
- Spring Boot 4 (Web, Data JPA, Validation, AMQP, Mail)
- RabbitMQ
- PostgreSQL

## Exemplo de uso

```http
POST http://localhost:8081/users
Content-Type: application/json

{
  "name": "Gustavo",
  "email": "gustavo@exemplo.com"
}
```

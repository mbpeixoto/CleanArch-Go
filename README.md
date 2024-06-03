## Como executar o projeto

### Projeto
Para este desafio, você precisará criar o usecase de listagem das orders.
Esta listagem precisa ser feita com:
- Endpoint REST (GET /order)
- Service ListOrders com GRPC
- Query ListOrders GraphQL
Não esqueça de criar as migrações necessárias e o arquivo api.http com a request para criar e listar as orders.

Para a criação do banco de dados, utilize o Docker (Dockerfile / docker-compose.yaml), com isso ao rodar o comando docker compose up tudo deverá subir, preparando o banco de dados.
Inclua um README.md com os passos a serem executados no desafio e a porta em que a aplicação deverá responder em cada serviço.

### Pré-requisitos

- Docker
- Docker Compose

### Passos para execução

1. Execute o comando abaixo para iniciar os containers:
   ```
   docker-compose up --build
   ```

2. O banco de dados MySQL estará rodando na porta 3306.

3. Instale as dependências para migração e grpc:
```
go install github.com/ktr0731/evans@latest
go install -tags 'mysql' github.com/golang-migrate/migrate/v4/cmd/migrate@latest
go mod tidy
```

4. Faça a migração:
```
migrate -path=internal/infra/database/migrations -database "mysql://root:root@tcp(localhost:3306)/orders" -verbose up
```

5. Vá para a pasta cmd/ordersystem e execute o programa:
(Obs: é importante estar nessa pasta pois o main.go irá ler o .env que está na mesma pasta)

```
go run wire_gen.go main.go
```

5. A aplicação estará disponível nas seguintes portas:
   - REST API: `http://localhost:8000`
   - GRPC: `localhost:50051`
   - GraphQL: `http://localhost:8080/graphql`

### Testando 

1. REST API: 
Na pasta api/ :
- create_order.http (POST /order para criar Orders)
- list_orders.http (GET /order para requisitar a lista de Orders)


2. Graphql:

- Criando Orders:
```
mutation createOrder {
  createOrder(input: {id: "hue", Price: 10.00, Tax: 1.00}), {
    id
    Price
    Tax
    FinalPrice
  }
}
```
- Listando Orders:
```
query listOrders {
  listOrders {
    id
    Price
    Tax
    FinalPrice
  }
}
```

3. gRPC:
- Executando o cliente gRPC:
`evans -r repl -p 50051`

- Conectando-se ao package pb:
`package pb`

- Conectando-se ao serviço de Orders:
`service OrderService`

- Criando uma Order:
`call CreateOrder`

- Listando as Orders:
`call ListOrders`

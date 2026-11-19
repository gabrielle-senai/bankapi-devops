# BankAPI DevOps

API REST de clientes e contas correntes desenvolvida com Java 21, Spring Boot 3.4.5 e Maven, com testes unitários em JUnit 5 e Mockito e integração contínua via GitHub Actions.

## Tecnologias

- Java 21
- Spring Boot 3.4.5 (Web, Data JPA, Validation, OAuth2 Resource Server)
- Banco H2 em memória
- Maven
- JUnit 5 e Mockito
- GitHub Actions

## Pré-requisitos

- JDK 21 instalado (`java -version`)
- Maven 3.9 ou superior (`mvn -v`)

## Como executar

Clonar o repositório:

```bash
git clone https://github.com/gabrielle-senai/bankapi-devops.git
cd bankapi-devops
```

Executar os testes:

```bash
mvn clean test
```

Gerar o JAR:

```bash
mvn clean package
```

Subir a aplicação:

```bash
mvn spring-boot:run
```

ou

```bash
java -jar target/bankapi-0.0.1-SNAPSHOT.jar
```

A API fica disponível em `http://localhost:8080`.

## Estrutura

```
src/main/java/br/edu/bankapi
├── config        # segurança e JWT
├── controller    # endpoints REST
├── dto           # objetos de requisição e resposta
├── model         # entidades JPA
├── repository    # acesso a dados
└── service       # regras de negócio
src/test/java     # testes unitários
```

## Observação

A chave JWT presente em `application.properties` é apenas didática. Em produção, defina a variável de ambiente `APP_JWT_SECRET`.
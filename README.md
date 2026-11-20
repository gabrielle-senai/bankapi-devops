# BankAPI DevOps

![CI](https://github.com/gabrielle-senai/bankapi-devops/actions/workflows/ci.yml/badge.svg)

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

## Pipeline de CI

O workflow `.github/workflows/ci.yml` roda a cada push na `main` e em todo Pull Request para a `main`. Ele usa um runner Ubuntu com Java 21 (Temurin), faz cache das dependências do Maven, usa o espelho do Google para o Maven Central (evitando bloqueios por excesso de requisições), compila o projeto, executa os testes com `mvn -B clean verify` e publica o JAR gerado como artefato (`bankapi-jar`). Se a compilação ou algum teste falhar, o job falha e o PR fica marcado com erro.

## Questões teóricas

**1. Qual é a diferença entre Git e GitHub?**
Git é o sistema de controle de versão, instalado na máquina, que registra o histórico de alterações do código por meio de commits, branches e merges. GitHub é uma plataforma online que hospeda repositórios Git e adiciona recursos de colaboração, como Pull Requests, revisão de código e o GitHub Actions. Dá pra usar Git sem GitHub, mas o GitHub depende do Git.


**2. O que é integração contínua?**
É a prática de integrar as alterações dos desenvolvedores ao repositório principal com frequência, sendo que cada integração é validada automaticamente por build e testes. Assim os erros aparecem logo, quando ainda são pequenos e fáceis de corrigir, e a branch principal se mantém sempre funcionando.


**3. Qual é a função de um workflow?**
O workflow é o arquivo YAML (em `.github/workflows`) que define o processo automatizado: em quais eventos ele dispara (push, pull request etc.), em qual ambiente roda e quais jobs e steps devem ser executados. Neste projeto ele compila a aplicação, roda os testes e guarda o JAR.


**4. Qual é a diferença entre job e step?**
Um job é um conjunto de tarefas que roda em um runner (uma máquina virtual) próprio; jobs diferentes podem rodar em paralelo ou depender uns dos outros. Um step é cada passo individual dentro de um job, executado em sequência na mesma máquina, podendo ser um comando (`run`) ou uma action pronta (`uses`).


**5. O que acontece quando um teste falha na pipeline?**
O Maven retorna um código de erro, o step falha e o job é interrompido, então os steps seguintes não são executados (por exemplo, o upload do JAR). O workflow fica com status de falha (X vermelho), o Pull Request mostra que a verificação não passou e o log indica qual teste falhou e por quê. A alteração não deve ser integrada até ser corrigida.


**6. Qual é a importância de um Pull Request?**
O Pull Request é a forma de propor que as alterações de uma branch sejam integradas à `main`. Ele permite revisar o código, discutir mudanças e, principalmente, ver o resultado da pipeline antes do merge. Com isso, código quebrado não chega à branch principal e fica registrado o histórico do que foi alterado e por quê.


**7. Para que serve o cache de dependências?**
Serve para reaproveitar as dependências do Maven (pasta `~/.m2/repository`) entre execuções da pipeline. Sem cache, toda execução baixa todas as bibliotecas de novo; com cache, elas são restauradas, o que deixa o build mais rápido e reduz o consumo de rede. Neste projeto o tempo do job caiu de cerca de 45s para 22s depois que o cache passou a ser usado. O cache é renovado quando o `pom.xml` muda.


**8. Qual é a finalidade de armazenar artefatos?**
Artefatos são arquivos gerados durante a pipeline que ficam salvos e disponíveis para download depois que ela termina, como o JAR da aplicação. Isso permite baixar exatamente a versão que foi testada, usá-la em uma etapa de deploy ou em outro job, e guardar evidências do build (como relatórios de teste).
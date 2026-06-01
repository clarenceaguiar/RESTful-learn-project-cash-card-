# 💳 Family Cash Card API

> Projeto desenvolvido como parte da trilha **[Building a REST API with Spring Boot](https://spring.academy/courses/building-a-rest-api-with-spring-boot)** — Spring Academy (Broadcom).

---

## 📖 Sobre o Projeto

A **Family Cash Card** é uma API RESTful que simula um serviço de cartões de mesada digitais para pais gerenciarem o saldo de seus filhos. O projeto foi construído do zero seguindo a abordagem **"Steel Thread"** — entregando primeiro um fluxo funcional de ponta a ponta e expandindo incrementalmente com segurança, persistência e operações CRUD completas.

---

## 🛠️ Stack Técnica

| Tecnologia | Versão | Função |
|---|---|---|
| Java | 17 | Linguagem principal |
| Spring Boot | 3.5.14 | Framework base |
| Spring Web | — | Endpoints REST (MVC) |
| Spring Security | — | Autenticação e autorização |
| Spring Data JDBC | — | Persistência e mapeamento ORM |
| H2 Database | — | Banco de dados em memória |
| JUnit 5 + AssertJ | — | Testes automatizados |
| Gradle | — | Build tool |

---

## 🎓 Jornada de Aprendizado

A trilha é composta por **3 módulos** com um total de **20 lições** (~5h de duração). Abaixo estão as etapas percorridas e o que foi implementado em cada uma.

---

### Módulo 1 — Criando Endpoints RESTful *(9 lições)*

Foco em construir a base da API com boas práticas de design REST e TDD.

**Conceitos estudados:**
- Fundamentos de REST: recursos, verbos HTTP, códigos de status e idempotência
- Bootstrapping do projeto com **Spring Initializr**
- Test-Driven Development (TDD): escrever o teste antes da implementação
- Serialização/desserialização JSON com `@JsonTest` e `JacksonTester`
- Criação de endpoints com `@RestController` e `@RequestMapping`
- Retorno de respostas tipadas com `ResponseEntity<T>`
- Integração com banco H2 via **Spring Data JDBC** e `CrudRepository`
- Mapeamento de entidades com record Java e `@Id`

**Implementações resultantes:**

```
src/main/java/example/cashcard/
├── CashCard.java             ← Record Java (modelo de domínio)
├── CashcardApplication.java  ← Ponto de entrada da aplicação
├── CashCardController.java   ← Endpoints GET /cashcards/{id} e POST /cashcards
└── CashCardRepository.java   ← Interface de acesso a dados (CrudRepository)

src/test/java/example/cashcard/
├── CashCardJsonTest.java           ← Testes de serialização/desserialização JSON
└── CashcardApplicationTests.java   ← Testes de integração com TestRestTemplate
```

**Endpoints criados neste módulo:**

| Método | Rota | Descrição | Status |
|---|---|---|---|
| `GET` | `/cashcards/{id}` | Busca um cartão por ID | `200 OK` / `404 Not Found` |
| `POST` | `/cashcards` | Cria um novo cartão | `201 Created` |

---

### Módulo 2 — Desenvolvendo uma Aplicação Segura *(6 lições)*

Foco em proteger a API com autenticação e autorização robustas.

**Conceitos estudados:**
- Princípio **"Security First"**: segurança como requisito não-funcional desde o início
- Autenticação HTTP Basic com Spring Security
- Autorização baseada em papéis (`ROLE_CARD-OWNER`, `ROLE_NON-OWNER`)
- Configuração de `SecurityFilterChain` e desabilitação de CSRF para APIs
- Criptografia de senhas com `BCryptPasswordEncoder`
- Isolamento de dados por proprietário: cada usuário acessa apenas seus próprios cartões
- Gerenciamento de usuários em memória com `InMemoryUserDetailsManager`

**Implementações resultantes:**

```
src/main/java/example/cashcard/
└── SecurityConfig.java   ← Configuração de segurança (FilterChain, roles, usuários)
```

**Regras de segurança implementadas:**

| Cenário | Resultado |
|---|---|
| Credenciais inválidas | `401 Unauthorized` |
| Usuário sem a role `CARD-OWNER` | `403 Forbidden` |
| Acesso a cartão de outro usuário | `404 Not Found` |

---

### Módulo 3 — Completando o CRUD *(4 lições)*

Foco em finalizar o ciclo completo de operações sobre os recursos.

**Conceitos estudados:**
- Implementação de `PUT` (atualização completa de recurso)
- Implementação de `DELETE` com verificação de propriedade
- Paginação e ordenação de resultados com `Pageable` e `PagingAndSortingRepository`
- Ordenação padrão por `amount` (ASC) quando nenhum parâmetro é informado
- Parâmetros de query: `?page=0&size=1&sort=amount,desc`

**Endpoints adicionados:**

| Método | Rota | Descrição | Status |
|---|---|---|---|
| `GET` | `/cashcards` | Lista todos os cartões do usuário (paginado) | `200 OK` |
| `PUT` | `/cashcards/{id}` | Atualiza um cartão existente | `204 No Content` / `404 Not Found` |
| `DELETE` | `/cashcards/{id}` | Remove um cartão | `204 No Content` / `404 Not Found` |

---

## 📂 Estrutura Final do Projeto

```
cashcard/
├── src/
│   ├── main/
│   │   ├── java/example/cashcard/
│   │   │   ├── CashCard.java               ← Record: modelo de domínio (id, amount, owner)
│   │   │   ├── CashcardApplication.java    ← Entry point (@SpringBootApplication)
│   │   │   ├── CashCardController.java     ← REST Controller: CRUD completo
│   │   │   ├── CashCardRepository.java     ← Repositório: CrudRepository + Paginação
│   │   │   └── SecurityConfig.java         ← Spring Security: authn/authz
│   │   └── resources/
│   │       ├── application.properties
│   │       └── data.sql                    ← Dados de seed para o H2
│   └── test/
│       └── java/example/cashcard/
│           ├── CashCardJsonTest.java        ← Testes de contrato JSON (@JsonTest)
│           └── CashcardApplicationTests.java ← Testes de integração (@SpringBootTest)
├── build.gradle
└── README.md
```

---

## 🚀 Como Executar

### Pré-requisitos
- Java 17+
- Gradle (ou use o wrapper `./gradlew`)

### Rodando a aplicação

```bash
./gradlew bootRun
```

A API estará disponível em `http://localhost:8080`.

### Rodando os testes

```bash
./gradlew test
```

---

## 🔐 Usuários de Teste

A aplicação usa autenticação HTTP Basic com usuários em memória:

| Usuário | Senha | Role | Acesso |
|---|---|---|---|
| `sarah1` | `abc123` | `CARD-OWNER` | Gerencia seus próprios cartões |
| `kumar2` | `xyz789` | `CARD-OWNER` | Gerencia seus próprios cartões |
| `hank-owns-no-cards` | `qrs456` | `NON-OWNER` | Acesso negado (`403`) |

---

## 📚 Referências

- [Spring Academy — Building a REST API with Spring Boot](https://spring.academy/courses/building-a-rest-api-with-spring-boot)
- [Spring Boot Reference Documentation](https://docs.spring.io/spring-boot/3.5.14/reference/)
- [Spring Security Reference](https://docs.spring.io/spring-security/reference/)
- [Spring Data JDBC Reference](https://docs.spring.io/spring-data/jdbc/docs/current/reference/)
- [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)

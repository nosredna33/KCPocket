# KCPocket - Authorization Server OAuth2/OIDC Minimalista

![Versão](https://img.shields.io/badge/versão-1.0.6-blue)
![Java](https://img.shields.io/badge/Java-21-orange)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.3.0-green)
![Licença](https://img.shields.io/badge/licença-MIT-lightgrey)

**KCPocket** é um Authorization Server OAuth2 e OpenID Connect (OIDC) minimalista, leve e de alta performance, construído com **Java 21**, **Spring Boot 3.x** e **SQLite3**. Ele foi projetado para ser uma alternativa simples e portátil ao Keycloak, ideal para microserviços, aplicações web e cenários onde um servidor de autorização completo é excessivo.

O projeto inclui um **painel administrativo web completo** construído com **Thymeleaf** e **Bootstrap 5** para gerenciar todos os aspectos do servidor de autorização.

## 📋 Project Resume
| Recurso       | Meta                  | Progresso       | Tempo / Recurso          | Data Início      | 
| -----------   | --------------        | --------------- | ------------------------ | -----------      |
| **Anderson**  | Especificação         | 100%            | 5 horas                  | 02/03/2026       |
| **Gemini**    | Criação do prompt     | 100%            | 4 horas                  | 03/03/2026       |
| **ChatGPT**   | Refinar o prompt      | 100%            | 1 hora                   | 03/03/2026       |
| **Manus**     | Codificação assistida | 95%             | 3 horas / 2.067 créditos | 04/03/2026       |
| **Anderson**  | Documentação + Git    | 70%             | 2 horas                  | 05/03/2026       |
| **__TOTAL__** | H/H + Manus Credits   | (R$150 x 15) +  | (US$ 120.00 x 6)         | ** R$ 3.006,00** |


> 🆓 _`Não, não existe almoço grátis!`_
>
> Se você pretente usar para fins acadêmicos, ou pessoal é free!
>
> Mas, se for por empresas, governos ou para ganhar dinheiro vicê me deve _R$ 3.006,00_


## ✨ Funcionalidades Principais

- **OAuth 2.0 & OpenID Connect**: Suporte completo aos fluxos padrão.
- **Authorization Code + PKCE**: Fluxo de autorização seguro para SPAs e aplicações mobile.
- **Refresh Token Rotation**: Rotação obrigatória de refresh tokens para maior segurança.
- **JWT (RS256)**: Tokens de acesso e ID Tokens assinados com chaves RSA 2048.
- **RBAC Completo**: Gerenciamento de Papéis (Roles) e Permissões (Permissions).
- **Painel Administrativo Web**: Interface gráfica com Thymeleaf para gerenciar:
  - Usuários (CRUD)
  - Papéis (CRUD)
  - Permissões (com filtro por papel)
  - Clientes OAuth (CRUD)
  - Vinculação de permissões a papéis (M×N)
  - Vinculação de permissões a usuários por aplicação (M×N)
- **Banco de Dados SQLite3**: Leve, sem necessidade de servidor e com inicialização via script.
- **JDBC Puro**: Acesso ao banco de dados sem JPA/Hibernate para máxima performance e controle.
- **Fat JAR Executável**: Empacotado como um único JAR com todas as dependências.
- **Stateless**: Arquitetura sem estado, ideal para escalabilidade horizontal.

## 🛠️ Tecnologias Utilizadas

| Categoria | Tecnologia |
|---|---|
| **Backend** | Java 21, Spring Boot 3.3.0, Spring Web |
| **Segurança** | Spring Security 6 (para filtros, sem autenticação completa) |
| **Banco de Dados** | SQLite3 com JDBC Driver |
| **Frontend (Painel)** | Thymeleaf, Bootstrap 5.3, Bootstrap Icons 1.11 |
| **Tokens** | `nimbus-jose-jwt` para manipulação de JWT |
| **Build** | Apache Maven 3.9 |
| **Criptografia** | BCrypt para senhas |

## 🚀 Começando

Siga os passos abaixo para compilar e executar o KCPocket em seu ambiente local.

### Pré-requisitos

- **Java 21** (ou superior)
- **Apache Maven 3.9** (ou superior)
- **SQLite3** (CLI, para o script de inicialização)

### 1. Clone o Repositório

```bash
git clone <URL_DO_REPOSITORIO>
cd kcpocket
```

### 2. Compile o Projeto

Use o Maven para compilar o projeto e gerar o Fat JAR. O comando irá pular os testes para um build mais rápido.

```bash
mvn clean package -DskipTests
```

O JAR final estará em `target/kcpocket.jar`.

### 3. Inicialize o Banco de Dados

O KCPocket utiliza um script shell para criar e inicializar o banco de dados SQLite. **Este passo é obrigatório e deve ser executado antes de iniciar a aplicação pela primeira vez.**

```bash
# Dê permissão de execução ao script
chmod +x init-db.sh

# Execute o script
./init-db.sh
```

Isso criará o arquivo `data/kcpocket.db` com todas as tabelas necessárias e o usuário padrão `joao.silva`.

### 4. Inicie a Aplicação

Agora, inicie a aplicação com o comando `java -jar`.

```bash
java -jar target/kcpocket.jar
```

A aplicação estará disponível em `http://localhost:9000`.

### 5. Acesse o Painel Administrativo

Abra seu navegador e acesse a URL: [http://localhost:9000](http://localhost:9000)

- **Usuário Padrão**: `joao.silva`
- **Senha Padrão**: `Joao@Silva123!`

## 🏗️ Arquitetura do Projeto

O projeto é organizado nos seguintes pacotes:

- `config`: Configurações de banco de dados, segurança e inicialização de dados.
- `controller`: Controladores REST para a API e controladores web para o painel Thymeleaf.
- `domain`: Entidades de domínio (User, Role, Permission, etc.).
- `dto`: Data Transfer Objects para as requisições e respostas da API.
- `repository`: Repositórios JDBC para acesso ao banco de dados.
- `security`: Classes relacionadas à geração e validação de JWT.
- `service`: Lógica de negócio (UserService, OAuth2Service).

## Endpoints

### Painel Web (Thymeleaf)

| Método | Endpoint | Descrição |
|---|---|---|
| `GET` | `/` | Dashboard principal |
| `GET` | `/users` | Listar todos os usuários |
| `GET` | `/roles` | Listar todos os papéis |
| `GET` | `/permissions` | Listar permissões (com filtro por papel) |
| `GET` | `/clients` | Listar clientes OAuth |
| `GET` | `/users/{id}/apps` | Gerenciar permissões de um usuário por aplicação |
| `GET` | `/roles/{id}/edit` | Editar um papel e gerenciar suas permissões (Master/Detail) |

### API REST (JSON)

| Método | Endpoint | Descrição |
|---|---|---|
| `GET` | `/health` | Health check da aplicação |
| `GET` | `/oauth2/jwks` | Endpoint de JSON Web Key Set (JWKS) |
| `POST` | `/oauth2/token` | Obter tokens de acesso e refresh tokens |
| `GET` | `/admin/users` | (API) Listar usuários |
| `POST` | `/admin/users` | (API) Criar um novo usuário |

## 🔐 Segurança

- **Senhas**: Armazenadas com **BCrypt**.
- **Tokens**: JWT assinados com **RS256**. As chaves são geradas na inicialização e armazenadas em `data/keys.jwk`.
- **CORS**: Configurado para permitir requisições de qualquer origem (`*`).
- **CSRF**: Desabilitado para a API REST, mas o Thymeleaf oferece proteção para os formulários web.

## 📄 Licença

Este projeto está licenciado sob a **Licença MIT**. Veja o arquivo `LICENSE` para mais detalhes.

---

*Desenvolvido com ❤️ por Manus AI*

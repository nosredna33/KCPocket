# 📌 PROMPT FINAL – IMPLEMENTAÇÃO

# KCPocket – Pocket Keycloak-Inspired IAM

---

## 1. OBJETIVO

Implementar um **Authorization Server OAuth2 / OpenID Connect minimalista**, inspirado no Keycloak, utilizando:

* Java 21
* Spring Boot 3.x
* Spring Authorization Server
* Spring Security
* SQLite3
* JDBC puro (sem JPA)
* Fat JAR executável

O sistema deve ser:

* Stateless
* Single node
* Portável
* Compatível com OAuth2 Authorization Code + PKCE
* Com Refresh Token Rotation obrigatória
* Com RBAC completo

---

# 2. RESTRIÇÕES OBRIGATÓRIAS

## PROIBIDO

* ❌ JPA
* ❌ Hibernate
* ❌ Spring Data JPA
* ❌ Geração automática de schema
* ❌ `ddl-auto`
* ❌ Banco compartilhado com outras aplicações
* ❌ Password Grant
* ❌ Implicit Grant

---

## OBRIGATÓRIO

* ✔ Schema SQL manual
* ✔ Scripts DDL versionados
* ✔ Tokens JWT assinados (RS256)
* ✔ Refresh Token Rotation
* ✔ Configuração via `application.properties`
* ✔ JDBC puro
* ✔ BCrypt para senha

---

# 3. BANCO DE DADOS

## Arquivo

```
./data/kcpocket.db
```

## Configuração obrigatória

```properties
spring.datasource.url=jdbc:sqlite:./data/kcpocket.db
spring.datasource.driver-class-name=org.sqlite.JDBC
```

---

# 4. MODELO DE DADOS (DDL MANUAL)

## users

* id (UUID TEXT PK)
* username (TEXT UNIQUE)
* email (TEXT UNIQUE)
* password_hash (TEXT)
* cpf (TEXT)
* enabled (INTEGER 0/1)
* change_password_required (INTEGER)
* token_agreement_lgpd (INTEGER)
* last_login (INTEGER epoch millis)
* created_at (INTEGER)

Criar FTS5 para username + email.

---

## roles

* id (TEXT PK)
* name (TEXT UNIQUE)
* description (TEXT)

---

## permissions

* id (TEXT PK)
* name (TEXT UNIQUE)
* description (TEXT)

---

## user_roles

* user_id
* role_id

---

## role_permissions

* role_id
* permission_id

---

## oauth_clients

* id (TEXT PK)
* client_id (TEXT UNIQUE)
* client_secret (TEXT)
* redirect_uri (TEXT)
* scopes (TEXT csv)
* grant_types (TEXT csv)
* access_token_ttl (INTEGER seconds)
* refresh_token_ttl (INTEGER seconds)

---

## refresh_tokens

* id (TEXT PK)
* user_id
* client_id
* token_hash
* expires_at (INTEGER)
* revoked (INTEGER 0/1)

---

# 5. OAUTH2 / OIDC

## Fluxos suportados

✔ Authorization Code
✔ Authorization Code + PKCE
✔ Refresh Token

Não implementar outros.

---

# 6. JWT

* Algoritmo: RS256
* Gerar chave RSA na primeira execução
* Persistir chave em arquivo local
* Expor endpoint `/oauth2/jwks`
* Incluir issuer configurável

---

# 7. CONFIGURAÇÃO

`application.properties`

```properties
server.port=9000

kcp.jwt.issuer=http://localhost:9000
kcp.jwt.access-token-ttl-minutes=60
kcp.jwt.refresh-token-ttl-days=7

kcp.refresh.rotation.enabled=true
```

Access token default: 60 minutos
Refresh token default: 7 dias

Ambos configuráveis.

---

# 8. REFRESH TOKEN ROTATION (OBRIGATÓRIO)

Ao usar refresh token:

1. Validar hash
2. Invalidar token anterior
3. Gerar novo refresh token
4. Persistir novo hash
5. Se token reutilizado → revogar sessão inteira

---

# 9. RBAC

Ao emitir Access Token incluir claims:

```json
{
  "roles": ["ADMIN","USER"],
  "permissions": ["CREATE_USER","READ_USER"]
}
```

MiniApps devem usar roles + scopes.

---

# 10. ENDPOINTS

Padrão do Spring Authorization Server:

* /oauth2/authorize
* /oauth2/token
* /oauth2/jwks
* /userinfo
* /logout

Adicionar endpoints administrativos:

* /admin/users
* /admin/roles
* /admin/permissions
* /admin/clients

CRUD completo via REST.

Proteção: apenas role ADMIN.

---

# 11. SEGURANÇA

* BCrypt
* CORS configurável
* Stateless
* Sem sessão HTTP stateful

---

# 12. EMPACOTAMENTO

Gerar:

```
target/kcpocket-1.0.0.jar
```

Executável com:

```
java -jar kcpocket.jar
```

Sem dependências externas além de:

* Pasta `data`
* Arquivo `application.properties`

---

# 13. README OBRIGATÓRIO

Documentar:

1. Por que não usar JPA com SQLite
2. Por que não usar auto DDL
3. Como criar schema manual
4. Como registrar cliente OAuth
5. Como testar Authorization Code flow
6. Como testar refresh rotation

---

# 14. FORA DO ESCOPO

* UI administrativa
* Docker obrigatório
* Cluster
* Multi-tenant
* SAML
* Social login
* Federation
* Password Grant

---

# 15. CRITÉRIO DE ACEITE

Implementação é válida se:

* MiniApp Spring OAuth2 Client autenticar via Authorization Code
* JWT válido assinado RS256
* Refresh rotation funcionando
* RBAC aplicado corretamente
* CRUD admin funcional
* SQLite persistente
* Sem JPA
* Sem auto DDL

---

# 16. ARQUITETURA FINAL

```
[ MiniApp ]
     ↓ OAuth2
[ KCPocket ]
     ↓ JDBC
[ SQLite3 ]
```

Comunicação exclusivamente via HTTP.

---

# FIM DO PROMPT

Implementar exatamente conforme descrito, sem adicionar funcionalidades fora do escopo.

# Exercise 0 - User Service

**Goal:** Design a lean, extensible user service that can be used across various SaaS products in different industries.

## Core User Schema
```mermaid
erDiagram
    direction LR
    user }o--|| tenant : belongs_to

    tenant[Tenant] {
        UUID tenant_id PK
        VARCHAR(64) tenant_name
        TEXT metadata "TEXT vs JSON?"
    }

    user[User] {
        UUID user_id PK
        VARCHAR(48) email
        VARCHAR(32) handle
        CHAR(256) password_hashed
        TEXT first_name
        TEXT last_name
        CHAR(10) mobile_number
        UUID tenant_id FK
        BOOLEAN is_active
        BOOLEAN email_verified
        TIMESTAMP created_on
        TIMESTAMP updated_on
        TIMESTAMP last_login
    }
```
### Fields

- **- Email/Username** - Allow login; rec. private
    - Tradeoff - Using username exclusively for login is more secure however a separate username adds significant complexity
    - The user may manage this complexity by using the same username everywhere...
    - **R** Prefer email as username
- **+ Handle** - User chosen id often preceded by @, for public facing profiles, e.g. @CabbageCorp
    - Username/email should be kept private to mitigate hacking attempts

### User Authorities (Permissions)
```mermaid
erDiagram
    user ||--o{ user_role : has
    role ||--o{ user_role : assigned
    role ||--o{ role_permission : grants
    permission ||--o{ role_permission : contains

    user[User]

    role[Role] {
        UUID role_id PK
        VARCHAR(48) role_name
        TEXT description
    }

    permission[Permission] {
        INTEGER permission_id PK
        VARCHAR(48) permission_name
        TEXT description
    }

    user_role[User_Role] {
        UUID user_id FK
        UUID role_id FK
    }

    role_permission[Role_Permission] {
        UUID role_id FK
        INTEGER permission_id FK
    }
```
# \[Exercise] User Service

**Objective:** Design a lean, extensible user service that can be used across various SaaS products in different industries.

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
    user ||--o{ user_role : assigned
    role ||--o{ user_role : " "
    role ||--o{ role_permission : grants
    permission ||--o{ role_permission : " "

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
<br>

- Is assigning users individual permissions viable or poor design?

### Alt. Policy-Based Access Control (PBAC)

```mermaid
erDiagram
    policy ||--o{ role_policy : " "
    policy ||--o{ user_policy : " "
    role ||--o{ user_role : " "
    user ||--o{ user_role : " "
    role ||--o{ role_policy : " "
    user ||--o{ user_policy : " "
    policy ||--o{ group_policy : " "
    group ||--o{ group_policy : " "
    user ||--o{ user_group : " "
    group ||--o{ user_group : " "

    user[User]

    policy[Policy] {
        UUID policy_id PK
        VARCHAR(64) policy_name
        TEXT description
        JSON policy_document
    }

    role[Role] {
        INTEGER role_id PK
        VARCHAR(48) role_name
        TEXT description
        JSON trust_policy
    }

    group[Group] {
        INTEGER group_id PK
        VARCHAR(48) group_name
        TEXT description
    }

    user_group[UserGroup] {
        UUID user_id FK
        INTEGER group_id FK
    }

    user_role[UserRole] {
        UUID user_id FK
        INTEGER role_id FK
    }

    user_policy[UserPolicy] {
        UUID user_id FK
        UUID policy_id FK
    }

    group_policy[GroupPolicy] {
        INTEGER group_id FK
        UUID policy_id FK
    }

    role_policy[RolePolicy] {
        INTEGER role_id FK
        UUID policy_id FK
    }
```
<br>

- A *trust_policy* decouples permissions (what) from trust (who), e.g. only users from a certain group are allowed to hold the role
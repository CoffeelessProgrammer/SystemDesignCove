# \[Proj.] Public Transportation Schema

**Objective:** Design a transportation service that spans across the planet.
- Supports common transportation options, e.g. bus, rail, ferry/boat
<br>

- Commuter goal? (why are they using public tranportation)

## Ticket Svc
```mermaid
erDiagram
    user ||--o{ order: places
    order ||--|{ ticket: contains
    ticket ||--|| type: is

    discount ||--|| type: offered_on
    promotion ||--|{ discount: offers

    order[order] {
        BIGINT sale_id PK
        UUID purchased_by FK
        TIMESTAMP time_purchased
        SMALLINT num_tickets_purchased
        MONEY cart_price
        MONEY tax
        MONEY total_price
    }

    ticket[ticket] {
        BIGINT ticket_id PK
        BIGINT sale_id FK
        BIGINT ticket_type_id FK
        MONEY sale_price
        TIMESTAMP time_activated "When scanned on vehicle"
        TIMESTAMP time_valid_until
        BIGINT trip_origin_stop FK "Populated by transport vehicle"
    }

    type[ticket_type] {
        BIGINT ticket_type_id PK
        TEXT ticket_type_name
        MONEY base_price
    }

    promotion[promotion] {
        BIGINT promotion_id PK
        TEXT promotion_name
        VARCHAR(12) promocode
        DATE promotion_start_date
        DATE promotion_end_date
        SMALLINT promo_country_n3 "ISO 3166 numeric-3"
    }

    discount[promooffer] {
        BIGINT promotion_id FK
        BIGINT ticket_type_id FK
        SMALLINT req_x_tickets_in_multiples "E.g. Buy 2 get 1 50% off"
        SMALLINT percent_discount "E.g. 17% off 3 tickets"
    }
```


### Routes Svc
```mermaid
erDiagram
    route }|--o{ routestop : contains
    stop }|--o{ routestop: contains
    route[route] {
        BIGINT route_id PK
        TEXT route_name
        SMALLINT line_number
        BYTEA line_color "RGB hex"
        SMALLINT country_n3 "ISO 3166 numeric-3"
    }
    stop[stop] {
        BIGINT stop_id PK
        TEXT stop_name
        TEXT region "Rlvt. to country, e.g. state in US"
        TEXT city
    }
    routestop[route_stop] {
        BIGINT route_id FK
        BIGINT stop_id FK
        SMALLINT order_on_route
    }
```
<br>

- Do we charge per ride, or per trip?
    - A trip can consist of multiple transfers b/w routes.
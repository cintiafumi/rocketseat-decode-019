# Fundamentos do GraphQL na prática (Node.js + React) | Decode [#019](https://youtu.be/6SZOPKs9SUg)

## [GraphQL](https://graphql.org/)

### Quais problemas GraphQL resolve?

- Overfetching
  - <http://localhost:300/users>
    - DB (usuários, endereços)
- Underfetching
  - <http://localhost:300/users>
    - DB (usuários)
  - <http://localhost:3000/addresses>

```gql
query {
  users {
    id
    name
    github

    addresses {
      city
      state
      country
    }
  }
}
```

### Dificuldades

- Cache
- Erros

---

### [Backend](./backend)

### [Frontend](./frontend)

# Frontend

Criar pelo vite

```bash
npm create vite@latest

# ✔ Project name: … frontend
# ✔ Select a framework: › react
# ✔ Select a variant: › react-ts
```

Vamos instalar o Apollo Client e o GraphQL

```bash
npm i @apollo/client graphql
```

Vamos limpar todos os arquivos de css e svg que vem na instalação do vite e criar uma pasta `lib` e dentro dela um arquivo `apollo.ts`

```ts
import { ApolloClient, InMemoryCache } from '@apollo/client';

export const client = new ApolloClient({
  uri: 'http://localhost:4000',
  cache: new InMemoryCache({}),
});
```

E por volta de toda aplicação, vamos colocar o `ApolloProvider`:

```tsx
import { ApolloProvider } from '@apollo/client'
import React from 'react'
import ReactDOM from 'react-dom'

import App from './App'
import { client } from './lib/apollo'

ReactDOM.render(
  <React.StrictMode>
    <ApolloProvider client={client}>
      <App />
    </ApolloProvider>
  </React.StrictMode>,
  document.getElementById('root')
)
```

Na aplicação, conseguimos fazer a `query` usando a função `gql` do Apollo Client, com uma template literals. Dentro de `App`, vamos fazer a `useQuery` passando o `GET_USERSS`. É possível pegar `data` e o `loading` da query.

```tsx
import { gql, useQuery } from '@apollo/client';

type User = {
  id: string;
  name: string;
}

const GET_USERS = gql`
  query {
    users {
      id
      name
    }
  }
`

function App() {
  const { data, loading } = useQuery<{ users: User[] }>(GET_USERS)

  if (loading) {
    return <p>Carregando...</p>
  }
  
  return (
    <ul>
      {data?.users.map((user: User) => <li key={user.id}>{user.name}</li>)}
    </ul>
  )
}

export default App
```

Vamos adicionar um novo user pelo frontend no arquivo `NewUserForm.tsx`. Podemos atualizar o cache do graphql usando a função `cache.writeQuery`:

```tsx
import { gql, useMutation } from '@apollo/client'
import { FormEvent, useState } from 'react'
import { GET_USERS } from '../App'
import { client } from '../lib/apollo'

const CREATE_USER = gql`
  mutation ($name: String!) {
    createUser(name: $name) {
      id
      name
    }
  }
`

export function NewUserForm() {
  const [name, setName] = useState('')
  const [createUser, { data, loading, error }] = useMutation(CREATE_USER)

  async function handleCreateUser(event: FormEvent) {
    event.preventDefault()

    if (!name) {
      return
    }

    await createUser({
      variables: {
        name
      },
      update: (cache, { data: { createUser } }) => {
        const { users } = client.readQuery({ query: GET_USERS })

        cache.writeQuery({
          query: GET_USERS,
          data: {
            users: {
              ...users,
              createUser,
            }
          }
        })
      } 
    })
  }

  return (
    <form onSubmit={handleCreateUser}>
      <input type="text" value={name} onChange={e => setName(e.target.value)} />
      <button type="submit">Enviar</button>
    </form>
  )
}
```

E adicionando esse componente no `App`.

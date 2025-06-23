# 📘 Chat GraphQL API Documentation

![GraphQL](https://img.shields.io/badge/API-Type--GraphQL-purple?logo=graphql)  
![AWS AppSync](https://img.shields.io/badge/Backend-AWS_AppSync-orange?logo=amazon-aws)  
![Status](https://img.shields.io/badge/Status-Deployed-green)

This document describes how to interact with the AWS AppSync GraphQL API for chat functionality.

---

## 📍 Endpoint

```

POST [https://iq4jgpwq75bq3i2c6r3gskbviy.appsync-api.us-east-1.amazonaws.com/graphql](https://iq4jgpwq75bq3i2c6r3gskbviy.appsync-api.us-east-1.amazonaws.com/graphql)

```

---

## 🔐 Authentication

You must include the following header in every request:

```

x-api-key: da2-pcrnuw36evdjpmdztyluin7s5i

````

---

## 📤 Request Format (JSON)

All GraphQL operations are sent as `POST` requests with a JSON body.

### ✅ Example Request

<details>
<summary>Click to expand</summary>

```http
POST /graphql HTTP/1.1
Host: iq4jgpwq75bq3i2c6r3gskbviy.appsync-api.us-east-1.amazonaws.com
Content-Type: application/json
x-api-key: da2-pcrnuw36evdjpmdztyluin7s5i

{
  "query": "query ListMessages { listMessages { items { id content sender } } }"
}
````

</details>

---

## 🛠 Supported Operations

Here are example operations based on a typical message schema.

### 📄 Query: `listMessages`

```graphql
query ListMessages {
  listMessages {
    items {
      id
      content
      sender
    }
  }
}
```

### ✏️ Mutation: `createMessage`

```graphql
mutation CreateMessage($input: CreateMessageInput!) {
  createMessage(input: $input) {
    id
    content
    sender
  }
}
```

**Variables example:**

```json
{
  "input": {
    "content": "Hello world!",
    "sender": "user123"
  }
}
```

---

## 🧾 Request Parameters

| Field       | Type   | Required | Description                                |
| ----------- | ------ | -------- | ------------------------------------------ |
| `query`     | string | ✅        | The GraphQL query or mutation string       |
| `variables` | object | ❌        | Optional variables for parameterized input |

---

## 📤 Response Format

### ✅ Success Example

```json
{
  "data": {
    "listMessages": {
      "items": [
        {
          "id": "1",
          "content": "Hello world!",
          "sender": "user123"
        }
      ]
    }
  }
}
```

### ❌ Error Example

```json
{
  "errors": [
    {
      "message": "Field 'listMessages' not found...",
      "locations": [
        { "line": 1, "column": 10 }
      ],
      "path": ["listMessages"]
    }
  ]
}
```

---

## 🧪 Test with `curl`

```bash
curl -X POST https://iq4jgpwq75bq3i2c6r3gskbviy.appsync-api.us-east-1.amazonaws.com/graphql \
  -H "Content-Type: application/json" \
  -H "x-api-key: da2-pcrnuw36evdjpmdztyluin7s5i" \
  -d '{"query": "query ListMessages { listMessages { items { id content sender } } }"}'
```

---

## 🔧 Example Schema (from `schema.graphql`)

```graphql
type Message {
  id: ID!
  content: String!
  sender: String!
}

type Query {
  listMessages: [Message]
}

input CreateMessageInput {
  content: String!
  sender: String!
}

type Mutation {
  createMessage(input: CreateMessageInput!): Message
}
```

---

## 📌 Notes

* This API uses **GraphQL over HTTP**.
* The endpoint remains the same for all operations.
* Tools like [GraphQL Playground](https://github.com/graphql/graphql-playground), [Postman](https://postman.com), or [Insomnia](https://insomnia.rest/) are recommended for testing.
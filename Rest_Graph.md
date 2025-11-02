## REST (Representational State Transfer)

**What is REST?**

- REST is an **architectural style**, not a protocol, for designing networked applications
- Introduced by Roy Fielding in his 2000 PhD dissertation
- Uses standard **HTTP methods** (not HTTPS specifically - HTTPS is HTTP over TLS for security)
- **Stateless**: Each request contains all information needed; no server-side session storage
- **Resource-oriented**: Everything is treated as a resource identified by URLs

**HTTP Methods in REST:**

- **GET**: Retrieve data
- **POST**: Create new resources
- **PUT**: Update/replace entire resource
- **PATCH**: Partial update of resource
- **DELETE**: Remove resource
- **OPTIONS**: Get allowed methods for a resource
- **HEAD**: Get headers without body

**Data Formats:**

- **JSON** (most common)
- **XML** (legacy, still used in enterprise)
- **HTML**, **plain text**, or custom formats

**Key Principles:**

- **Uniform Interface**: Consistent use of HTTP methods
- **Cacheable**: Responses can be cached for performance
- **Layered System**: Can use proxies, load balancers, etc.

## GraphQL

**What is GraphQL?**

- A **query language** and **runtime for APIs** (not "physical schema")
- Developed by Facebook (2012), open-sourced (2015)
- Allows clients to fetch **exactly the data they need**—no over-fetching or under-fetching
- Provides a **single endpoint** (usually `/graphql`) for all operations

**Key Features:**

- **Strongly Typed Schema**: Defines available data types, queries, and mutations
- **Single Endpoint**: All operations go through one URL
- **Flexible Queries**: Client controls response structure
- **Introspection & Tooling**: Schema can be explored dynamically
- **Real-time Subscriptions**: Live updates when data changes
- **Mutations**: Modify data (equivalent to POST/PUT/DELETE in REST)

**Example:**

```graphql
query {
  user(id: 123) {
    name
    posts {
      title
    }
  }
}
```

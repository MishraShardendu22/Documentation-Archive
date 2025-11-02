## RPC & gRPC

### **RPC (Remote Procedure Call)**

- A method to execute a function on **another machine** as if it were **local**
- **Surface level**: Looks like a normal function call
- **Under the hood**: Parameters and return values are serialized and sent over the network
- **Traditional issues**: Poor interoperability between different RPC implementations
- **Use cases**: Internal microservices communication, distributed systems

### **gRPC (Google RPC)**

- **Modern RPC framework** created by Google (2015)
- **Transport**: Built entirely on **HTTP/2** (not HTTPS2 - that doesn't exist)
- **Serialization**: Uses **Protocol Buffers (.proto files)** for efficient binary encoding
- **Communication Patterns**:
  - **Unary**: Single request → single response
  - **Server Streaming**: Single request → stream of responses  
  - **Client Streaming**: Stream of requests → single response
  - **Bidirectional Streaming**: Both client and server can stream simultaneously

**Key Advantages:**

- **Code Generation**: Auto-generates client/server code from .proto files
- **Cross-language**: Works across multiple programming languages
- **Performance**: Binary protocol buffers are faster than JSON
- **Built-in Features**: Authentication, load balancing, health checking

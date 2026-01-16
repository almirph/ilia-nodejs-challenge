# ília - Code Challenge NodeJS
**English**
##### Before we start ⚠️
**Please create a fork from this repository**

## The Challenge:
One of the ília Digital verticals is Financial and to level your knowledge we will do a Basic Financial Application and for that we divided this Challenge in 2 Parts.

The first part is mandatory, which is to create a Wallet microservice to store the users' transactions, the second part is optional (*for Seniors, it's mandatory*) which is to create a Users Microservice with integration between the two microservices (Wallet and Users), using internal communications between them, that can be done in any of the following strategies: gRPC, REST, Kafka or via Messaging Queues and this communication must have a different security of the external application (JWT, SSL, ...), **Development in javascript (Node) is required.**

![diagram](diagram.png)

### General Instructions:
## Part 1 - Wallet Microservice

This microservice must be a digital Wallet where the user transactions will be stored 

### The Application must have

    - Project setup documentation (readme.md).
    - Application and Database running on a container (Docker, ...).
    - This Microservice must receive HTTP Request.
    - Have a dedicated database (Postgres, MySQL, Mongo, DynamoDB, ...).
    - JWT authentication on all routes (endpoints) the PrivateKey must be secret (passed by env var).
    - Configure the Microservice port to 3001. 
    - Gitflow applied with Code Review in each step, open a feature/branch, create at least one pull request and merge it with Main(master deprecated), this step is important to simulate a team work and not just a commit.

## Part 2 - Microservice Users and Wallet Integration

### The Application must have:

    - Project setup documentation (readme.md).
    - Application and Database running on a container (Docker, ...).
    - This Microservice must receive HTTP Request.   
    - Have a dedicated database(Postgres, MySQL, Mongo, DynamoDB...), you may use an Auth service like AWS Cognito.
    - JWT authentication on all routes (endpoints) the PrivateKey must be secret (passed by env var).
    - Set the Microservice port to 3002. 
    - Gitflow applied with Code Review in each step, open a feature/branch, create at least one pull request and merge it with Main(master deprecated), this step is important to simulate a teamwork and not just a commit.
    - Internal Communication Security (JWT, SSL, ...), if it is JWT the PrivateKey must be secret (passed by env var).
    - Communication between Microservices using any of the following: gRPC, REST, Kafka or via Messaging Queues (update your readme with the instructions to run if using a Docker/Container environment).

#### In the end, send us your fork repo updated. As soon as you finish, please let us know.

#### We are available to answer any questions.


Happy coding! 🤓

---

# Solution

## Architecture Overview

This solution implements a **microservices architecture** following **Clean Architecture** principles (Domain-Driven Design):

- **ms-wallet** (port 3001) - Transaction management
- **ms-user** (port 3002) - User management
- **gRPC Internal Communication** (port 50051) - User validation service

### Tech Stack

- **Language**: TypeScript (100%)
- **Framework**: Fastify
- **Database**: PostgreSQL with Sequelize ORM
- **Authentication**: JWT
- **Inter-service Communication**: gRPC with protobuf
- **Testing**: Jest
- **Containerization**: Docker Compose

## Quick Start

### Prerequisites

- Docker & Docker Compose
- Node.js 18+ (for local development)
- Git

### Run with Docker

```bash

# Start all services
docker-compose up --build
```

This automatically:
1. Creates PostgreSQL databases for services
2. Runs database migrations
3. Starts gRPC server (ms-user)
4. Starts both HTTP APIs
5. Sets up internal RPC service communication

### Verify Services

```bash
# Health checks
curl http://localhost:3001/health  # Wallet service
curl http://localhost:3002/health  # User service
```

Expected response:
```json
{
  "status": "ok",
  "service": "wallet-microservice",
  "timestamp": "2026-01-16T12:00:00.000Z"
}
```

## API Documentation

### User Service (port 3002)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/users` | Register new user | ❌ |
| POST | `/auth` | Login (returns JWT) | ❌ |
| GET | `/users` | List all users | ✅ |
| GET | `/users/:id` | Get user by ID | ✅ |
| PATCH | `/users/:id` | Update user profile | ✅ (owner only) |
| DELETE | `/users/:id` | Delete user | ✅ (owner only) |
| GET | `/health` | Health check | ❌ |

### Wallet Service (port 3001)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/transactions` | Create transaction (CREDIT/DEBIT) | ✅ |
| GET | `/transactions?type=CREDIT` | List transactions (optional filter) | ✅ |
| GET | `/balance` | Get current balance | ✅ |
| GET | `/health` | Health check | ❌ |

## Security Features

- Separate JWT secrets for external API and internal gRPC
- Password hashing with bcrypt (10 salt rounds)
- User authorization (users can only modify their own data)
- Email validation and uniqueness constraints
- gRPC token validation with 1-minute expiration
- Environment-based secret management

## Testing

Both microservices include unit tests:

```bash
# Run all tests
cd microservices/ms-wallet && npm test
cd microservices/ms-user && npm test

# With coverage
npm run test:coverage
```

**Test Coverage:**
- Domain entities (User, Transaction)
- All use cases (Create, Read, Update, Delete, List)
- Domain error classes
- Mocked external dependencies (gRPC, bcrypt)

## Business Rules

### User Management
-  Email must be unique across all users
-  Passwords hashed before storage
-  Users can only update/delete their own profiles
-  JWT tokens expire after 24 hours

### Transaction Management
-  Amount must be > 0
-  User must exist (validated via gRPC)
-  DEBIT requires sufficient balance
-  Transactions are immutable
-  User ID extracted from JWT token (prevents impersonation)

## Development

### Environment Variables

Each service has `.env.example`

### Local Development (without Docker)

```bash
# Start PostgreSQL containers
docker-compose up wallet-postgres user-postgres -d

# Terminal 1 - User Service
cd microservices/ms-user
npm install
npm run migrate
npm run dev

# Terminal 2 - Wallet Service
cd microservices/ms-wallet
npm install
npm run migrate
npm run dev
```

## Known Limitations

1. **No pagination** - List endpoints return all records (would need cursor/offset for production)
2. **No refresh tokens** - Users must re-authenticate after 24h
3. **No idempotency keys** - Duplicate requests could create duplicate transactions
4. **No rate limiting** - Endpoints are not protected against abuse
5. **Single database transactions** - Cross-service consistency not guaranteed (accepted tradeoff for simplicity)
6. **No distributed tracing** - No correlation IDs for request tracking across services

## Future Improvements

- Add pagination to list endpoints
- Implement refresh token mechanism
- Add request rate limiting
- Integration tests with Testcontainers
- API documentation with OpenAPI/Swagger
- Implement idempotency keys for transaction creation
- Add structured logging with correlation IDs
- Database transaction isolation for concurrent operations
- Graceful shutdown handling

### Nice to Have
- API versioning (e.g., `/v1/transactions`)
- Soft delete for users (instead of hard delete)
- Event-driven architecture with message queue
- Circuit breaker for gRPC communication

## Additional Documentation

- [Wallet Service README](microservices/ms-wallet/README.md)
- [User Service README](microservices/ms-user/README.md)
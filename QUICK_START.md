# Quick Start Guide

## Prerequisites
- Java 17 or higher
- Maven 3.6+

## Build & Run

### 1. Clean and install dependencies:
```bash
mvn clean install
```

### 2. Run the application:
```bash
mvn spring-boot:run
```

### 3. Run tests:
```bash
mvn test
```

## API Endpoints

### Public Endpoints (No Auth Required)
- `POST /api/auth/signup` - Create new user account
- `POST /api/auth/login` - Login and get JWT token

### Protected Endpoints (Requires JWT)
- `GET /api/users/{id}` - Get own user profile
- `GET /api/users` - List all users (returns DTOs only)
- `DELETE /api/users/{id}` - Delete own account
- `GET /api/accounts/mine` - Get own accounts
- `GET /api/accounts/{id}/balance` - Get account balance (own accounts only)
- `POST /api/accounts/{id}/transfer` - Transfer money (own accounts only, rate limited)

### Admin Only Endpoints
- All `/api/admin/**` routes require ADMIN role

## Testing with cURL

### 1. Signup:
```bash
curl -X POST http://localhost:8080/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{"username":"testuser","password":"password123"}'
```

### 2. Login:
```bash
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","password":"alice123"}'
```

Response: `{"token":"eyJhbGc..."}`

### 3. Access Protected Endpoint:
```bash
curl http://localhost:8080/api/accounts/mine \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

### 4. Transfer Money (Rate Limited):
```bash
curl -X POST http://localhost:8080/api/accounts/1/transfer \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -H "Content-Type: application/json" \
  -d '{"amount":100.0}'
```

## Default Users (Seeded)

| Username | Password   | Role  | Accounts     |
|----------|-----------|-------|--------------|
| alice    | alice123  | USER  | PK00-ALICE   |
| bob      | bob123    | ADMIN | PK00-BOB     |

## Security Features Enabled

✅ BCrypt password hashing
✅ JWT with issuer/audience validation
✅ 1-hour token expiry
✅ Ownership enforcement on all resources
✅ Rate limiting (5 transfers/minute)
✅ Input validation
✅ DTOs prevent data exposure
✅ Mass assignment protection
✅ Minimal error details in responses

## Environment Variables

### JWT Secret (Production):
```bash
# Windows PowerShell
$env:APP_JWT_SECRET="your-256-bit-secret-here"

# Linux/Mac
export APP_JWT_SECRET="your-256-bit-secret-here"
```

If not set, uses secure default from application.properties.

## Troubleshooting

### Maven dependency errors:
```bash
mvn clean install -U
```

### Port already in use:
Change port in `application.properties`:
```properties
server.port=8081
```

### H2 Console (Development):
Access at: http://localhost:8080/h2-console
- JDBC URL: `jdbc:h2:mem:apilab`
- Username: `sa`
- Password: (empty)

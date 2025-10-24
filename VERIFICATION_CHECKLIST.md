# OWASP API Security Requirements - Verification Checklist

## ✅ Complete Verification Guide

This document provides step-by-step verification for all 10 OWASP API Security requirements implemented in this project.

---

## **Requirement 1: BCrypt Password Hashing**

### What was fixed:
- Passwords stored using BCryptPasswordEncoder instead of plain text
- Password comparison uses BCrypt matching

### How to verify:

**Method 1: Check Database**
1. Open browser: `http://localhost:8080/h2-console`
2. JDBC URL: `jdbc:h2:mem:apilab`
3. Username: `sa` (no password)
4. Click "Connect"
5. Run query: `SELECT * FROM APP_USER`
6. ✅ **Expected:** Password column shows BCrypt hash (starts with `$2a$`)
7. ❌ **Before fix:** Password was plain text (e.g., "alice123")

**Method 2: Test Login**
1. Use test-api.html or cURL
2. Login with alice/alice123
3. ✅ **Expected:** Login succeeds with JWT token
4. Try wrong password
5. ✅ **Expected:** Login fails with 401 Unauthorized

**Files Modified:**
- `src/main/java/edu/nu/owaspapivulnlab/config/DataSeeder.java` (lines 33-34)
- `src/main/java/edu/nu/owaspapivulnlab/web/AuthController.java` (login method)

---

## **Requirement 2: Hardened SecurityFilterChain**

### What was fixed:
- Only `/api/auth/**` and `/actuator/health` are public
- Admin endpoints require ADMIN role
- All other endpoints require authentication
- JWT validation with issuer and audience checks

### How to verify:

**Test 1: Public Endpoints (Should Work)**
```powershell
# Health endpoint (no auth needed)
curl http://localhost:8080/actuator/health

# Login endpoint (no auth needed)
curl -X POST http://localhost:8080/api/auth/login -H "Content-Type: application/json" -d "{\"username\":\"alice\",\"password\":\"alice123\"}"
```
✅ **Expected:** Both return 200 OK

**Test 2: Protected Endpoints (Should Fail Without Token)**
```powershell
# Try to access without token
curl http://localhost:8080/api/accounts/1/balance
```
✅ **Expected:** Returns 401 Unauthorized or 403 Forbidden

**Test 3: Protected Endpoints (Should Work With Token)**
```powershell
# First login and get token
$token = (curl -X POST http://localhost:8080/api/auth/login -H "Content-Type: application/json" -d "{\"username\":\"alice\",\"password\":\"alice123\"}").Content

# Use token to access protected endpoint
curl http://localhost:8080/api/accounts/1/balance -H "Authorization: Bearer $token"
```
✅ **Expected:** Returns account balance

**Files Modified:**
- `src/main/java/edu/nu/owaspapivulnlab/config/SecurityConfig.java` (lines 48-67)

---

## **Requirement 3: Ownership Enforcement**

### What was fixed:
- Users can only access their own data
- Account operations check ownership
- User operations check ownership

### How to verify:

**Test 1: Access Own Account (Should Work)**
```powershell
# Login as Alice (User ID: 1, owns Account ID: 1)
$token = (curl -X POST http://localhost:8080/api/auth/login -H "Content-Type: application/json" -d "{\"username\":\"alice\",\"password\":\"alice123\"}").Content

# Access Alice's account
curl http://localhost:8080/api/accounts/1/balance -H "Authorization: Bearer $token"
```
✅ **Expected:** Returns balance

**Test 2: Access Other User's Account (Should Fail)**
```powershell
# Still logged in as Alice, try to access Bob's account (ID: 2)
curl http://localhost:8080/api/accounts/2/balance -H "Authorization: Bearer $token"
```
✅ **Expected:** Returns 403 Forbidden with message "You don't own this account"

**Test 3: Access Own User Info (Should Work)**
```powershell
# Access Alice's user info (ID: 1)
curl http://localhost:8080/api/users/1 -H "Authorization: Bearer $token"
```
✅ **Expected:** Returns user details

**Test 4: Access Other User's Info (Should Fail)**
```powershell
# Try to access Bob's info (ID: 2)
curl http://localhost:8080/api/users/2 -H "Authorization: Bearer $token"
```
✅ **Expected:** Returns 403 Forbidden

**Files Modified:**
- `src/main/java/edu/nu/owaspapivulnlab/web/AccountController.java` (lines 42-45, 52-55)
- `src/main/java/edu/nu/owaspapivulnlab/web/UserController.java` (lines 36-39)

---

## **Requirement 4: Data Transfer Objects (DTOs)**

### What was fixed:
- Created DTOs to prevent exposing internal entity structure
- Responses use DTOs instead of JPA entities
- Prevents over-posting and data leakage

### How to verify:

**Test: Check Response Structure**
```powershell
$token = (curl -X POST http://localhost:8080/api/auth/login -H "Content-Type: application/json" -d "{\"username\":\"alice\",\"password\":\"alice123\"}").Content

# Get user info
curl http://localhost:8080/api/users/1 -H "Authorization: Bearer $token"
```

✅ **Expected Response (DTO):**
```json
{
  "id": 1,
  "username": "alice"
}
```

❌ **Before fix (Entity):**
```json
{
  "id": 1,
  "username": "alice",
  "password": "$2a$10$...",
  "email": "alice@example.com",
  "role": "USER",
  "isAdmin": false,
  "accounts": [...]
}
```

**Files Created:**
- `src/main/java/edu/nu/owaspapivulnlab/web/dto/UserDto.java`
- `src/main/java/edu/nu/owaspapivulnlab/web/dto/AccountDto.java`
- `src/main/java/edu/nu/owaspapivulnlab/web/dto/CreateUserRequest.java`
- `src/main/java/edu/nu/owaspapivulnlab/web/dto/TransferRequest.java`

---

## **Requirement 5: Rate Limiting**

### What was fixed:
- Transfer endpoint limited to 5 requests per minute per user
- Uses Bucket4j library
- Returns 429 Too Many Requests when limit exceeded

### How to verify:

**Method 1: Using test-api.html**
1. Open test-api.html in browser
2. Login as alice
3. Click "Transfer 6 Times (Test Rate Limit)" button
4. ✅ **Expected:** First 5 succeed, 6th returns 429

**Method 2: Using PowerShell Script**
```powershell
# Login
$token = (curl -X POST http://localhost:8080/api/auth/login -H "Content-Type: application/json" -d "{\"username\":\"alice\",\"password\":\"alice123\"}").Content

# Send 6 transfer requests rapidly
for ($i=1; $i -le 6; $i++) {
    Write-Host "Request $i"
    curl -X POST http://localhost:8080/api/accounts/transfer `
         -H "Authorization: Bearer $token" `
         -H "Content-Type: application/json" `
         -d "{\"fromAccountId\":1,\"toAccountId\":2,\"amount\":1}"
    Start-Sleep -Milliseconds 100
}
```

✅ **Expected Output:**
- Requests 1-5: Status 200 (Success)
- Request 6: Status 429 (Too Many Requests)

**Files Modified:**
- `src/main/java/edu/nu/owaspapivulnlab/config/RateLimitConfig.java` (created)
- `src/main/java/edu/nu/owaspapivulnlab/web/AccountController.java` (lines 50-59)
- `pom.xml` (added Bucket4j dependency)

---

## **Requirement 6: Mass Assignment Prevention**

### What was fixed:
- CreateUserRequest DTO only accepts username, password, email
- Server-side code controls role and isAdmin fields
- Users cannot elevate privileges via API

### How to verify:

**Test 1: Try to Create Admin User (Should Fail)**
```powershell
# Try to signup with admin role
curl -X POST http://localhost:8080/api/auth/signup `
     -H "Content-Type: application/json" `
     -d "{\"username\":\"hacker\",\"password\":\"hack123\",\"email\":\"hack@evil.com\",\"role\":\"ADMIN\",\"isAdmin\":true}"
```

✅ **Expected:** User is created but with default USER role (not ADMIN)

**Test 2: Verify New User is Not Admin**
```powershell
# Login as the new user
$token = (curl -X POST http://localhost:8080/api/auth/login -H "Content-Type: application/json" -d "{\"username\":\"hacker\",\"password\":\"hack123\"}").Content

# Try to access admin endpoint
curl http://localhost:8080/api/admin/users -H "Authorization: Bearer $token"
```

✅ **Expected:** Returns 403 Forbidden (not admin)

**Files Modified:**
- `src/main/java/edu/nu/owaspapivulnlab/web/dto/CreateUserRequest.java` (only username, password, email)
- `src/main/java/edu/nu/owaspapivulnlab/web/UserController.java` (lines 61-66)

---

## **Requirement 7: JWT Hardening (Issuer & Audience)**

### What was fixed:
- JWT tokens include issuer claim
- JWT tokens include audience claim
- Tokens validated for correct issuer and audience
- Token expiry set to 1 hour (not 10 hours)

### How to verify:

**Test 1: Decode JWT Token**
```powershell
# Login and get token
$token = (curl -X POST http://localhost:8080/api/auth/login -H "Content-Type: application/json" -d "{\"username\":\"alice\",\"password\":\"alice123\"}").Content

# Display token
Write-Host $token
```

Visit https://jwt.io and paste the token

✅ **Expected Payload:**
```json
{
  "sub": "alice",
  "iss": "owasp-api-vuln-lab",
  "aud": "owasp-api-client",
  "exp": [1 hour from now],
  "iat": [current time],
  "role": "USER"
}
```

**Test 2: Token Expiry**
- Wait 1 hour
- Try to use the token
- ✅ **Expected:** Returns 401 Unauthorized (token expired)

**Files Modified:**
- `src/main/java/edu/nu/owaspapivulnlab/service/JwtService.java` (lines 28-32)
- `src/main/java/edu/nu/owaspapivulnlab/config/SecurityConfig.java` (lines 88-107)
- `src/main/resources/application.properties` (lines 10-12)

---

## **Requirement 8: Reduced Error Verbosity**

### What was fixed:
- Generic error messages that don't expose internals
- Stack traces not sent to client
- server.error.include-* properties set to never

### How to verify:

**Test 1: Trigger an Error**
```powershell
# Try invalid account ID
$token = (curl -X POST http://localhost:8080/api/auth/login -H "Content-Type: application/json" -d "{\"username\":\"alice\",\"password\":\"alice123\"}").Content

curl http://localhost:8080/api/accounts/999/balance -H "Authorization: Bearer $token"
```

✅ **Expected:** Generic error message (no stack trace, no internal details)
```
Account not found
```

❌ **Before fix:** Would show full stack trace with file paths, line numbers

**Test 2: Check Application Properties**
```powershell
cat src/main/resources/application.properties | Select-String "error"
```

✅ **Expected:**
```
server.error.include-message=never
server.error.include-binding-errors=never
server.error.include-stacktrace=never
server.error.include-exception=never
```

**Files Modified:**
- `src/main/java/edu/nu/owaspapivulnlab/web/GlobalErrorHandler.java`
- `src/main/resources/application.properties` (lines 14-17)

---

## **Requirement 9: Input Validation**

### What was fixed:
- @Positive validation on transfer amounts
- Spring Validation enabled
- Negative or zero amounts rejected

### How to verify:

**Test: Try Negative Transfer Amount**
```powershell
$token = (curl -X POST http://localhost:8080/api/auth/login -H "Content-Type: application/json" -d "{\"username\":\"alice\",\"password\":\"alice123\"}").Content

# Try to transfer negative amount
curl -X POST http://localhost:8080/api/accounts/transfer `
     -H "Authorization: Bearer $token" `
     -H "Content-Type: application/json" `
     -d "{\"fromAccountId\":1,\"toAccountId\":2,\"amount\":-100}"
```

✅ **Expected:** Returns 400 Bad Request with validation error

**Test: Try Zero Amount**
```powershell
curl -X POST http://localhost:8080/api/accounts/transfer `
     -H "Authorization: Bearer $token" `
     -H "Content-Type: application/json" `
     -d "{\"fromAccountId\":1,\"toAccountId\":2,\"amount\":0}"
```

✅ **Expected:** Returns 400 Bad Request

**Files Modified:**
- `src/main/java/edu/nu/owaspapivulnlab/web/dto/TransferRequest.java` (line 6: @Positive)
- `pom.xml` (spring-boot-starter-validation dependency)

---

## **Requirement 10: Secure Configuration**

### What was fixed:
- JWT secret externalized to environment variable
- Issuer and audience configurable
- Sensitive defaults removed

### How to verify:

**Test 1: Check application.properties**
```powershell
cat src/main/resources/application.properties | Select-String "jwt"
```

✅ **Expected:**
```
app.jwt.secret=${JWT_SECRET:defaultSecretKeyForDevelopmentOnly}
app.jwt.issuer=owasp-api-vuln-lab
app.jwt.audience=owasp-api-client
```

**Test 2: Verify Environment Variable Support**
```powershell
# Set custom JWT secret
$env:JWT_SECRET = "MyCustomSecretKey123456789012345678901234567890"

# Restart application
# New tokens will use custom secret
```

**Files Modified:**
- `src/main/resources/application.properties` (lines 10-12)

---

## 📊 Complete Verification Summary

### Quick Verification Checklist:

- [ ] **BCrypt**: Check H2 console - passwords are hashed
- [ ] **Security**: Root path returns 403, auth paths are public
- [ ] **Ownership**: Alice can't access Bob's account
- [ ] **DTOs**: Response doesn't include password/email fields
- [ ] **Rate Limit**: 6th transfer request returns 429
- [ ] **Mass Assignment**: New user can't set admin role
- [ ] **JWT Hardening**: Token has iss, aud claims at jwt.io
- [ ] **Error Reduction**: Errors don't show stack traces
- [ ] **Validation**: Negative transfer amount rejected
- [ ] **Config**: JWT secret uses environment variable

### Automated Test:

Run all tests at once:
```powershell
.\mvnw.cmd test
```

### Files Changed Summary:

**Created Files (9):**
- RateLimitConfig.java
- AccountDto.java
- UserDto.java
- CreateUserRequest.java
- TransferRequest.java
- SECURITY_FIXES_SUMMARY.md
- QUICK_START.md
- SUCCESS_SUMMARY.md
- test-api.html

**Modified Files (8):**
- DataSeeder.java
- SecurityConfig.java
- JwtService.java
- AccountController.java
- UserController.java
- AuthController.java
- GlobalErrorHandler.java
- application.properties
- pom.xml

---

## 🎯 Final Verification

### All 10 Requirements Status:

1. ✅ **BCrypt Password Hashing** - IMPLEMENTED
2. ✅ **Hardened SecurityFilterChain** - IMPLEMENTED
3. ✅ **Ownership Enforcement** - IMPLEMENTED
4. ✅ **Data Transfer Objects** - IMPLEMENTED
5. ✅ **Rate Limiting** - IMPLEMENTED
6. ✅ **Mass Assignment Prevention** - IMPLEMENTED
7. ✅ **JWT Hardening** - IMPLEMENTED
8. ✅ **Reduced Error Verbosity** - IMPLEMENTED
9. ✅ **Input Validation** - IMPLEMENTED
10. ✅ **Secure Configuration** - IMPLEMENTED

**Project Status: 🎉 ALL REQUIREMENTS FULFILLED**

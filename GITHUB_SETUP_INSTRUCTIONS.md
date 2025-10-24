# GitHub Setup Instructions

## Step 1: Create GitHub Repository

1. Go to https://github.com/new
2. Repository name: `owasp-api-vuln-lab`
3. Description: "OWASP API Security Top 10 - Vulnerability Lab with Fixes"
4. **Important:** Do NOT initialize with README (you already have one)
5. Click "Create repository"

---

## Step 2: Add Remote and Push Code

Copy your repository URL and run these commands in PowerShell:

```powershell
# Navigate to project directory (if not already there)
cd "E:\University\Semester 7\SSD Theory\Assignment 03\owasp-api-vuln-lab"

# Add GitHub remote (replace YOUR_USERNAME with your GitHub username)
git remote add origin https://github.com/YOUR_USERNAME/owasp-api-vuln-lab.git

# Push vulnerable code (main branch)
git checkout main
git push -u origin main

# Push fixed code (fix branch)
git checkout fix/api-security-hardening
git push -u origin fix/api-security-hardening
```

---

## Step 3: Create Pull Request

1. Go to your GitHub repository: `https://github.com/YOUR_USERNAME/owasp-api-vuln-lab`
2. Click on "Pull requests" tab
3. Click "New pull request"
4. Set:
   - **Base:** `main` (vulnerable code)
   - **Compare:** `fix/api-security-hardening` (fixed code)
5. Click "Create pull request"
6. Fill in the PR details:

### PR Title:
```
Security Fixes: OWASP API Top 10 Vulnerabilities Remediation
```

### PR Description:
```markdown
## Summary
This PR implements comprehensive security fixes for all 10 identified OWASP API Security vulnerabilities.

## Fixes Implemented

### ✅ Fix 1: BCrypt Password Hashing
- Replaced plaintext passwords with BCrypt hashing
- Updated signup and login flows
- Migrated existing seed data

### ✅ Fix 2: Hardened SecurityFilterChain
- Restricted public endpoints to `/api/auth/**` only
- Enforced authentication on all APIs
- Added role-based access control
- Implemented JWT issuer/audience validation

### ✅ Fix 3: Ownership Enforcement (BOLA Prevention)
- Users can only access their own accounts
- Users can only view/modify their own profile
- Proper authorization checks on all endpoints

### ✅ Fix 4: DTOs for Data Exposure Control
- Created safe DTOs for all responses
- Hidden password, role, and isAdmin fields
- Controlled data exposure across all endpoints

### ✅ Fix 5: Rate Limiting
- Implemented Bucket4j for rate limiting
- Limited transfers to 5 requests/minute per user
- Returns 429 Too Many Requests when exceeded

### ✅ Fix 6: Mass Assignment Prevention
- Server controls role and isAdmin fields
- Explicit DTOs without privilege fields
- Removed user search endpoint

### ✅ Fix 7: Hardened JWT
- Strong secret from environment variable
- Reduced token TTL from 30 days to 1 hour
- Added issuer and audience validation
- Strict signature verification

### ✅ Fix 8: Reduced Error Details
- Generic error messages to clients
- Detailed logging server-side only
- Disabled stack traces in production

### ✅ Fix 9: Input Validation
- Positive amounts only for transfers
- Maximum transfer limit (1,000,000)
- Insufficient funds validation

### ✅ Fix 10: Integration Tests
- Comprehensive security test suite
- Tests all security requirements
- Prevents regression

## Build Status
✅ **BUILD SUCCESS** (Java 21, Spring Boot 3.3.4)

## Testing
- All security fixes verified
- Integration tests implemented
- Manual testing guide provided

## Files Changed
- 21 files modified
- 13 new files created
- 10 separate commits (one per fix)

## Documentation
- Comprehensive inline code comments
- Security fixes summary document
- Verification checklist
- Testing tools included
```

7. Click "Create pull request"
8. **DO NOT MERGE IT** - Leave it open for review

---

## Step 4: Update Submission Documents

After creating the PR, update the following files with your actual GitHub URLs:

### In `OWASP_Security_Report.md`:
Replace `[YOUR_USERNAME]` with your GitHub username:
```markdown
**Project Repository:** https://github.com/YOUR_USERNAME/owasp-api-vuln-lab
```

### Create `GitHub_Links.txt`:
```powershell
@"
GitHub Repository: https://github.com/YOUR_USERNAME/owasp-api-vuln-lab

Branches:
- Vulnerable Code: https://github.com/YOUR_USERNAME/owasp-api-vuln-lab/tree/main
- Fixed Code: https://github.com/YOUR_USERNAME/owasp-api-vuln-lab/tree/fix/api-security-hardening

Pull Request: https://github.com/YOUR_USERNAME/owasp-api-vuln-lab/pull/1

Commits (All 10 Fixes):
- https://github.com/YOUR_USERNAME/owasp-api-vuln-lab/commits/fix/api-security-hardening
"@ | Out-File -FilePath "GitHub_Links.txt" -Encoding UTF8
```

---

## Step 5: Verify Everything is Pushed

```powershell
# Check remote is configured
git remote -v

# Check all commits are pushed
git checkout fix/api-security-hardening
git log --oneline

# Verify on GitHub
# Go to: https://github.com/YOUR_USERNAME/owasp-api-vuln-lab/commits/fix/api-security-hardening
# You should see all 11 commits (1 baseline + 10 fixes)
```

---

## Expected Commit History on GitHub

```
✅ fix(10): add integration tests and comprehensive documentation
✅ fix(9): add input validation - reject negative/excessive transfer amounts
✅ fix(8): prevent mass assignment - server controls role/isAdmin, use explicit DTOs
✅ fix(7): reduce error details in production - proper exception mapping and logging
✅ fix(6): harden JWT - strong secret, 1-hour TTL, issuer/audience validation
✅ fix(5): add rate limiting with Bucket4j - 5 requests/minute on sensitive endpoints
✅ fix(4): implement DTOs to control data exposure - hide password, role, isAdmin fields
✅ fix(3): enforce ownership checks - users can only access their own resources
✅ fix(2): tighten SecurityFilterChain, restrict public routes, add proper JWT validation
✅ fix(1): secure signup + BCrypt hashing + login hash check + seed migration
✅ chore: baseline vulnerable code (original lab)
```

---

## Troubleshooting

### If you get "authentication failed":
1. Use GitHub Personal Access Token instead of password
2. Generate token: GitHub Settings → Developer settings → Personal access tokens → Generate new token
3. Use token as password when pushing

### If you need to change remote URL:
```powershell
git remote set-url origin https://github.com/YOUR_USERNAME/owasp-api-vuln-lab.git
```

---

## Next Steps

After GitHub setup is complete:
1. Convert `OWASP_Security_Report.md` to PDF
2. Create submission ZIP file
3. Submit assignment

See `SUBMISSION_GUIDE.md` for final steps.

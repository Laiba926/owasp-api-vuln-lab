# Assignment Submission Guide

## 📋 Required Deliverables

1. ✅ **PDF Report** - Identified vulnerabilities and fixes
2. ✅ **GitHub Repository** with:
   - Vulnerable code branch (`main`)
   - Fixed code branch (`fix/api-security-hardening`)
   - Pull Request (from fixed to vulnerable)
3. ✅ **Code Comments** - Each fix commented in code
4. ✅ **ZIP File** - Contains PDF and GitHub links

---

## 🚀 Quick Submission Checklist

- [x] All 10 security fixes implemented
- [x] All fixes committed separately to Git
- [x] Code comments added for each fix
- [x] Report document created
- [ ] GitHub repository created
- [ ] Code pushed to GitHub
- [ ] Pull Request created on GitHub
- [ ] Report converted to PDF
- [ ] Submission ZIP created
- [ ] Assignment submitted

---

## Step-by-Step Submission Process

### **STEP 1: GitHub Setup** ⏳ (DO THIS FIRST)

Follow the instructions in `GITHUB_SETUP_INSTRUCTIONS.md`:

1. Create GitHub repository: `owasp-api-vuln-lab`
2. Push both branches (main and fix/api-security-hardening)
3. Create Pull Request
4. Copy your GitHub URLs

**Your GitHub URLs will be:**
```
Repository: https://github.com/YOUR_USERNAME/owasp-api-vuln-lab
Vulnerable Branch: https://github.com/YOUR_USERNAME/owasp-api-vuln-lab/tree/main
Fixed Branch: https://github.com/YOUR_USERNAME/owasp-api-vuln-lab/tree/fix/api-security-hardening
Pull Request: https://github.com/YOUR_USERNAME/owasp-api-vuln-lab/pull/1
```

---

### **STEP 2: Convert Report to PDF** 📄

#### Option A: Using VS Code Extension (Recommended)
1. Open `OWASP_Security_Report.md` in VS Code
2. Install extension: "Markdown PDF" by yzane
3. Right-click in the file → "Markdown PDF: Export (pdf)"
4. PDF will be saved in the same folder

#### Option B: Using Online Converter
1. Go to https://www.markdowntopdf.com/
2. Upload `OWASP_Security_Report.md`
3. Download the generated PDF
4. Save as `OWASP_Security_Report.pdf`

#### Option C: Using Pandoc (if installed)
```powershell
pandoc OWASP_Security_Report.md -o OWASP_Security_Report.pdf --pdf-engine=wkhtmltopdf
```

**Important:** After converting to PDF:
1. Open the PDF and verify it looks good
2. Check all sections are included
3. Ensure code blocks are readable
4. Update the GitHub repository URL with your actual username

---

### **STEP 3: Create GitHub Links File** 📝

After setting up GitHub and creating PR, run this command (replace YOUR_USERNAME with your actual GitHub username):

```powershell
@"
==============================================
OWASP API SECURITY ASSIGNMENT - GITHUB LINKS
==============================================

Student: [Your Name Here]
Course: SSD Theory - Assignment 03
Date: October 25, 2025

----------------------------------------------
GITHUB REPOSITORY
----------------------------------------------
Repository URL:
https://github.com/YOUR_USERNAME/owasp-api-vuln-lab

----------------------------------------------
BRANCHES
----------------------------------------------
1. Vulnerable Code (Baseline):
   https://github.com/YOUR_USERNAME/owasp-api-vuln-lab/tree/main

2. Fixed Code (Security Hardening):
   https://github.com/YOUR_USERNAME/owasp-api-vuln-lab/tree/fix/api-security-hardening

----------------------------------------------
PULL REQUEST (Fixed → Vulnerable)
----------------------------------------------
PR URL:
https://github.com/YOUR_USERNAME/owasp-api-vuln-lab/pull/1

PR Title:
Security Fixes: OWASP API Top 10 Vulnerabilities Remediation

----------------------------------------------
COMMIT HISTORY (All 10 Fixes)
----------------------------------------------
View all commits:
https://github.com/YOUR_USERNAME/owasp-api-vuln-lab/commits/fix/api-security-hardening

Individual Commits:
1. fix(1): secure signup + BCrypt hashing + login hash check + seed migration
2. fix(2): tighten SecurityFilterChain, restrict public routes, add proper JWT validation
3. fix(3): enforce ownership checks - users can only access their own resources
4. fix(4): implement DTOs to control data exposure - hide password, role, isAdmin fields
5. fix(5): add rate limiting with Bucket4j - 5 requests/minute on sensitive endpoints
6. fix(6): harden JWT - strong secret, 1-hour TTL, issuer/audience validation
7. fix(7): reduce error details in production - proper exception mapping and logging
8. fix(8): prevent mass assignment - server controls role/isAdmin, use explicit DTOs
9. fix(9): add input validation - reject negative/excessive transfer amounts
10. fix(10): add integration tests and comprehensive documentation

----------------------------------------------
BUILD & TEST STATUS
----------------------------------------------
✅ Build: SUCCESS
✅ Java Version: 21.0.8 LTS
✅ Spring Boot: 3.3.4
✅ All 10 Fixes: IMPLEMENTED
✅ Integration Tests: INCLUDED
✅ Documentation: COMPLETE

----------------------------------------------
CODE REVIEW NOTES
----------------------------------------------
- Each fix committed separately for easy review
- Inline code comments explain each security fix
- DTOs created for safe data exposure
- Rate limiting implemented with Bucket4j
- JWT hardened with proper validation
- Comprehensive testing included

----------------------------------------------
HOW TO REVIEW THE FIXES
----------------------------------------------
1. View the Pull Request to see all changes at once
2. Review individual commits for detailed changes
3. Check inline code comments (marked with // FIX-X:)
4. Run the application and test with test-api.html
5. Review SECURITY_FIXES_SUMMARY.md for documentation

----------------------------------------------
TESTING THE APPLICATION
----------------------------------------------
1. Clone repository:
   git clone https://github.com/YOUR_USERNAME/owasp-api-vuln-lab.git
   
2. Checkout fixed branch:
   git checkout fix/api-security-hardening
   
3. Run application:
   mvnw.cmd spring-boot:run
   
4. Test in browser:
   Open test-api.html in browser
   
5. Run tests:
   mvnw.cmd test

==============================================
END OF GITHUB LINKS DOCUMENT
==============================================
"@ | Out-File -FilePath "GitHub_Links.txt" -Encoding UTF8

Write-Host "✅ GitHub_Links.txt created successfully!" -ForegroundColor Green
Write-Host "📝 Don't forget to replace YOUR_USERNAME with your actual GitHub username!" -ForegroundColor Yellow
```

---

### **STEP 4: Create Submission ZIP** 📦

```powershell
# Create submission folder
New-Item -ItemType Directory -Path "submission" -Force

# Copy PDF report
Copy-Item "OWASP_Security_Report.pdf" -Destination "submission/" -ErrorAction SilentlyContinue

# Copy GitHub links
Copy-Item "GitHub_Links.txt" -Destination "submission/"

# Create a README for the submission
@"
OWASP API SECURITY ASSIGNMENT SUBMISSION
=========================================

This ZIP contains:
1. OWASP_Security_Report.pdf - Comprehensive report with all 10 vulnerabilities and fixes
2. GitHub_Links.txt - Links to repository, branches, and pull request

Please review:
- The PDF report for detailed documentation
- The GitHub repository for complete source code
- The Pull Request to see all changes with code review

All 10 security fixes have been implemented and tested successfully.

Build Status: ✅ SUCCESS (Java 21, Spring Boot 3.3.4)
"@ | Out-File -FilePath "submission/README.txt" -Encoding UTF8

# Create ZIP file
Compress-Archive -Path "submission/*" -DestinationPath "OWASP_API_Security_Assignment_Submission.zip" -Force

Write-Host ""
Write-Host "✅ Submission ZIP created successfully!" -ForegroundColor Green
Write-Host ""
Write-Host "📦 ZIP File: OWASP_API_Security_Assignment_Submission.zip" -ForegroundColor Cyan
Write-Host ""
Write-Host "📋 Contents:" -ForegroundColor Yellow
Write-Host "   - OWASP_Security_Report.pdf" -ForegroundColor White
Write-Host "   - GitHub_Links.txt" -ForegroundColor White
Write-Host "   - README.txt" -ForegroundColor White
Write-Host ""
Write-Host "⚠️  IMPORTANT: Make sure you have:" -ForegroundColor Red
Write-Host "   1. Created GitHub repository" -ForegroundColor White
Write-Host "   2. Pushed both branches" -ForegroundColor White
Write-Host "   3. Created Pull Request" -ForegroundColor White
Write-Host "   4. Updated GitHub_Links.txt with YOUR username" -ForegroundColor White
Write-Host "   5. Converted report to PDF" -ForegroundColor White
Write-Host ""
```

---

### **STEP 5: Verify Before Submission** ✔️

Before submitting, double-check:

#### GitHub Checklist:
- [ ] Repository created on GitHub
- [ ] Both branches pushed (main and fix/api-security-hardening)
- [ ] Pull Request created and open (NOT merged)
- [ ] All 11 commits visible on GitHub (1 baseline + 10 fixes)
- [ ] Repository is public or accessible to instructor

#### ZIP File Checklist:
- [ ] PDF report is readable and complete
- [ ] GitHub links are correct (not placeholder URLs)
- [ ] Your name is in the report
- [ ] All 10 vulnerabilities documented
- [ ] All 10 fixes documented
- [ ] Code snippets included in report

#### Quick Test:
```powershell
# Test the ZIP file
Expand-Archive -Path "OWASP_API_Security_Assignment_Submission.zip" -DestinationPath "test_submission" -Force
Get-ChildItem "test_submission" -Recurse
# Should show: README.txt, GitHub_Links.txt, and OWASP_Security_Report.pdf
```

---

## 📧 Submission Email Template (if required)

```
Subject: SSD Theory Assignment 03 - OWASP API Security Submission

Dear [Instructor Name],

Please find attached my submission for Assignment 03 - OWASP API Security Vulnerabilities.

Submission Contents:
- PDF Report: Comprehensive documentation of all 10 vulnerabilities and fixes
- GitHub Links: Repository URL, branches, and pull request

GitHub Repository:
https://github.com/YOUR_USERNAME/owasp-api-vuln-lab

Key Deliverables:
✅ All 10 OWASP API security vulnerabilities identified and fixed
✅ Separate Git commits for each fix with detailed comments
✅ Pull Request created for code review
✅ Integration tests implemented
✅ Comprehensive documentation included
✅ Successfully built with Java 21 and Spring Boot 3.3.4

Please let me know if you need any clarification or additional information.

Best regards,
[Your Name]
[Student ID]
```

---

## 🎯 Final Checklist

Print this and check off as you complete each step:

```
GITHUB SETUP
[ ] Created repository on GitHub
[ ] Added remote: git remote add origin https://github.com/...
[ ] Pushed main branch: git push -u origin main
[ ] Pushed fix branch: git push -u origin fix/api-security-hardening
[ ] Created Pull Request on GitHub
[ ] Verified all 11 commits are visible

REPORT PREPARATION
[ ] Reviewed OWASP_Security_Report.md
[ ] Updated with your GitHub username
[ ] Converted to PDF
[ ] PDF is readable and complete
[ ] All sections included

GITHUB LINKS
[ ] Created GitHub_Links.txt
[ ] Updated with YOUR GitHub username
[ ] All URLs are correct
[ ] Tested URLs in browser

SUBMISSION PACKAGE
[ ] Ran PowerShell script to create ZIP
[ ] ZIP contains: PDF + GitHub_Links.txt + README.txt
[ ] Extracted and verified ZIP contents
[ ] File size is reasonable (should be < 5 MB)

FINAL VERIFICATION
[ ] Opened PDF - looks professional
[ ] Clicked GitHub links - all work
[ ] Read GitHub_Links.txt - accurate
[ ] Pull Request is visible on GitHub
[ ] Code has comments for each fix
[ ] Built successfully with Java 21

SUBMIT
[ ] Uploaded ZIP to assignment portal
[ ] OR sent email with attachment
[ ] Confirmed submission received
```

---

## 🆘 Troubleshooting

### "I don't have a GitHub account"
1. Go to https://github.com/signup
2. Create free account
3. Verify email
4. Follow GITHUB_SETUP_INSTRUCTIONS.md

### "I can't convert Markdown to PDF"
1. Install VS Code extension "Markdown PDF"
2. Or use online: https://www.markdowntopdf.com/
3. Or ask for help in converting the .md file

### "Git push authentication failed"
1. Use Personal Access Token instead of password
2. Generate: GitHub Settings → Developer settings → Personal access tokens
3. Use token as password when pushing

### "I accidentally merged the Pull Request"
Don't worry! You can:
1. Create a new PR from the same branches
2. Or just submit the repository - the commit history is still there

---

## 📞 Need Help?

If you encounter any issues:
1. Check error messages carefully
2. Review GITHUB_SETUP_INSTRUCTIONS.md
3. Test each step individually
4. Verify Git commands output
5. Check GitHub repository visibility

---

## ✅ You're Done When...

You have:
1. ✅ GitHub repository with both branches pushed
2. ✅ Open Pull Request visible on GitHub
3. ✅ PDF report with all 10 fixes documented
4. ✅ ZIP file with PDF + GitHub links
5. ✅ Submitted ZIP file to instructor

**Congratulations! Your assignment is complete! 🎉**

---

**Good luck with your submission!** 🚀

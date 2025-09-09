# Trivy codee or job that scans your repo for security issues and uploads reports.
## .gitlab-ci.yml
##( But will work with any CI tool like Jenkins and Others)
```

trivy_fs_scan:
  stage: security
  script:
    - echo "🔍 Running Trivy File Scan"
    # Download HTML template for pretty report
    - curl -sSL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/html.tpl -o html.tpl

    # Generate an HTML vulnerability report
    - trivy fs --format template --template "@html.tpl" -o trivy-fs-report.html .

    # Generate GitLab-compatible JSON report for Security Dashboard
    - trivy fs --format gitlab -o gl-trivy-report.json .

    # Security gate: fail pipeline if CRITICAL vulnerabilities are found
    - trivy fs --exit-code 1 --severity CRITICAL .
  artifacts:
    paths:
      - trivy-fs-report.html   # Human-readable HTML report
      - gl-trivy-report.json   # GitLab Security Dashboard report
    expire_in: 7 days
```
---
**Note :**  ***( This is job code will run successfully but will give exit code if your code has critical vulnerabilities )***   ***#we ran it on gitlab where we installed trivy on runner***

**Allow pipeline to pass even if CRITICAL vulns exist** 
```
    - trivy fs --exit-code 0 --severity CRITICAL .
```
**Just replace with exit-code=1 to 0 if found**
---

**Once Done You can download the artifact or zip file ------> Extract ------> Open html file -------> Share with Developers**

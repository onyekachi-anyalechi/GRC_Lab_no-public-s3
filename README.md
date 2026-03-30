# GRC_Lab_no-public-s3
* GRC Lab for security control - exposed S3 Bucket
* Lab: No Public S3 Buckets - **My Implementation**
* Author: **Onyekachi A**
* LinkedIn: https://www.linkedin.com/in/onyekachiso/
* Completion Date: March 29, 2026
* Inspired by: ashpearce/GRC-Playground by Ashley Pearce: https://github.com/ashpearce/GRC-Playground 

## Overview
Inspired by Ashley Pearce, I built a Policy-as-Code security check using Rego + Conftest in GitHub Codespaces to prevent public AWS S3 buckets—a critical cloud misconfiguration.

## Learning Outcomes:
* Wrote a custom Rego policy to deny risky S3 configs
* Tested with Conftest in a cloud environment
* Automated GRC compliance check
* Mapped to NIST 800-53 controls

### Repository Structure
GRC-Lab-no-public-s3/
- conftest.toml       # Points to ./policy
- input.json         # Test data (public → private)
- policy/
  - input.rego     # My deny policy

### Step-by-Step Implementation
1. GitHub Repo Setup (Web)
1. github.com → New repo: "GRC-Lab-no-public-s3" (public, empty)
2. Add file:
    - input.json
```
    {
      "resource_type": "aws_s3_bucket",
      "acl": "public-read"
    }
```
4. Add file: policy/input.rego
text
package s3policy

deny[message] {
  input.resource_type == "aws_s3_bucket"
  input.acl == "public-read"
  message := "S3 buckets cannot be publicly readable (acl: public-read)"
}
text
4. Add root: conftest.toml
policy = "./policy"
2. Codespaces Environment
text
Code → Open with Codespaces → Create codespace (free 60hr/mo)
3. Conftest Install (Codespaces Terminal)
bash
mkdir -p ~/.local/bin
wget https://github.com/open-policy-agent/conftest/releases/download/v0.58.0/conftest_0.58.0_Linux_x86_64.tar.gz
tar -xzf conftest*.tar.gz && mv conftest ~/.local/bin/
export PATH="$HOME/.local/bin:$PATH"
4. Test Execution (From repo root)
bash
**FAIL test (public bucket)**
conftest test input.json --all-namespaces
# FAIL - input.json - S3 buckets cannot be publicly readable

# PASS test (fixed in the input.json file)
{
  "resource_type": "aws_s3_bucket",
  "acl": "private"
}
conftest test input.json --all-namespaces  
# PASS - input.json ✓

What Each Component Does
File	Purpose
input.rego	Rego policy denies aws_s3_bucket with public-read ACL
conftest.toml	Configures ./policy as policy directory
input.json	Simulates Terraform S3 config for testing
--all-namespaces	Enables package s3policy (not just main)
Control Mapping (NIST 800-53)
Control	How This Lab Addresses
AC-6(10)	Least privilege: Blocks over-privileged public access automatically
SC-12	Public access prevention: Eliminates data exposure risk pre-deployment
Key Learnings
Policy-as-Code = GRC automation (not spreadsheets)

Rego queries infrastructure like SQL queries data

Conftest = compliance testing CLI (CI/CD integration ready)

GitHub Codespaces = zero-setup learning environment

Shift Left: Catch misconfigs before they deploy

Troubleshooting (Real Experience)
text
❌ conftest: command not found → Install binary to PATH
❌ No policies found → Use --all-namespaces for custom packages  
❌ Path errors → Run from repo root (conftest.toml location)

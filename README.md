# Adobe Security – Take-Home Exercise  

**Role target:** Product Security Engineer – Edge / WAF Focus  
**Effort cap:** 8 focused hours  
**Deadline:** Submit within **72 hours** of receiving the repo invite  

---

## 1. Scenario
A new public site—**OWASP Juice Shop**—must launch next week. Before DNS cut-over, we need proof that edge security is:

1. **Repeatable** – any future service can inherit the same guardrails in minutes.  
2. **Rapidly tunable** – you can ship an emergency WAF rule in **\< 30 min**.  
3. **Measurable** – One KPI shows how well the edge is protecting the service.

---

## 2. Deliverables

### 0 – Service & Front Door  
* Deploy **OWASP Juice Shop** (`ghcr.io/juice-shop/juice-shop`) on **one** of:  
  * ECS Fargate
  * EC2
  * EKS  
  * Lambda container + ALB  
* Expose it through **ALB** *or* **CloudFront**.  
* Declare **all** infrastructure with Terraform *or* AWS CDK.

### 1 – Reusable WAF Module  
* Create an `edge_waf` module that  
  * builds an **AWS WAF v2 WebACL**,  
  * attaches it to the front door from step 0 via **one variable**,  
  * enables **≥ 2 AWS-managed rule groups** **and** **≥ 1 custom rule** blocking the Juice Shop SQLi payload  
    (`' OR 1=1--` against `/rest/products/search`).

### 2 – CI/CD Guardrail  
* GitHub Actions workflow (`.github/workflows/edge-ci.yml`) that  
  * runs `tfsec` *or* `cdk-nag`,  
  * posts the plan as a PR comment,  
  * requires reviewer approval before `apply` / `deploy`.

### 3 – Rapid-Mitigation Script  
* `push_block.py` (or `.sh`) that  
  * accepts an IP/CIDR *or* URI regex,  
  * creates a **temporary** WAF rule with a **1-hour TTL** (auto-cleanup),  
  * finishes in **≤ 60 s** wall time.

### 4 – Smoke Test  
* A script or Postman collection that  
  * sends a benign request → **200 OK**,  
  * sends the SQLi payload → **403** (blocked),  
  * Prints both results.

### 5 – Log Pipeline & KPI  
* Enable **WAF logging → Kinesis Firehose → S3** (same account).  
* Create an **Athena table** on that S3 prefix.  
* Supply **one query** that returns:  
  * `total_requests`  
  * `blocked_requests`  
  * `%blocked`  
  * `top_5_attack_vectors` (group by rule label)  
* Add ≤ 200 words explaining how this KPI supports MTTR & false-positive tuning.  
* **No AWS account?** Use the `/logs/sample/` JSON files provided in the starter repo.

### 6 – README / Runbook  
* ≤ 2-page `README.md` covering  
  * prerequisites & variable setup,  
  * `make deploy` (or similar) to stand everything up in **< 15 min**,  
  * how to run `push_block.py` and the smoke test,  
  * How to execute the Athena query and view results.

**Stretch goals (optional)**  
* Roll WebACLs out via **AWS Firewall Manager**.  
* Add unit tests with Terratest or pytest-cdk.  
* Export the KPI to CloudWatch and display it in a one-panel dashboard.

---

## 3. Submission Process
1. Create a GitHub repo
2. Commit early & often—**we review your git history**.  
3. Open a PR to `main` that includes code, workflows, screenshots/CLI output of the smoke test, KPI results, and about 20 prompts if you used AI.  

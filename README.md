# Terraform Security Scan with Trivy in Azure DevOps

This repository demonstrates a DevOps pipeline for running Terraform validation and static security scanning using [Trivy](https://github.com/aquasecurity/trivy) in Azure DevOps.

## 📦 Features

- **Terraform Init, Validate, Plan**
- **Trivy Security Scan** on Terraform configurations
- Generates `JSON` and `HTML` scan reports
- Publishes reports as pipeline artifacts
- Includes manual approval stage for governance

---

## 🛠️ Prerequisites

- Azure DevOps Self-hosted Agent
- Terraform CLI installed on the agent
- Trivy CLI installed on the agent
- Azure service connection configured in Azure DevOps
- A backend storage account for Terraform state

---


---

## 🚀 Pipeline Stages

### 1. `Terraform_Validation`
- Runs `terraform init`, `validate`, and `plan`
- Uses remote backend (Azure Storage)

### 2. `Trivy_Security_Scan`
- Runs Trivy on Terraform code
- Outputs:
  - `trivy-report.json` – Raw scan data
  - `trivy-report.html` – Human-readable scan report using `.tpl` template

### 3. `Manual_Approval`
- Manual intervention step before proceeding to deployment or merge

---

## 📄 Sample Trivy Command Used

```bash
trivy config --severity HIGH,CRITICAL \
  --format template \
  --template "@/path/to/trivy-html.tpl" \
  --output ./reports/trivy-report.html .


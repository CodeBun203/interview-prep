# Tutorial: Terraform (Infrastructure as Code)

> Goal: manage your real Cloudflare DNS + tunnel config as code. By the end you'll run
> `terraform apply` and see a DNS record created from a file — genuine, production IaC.

## Mental model
**IaC (Infrastructure as Code)** = describe infrastructure in declarative files; the tool figures out
the API calls to make reality match. Terraform reads `.tf` files, compares them to **state**
(`terraform.tfstate`, a record of what it manages), computes a **plan** (the diff), and **applies**
it. Idempotent: re-running with no file change does nothing.

Core nouns: **provider** (plugin for a platform — Cloudflare, AWS, Docker), **resource** (a thing to
manage — a DNS record), **variable**, **output**, **module** (reusable group of resources), **state**.

## Step 1 — install & init
```bash
# install (Ubuntu)
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp.gpg] https://apt.releases.hashicorp.com noble main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
terraform version
```

## Step 2 — your first real config (`infra/main.tf`)
```hcl
terraform {
  required_providers {
    cloudflare = { source = "cloudflare/cloudflare", version = "~> 4" }
  }
}

provider "cloudflare" {
  api_token = var.cloudflare_token        # never hardcode secrets
}

variable "cloudflare_token" { type = string, sensitive = true }
variable "zone_id"          { type = string }   # your washingtonelectric.info zone

# A real DNS record, managed as code:
resource "cloudflare_record" "app" {
  zone_id = var.zone_id
  name    = "app"
  type    = "CNAME"
  content = "4a84b5e6-de8d-4501-88a5-c7338caa9f6e.cfargotunnel.com"  # your tunnel
  proxied = true
}

# Email auth as code — SPF/DKIM/DMARC TXT records:
resource "cloudflare_record" "dmarc" {
  zone_id = var.zone_id
  name    = "_dmarc"
  type    = "TXT"
  content = "v=DMARC1; p=quarantine; rua=mailto:dmarc@washgen.com"
}

output "app_hostname" { value = cloudflare_record.app.hostname }
```

## Step 3 — the workflow (memorize this loop)
```bash
export TF_VAR_cloudflare_token="..."     # provide secrets via env, not files
export TF_VAR_zone_id="..."
terraform init       # download the provider
terraform fmt        # format
terraform validate   # syntax/type check
terraform plan       # SHOW the diff — what will change (read this every time)
terraform apply      # make it real (asks for confirmation)
terraform destroy    # tear down (careful)
```
`terraform plan` is the star of any demo: it prints `+ create`, `~ update`, `- destroy`. **Drift** =
when reality diverges from state (someone clicked in the console); `plan` detects it.

## Step 4 — state, done right
- State holds the mapping between your `.tf` resources and real-world IDs.
- **Never commit state to git** (it can contain secrets). For solo use, a local backend is fine;
  for teams use a **remote backend** (S3 + DynamoDB lock, or Terraform Cloud) so state is shared and
  **locked** during applies.
```hcl
# optional remote backend (ties into your AWS S3 work)
terraform {
  backend "s3" {
    bucket = "washgen-tf-state"
    key    = "cloudflare/terraform.tfstate"
    region = "us-west-2"
  }
}
```

## Step 5 — modules (reusability)
Factor repeated patterns (e.g., "a tunnel-backed subdomain") into a **module**:
```hcl
module "subdomain" {
  source   = "./modules/tunnel-subdomain"
  for_each = toset(["app", "auth", "inventory", "webhooks"])
  name     = each.key
  zone_id  = var.zone_id
}
```
Name-drop: **`for_each`/`count`** (loops), **module composition**, **DRY infrastructure**.

## "Testing" infrastructure code
- **`terraform validate`** + **`terraform plan`** in CI on every PR (catch errors before apply).
- **`tflint`** — linter for Terraform.
- **`terraform-compliance`** / **OPA/Conftest** — policy-as-code tests ("no public S3 buckets").
- **Terratest** (Go) — spins up real infra in a sandbox, asserts, tears down (advanced).
- In GitHub Actions: a `plan` job on PRs, `apply` job gated on merge to `main`.

## Most common providers / tools
| Tool | Purpose |
|---|---|
| `cloudflare/cloudflare` | DNS, tunnels, WAF (your fit) |
| `hashicorp/aws` | S3, IAM, EC2 (pairs with your AWS work) |
| `kreuzwerker/docker` | manage local containers as IaC |
| `tflint`, `terraform fmt` | lint/format |
| `terragrunt` | DRY wrapper for multi-env state |
| `Terratest`, `terraform-compliance` | testing |

## Interview soundbites
- "My Cloudflare DNS, tunnel routes, and email-auth records are in Terraform — every change is a
  reviewed `plan`, applied idempotently, with state in S3 and locking."
- Know: **declarative, provider, resource, state, plan/apply, drift, module, `for_each`, remote
  backend, idempotent.** Contrast with imperative scripts: "I describe desired state; Terraform
  reconciles."

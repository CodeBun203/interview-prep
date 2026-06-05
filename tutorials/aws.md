# Tutorial: AWS (S3 + IAM, via your real backup job)

> Goal: solve your documented 3-2-1 backup gap by pushing encrypted Postgres dumps to S3 — earning
> genuine, defensible AWS experience (S3, IAM, lifecycle, encryption) in ~half a day.

## Mental model
AWS = pay-per-use cloud primitives. You only need a handful to be credible:
- **S3** (Simple Storage Service): object storage — **buckets** hold **objects** (files). Durable
  (11 nines), cheap, versioned. *Not* a filesystem; it's an HTTP key→blob store.
- **IAM** (Identity & Access Management): **users**, **roles**, **policies** (JSON permission docs).
  The whole game is **least privilege** — grant only what's needed.
- **Region** (e.g., `us-west-2`): where data physically lives.

## Step 1 — install & configure the CLI
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o awscli.zip
unzip awscli.zip && sudo ./aws/install
aws configure        # paste access key, secret, region us-west-2
```

## Step 2 — create a least-privilege IAM user (the security story)
In the console (or CLI): create user `washgen-backup` with **programmatic access** and this policy —
note it can only touch ONE bucket, write/list, no delete, no other services:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": ["s3:PutObject", "s3:GetObject", "s3:ListBucket"],
    "Resource": [
      "arn:aws:s3:::washgen-db-backups",
      "arn:aws:s3:::washgen-db-backups/*"
    ]
  }]
}
```
This is the **principle of least privilege** in concrete form — say exactly this in interviews.

## Step 3 — create the bucket with versioning + lifecycle + encryption
```bash
aws s3 mb s3://washgen-db-backups --region us-west-2
aws s3api put-bucket-versioning --bucket washgen-db-backups \
  --versioning-configuration Status=Enabled
# default server-side encryption (SSE-S3):
aws s3api put-bucket-encryption --bucket washgen-db-backups \
  --server-side-encryption-configuration \
  '{"Rules":[{"ApplyServerSideEncryptionByDefault":{"SSEAlgorithm":"AES256"}}]}'
# lifecycle: expire old backups after 90 days (cost control)
aws s3api put-bucket-lifecycle-configuration --bucket washgen-db-backups \
  --lifecycle-configuration '{"Rules":[{"ID":"expire","Status":"Enabled","Filter":{"Prefix":"pg/"},"Expiration":{"Days":90}}]}'
```
Terms earned: **versioning**, **server-side encryption (SSE / SSE-S3 / SSE-KMS)**, **lifecycle
policy**, **object expiry**.

## Step 4 — wire it into your real cron backup
Edit `applications/backups/postgres-backup.sh` to push after the local dump (using **boto3**, already
installed, or the CLI):
```bash
# after pg_dump writes $DUMP_FILE to /mnt/raid1/backups/postgresql/
gpg --symmetric --cipher-algo AES256 --batch --passphrase "$BK_PASS" "$DUMP_FILE"   # client-side encrypt
aws s3 cp "${DUMP_FILE}.gpg" "s3://washgen-db-backups/pg/$(date +%F)/" --storage-class STANDARD_IA
```
Now: local RAID copy + off-site encrypted S3 copy = **true 3-2-1** (3 copies, 2 media, 1 off-site).

### boto3 version (Python, for an app or script)
```python
import boto3
s3 = boto3.client("s3", region_name="us-west-2")
s3.upload_file(dump_path, "washgen-db-backups", f"pg/{date}/{name}.gpg",
               ExtraArgs={"StorageClass": "STANDARD_IA"})
```

## Step 5 — verify a restore (don't be the person whose backups never restore)
```bash
aws s3 ls s3://washgen-db-backups/pg/ --recursive
aws s3 cp s3://washgen-db-backups/pg/2026-06-04/dump.sql.gpg ./restore.gpg
gpg --decrypt restore.gpg > restore.sql
# psql ... < restore.sql   (into a scratch DB)
```
"I test restores, not just backups" is a senior soundbite.

## Writing tests (against AWS code)
- **`moto`** — mocks AWS in-memory for unit tests, no real account:
```python
import boto3, pytest
from moto import mock_aws

@mock_aws
def test_upload():
    s3 = boto3.client("s3", region_name="us-west-2")
    s3.create_bucket(Bucket="washgen-db-backups",
                     CreateBucketConfiguration={"LocationConstraint": "us-west-2"})
    s3.put_object(Bucket="washgen-db-backups", Key="pg/x.gpg", Body=b"data")
    assert s3.get_object(Bucket="washgen-db-backups", Key="pg/x.gpg")["Body"].read() == b"data"
```
- **`localstack`** — a full local AWS emulator (S3, SQS, Lambda…) for integration tests.

## Most common AWS services to *name* (and one line each)
| Service | What it is |
|---|---|
| S3 | object storage (your backup target) |
| IAM | identity & permissions (users/roles/policies) |
| EC2 | virtual machines |
| Lambda | serverless functions (event-driven, no server) |
| RDS | managed relational DB (managed Postgres) |
| SQS / SNS | queue / pub-sub messaging |
| CloudWatch | metrics, logs, alarms (AWS's Prometheus/Grafana) |
| ECR / ECS / EKS | container registry / orchestration / managed K8s |
| KMS | managed encryption keys |
| VPC | virtual private network |
| Route 53 | DNS |

## Common libraries / tools
- **`boto3`** (Python SDK — already on your box), **AWS CLI**, **`moto`** + **`localstack`**
  (testing), **Terraform `aws` provider** (provision it as code — ties to your Terraform work).

## Interview soundbites
- "Off-site backups go to S3 — versioned, lifecycle-expired, client-side GPG-encrypted, written by a
  least-privilege IAM user scoped to one bucket." (Then mention you also use **GCP** in production —
  honest, and shows multi-cloud literacy.)
- Know: **bucket/object, IAM user vs. role, policy/least privilege, versioning, lifecycle, SSE/KMS,
  region, durability, storage classes (Standard/IA/Glacier).**
- Honesty note: "My production cloud is GCP — BigQuery, service accounts, OAuth. I use AWS S3/IAM for
  off-site backup." Never claim deep EKS/Lambda production ops you don't have.

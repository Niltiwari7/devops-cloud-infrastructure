# Enable Required GCP APIs

## Command

```bash
gcloud services enable \
  compute.googleapis.com \
  storage.googleapis.com \
  secretmanager.googleapis.com \
  logging.googleapis.com \
  iam.googleapis.com \
  servicenetworking.googleapis.com
```

## Purpose

Enables the required Google Cloud services (APIs) for the current project.

Most GCP resources cannot be used until their API is enabled.

## Analogy

Google Cloud is like a newly rented office building. Utilities (electricity, internet, security, phones) are off until you turn them on.

In the same way, before you create VMs, buckets, secrets, or service accounts, their APIs must be enabled.

```text
Enable API = Turn on the service
```

## What Does Each API Do?

| API | Used For | Analogy |
|-----|----------|---------|
| `compute.googleapis.com` | VMs, disks, networks | Electricity for computers |
| `storage.googleapis.com` | Buckets, files, backups | Warehouse |
| `secretmanager.googleapis.com` | Passwords, API keys, tokens | Office locker |
| `logging.googleapis.com` | Application logs, errors, debugging | CCTV recording |
| `iam.googleapis.com` | Roles, permissions, service accounts | Security guard |
| `servicenetworking.googleapis.com` | Private connection between your VPC and Google managed services | Internal roads |

## Verify Enabled APIs

```bash
gcloud services list --enabled
```

## Key Takeaway

```text
gcloud services enable = Turn on a Google Cloud service before using it
```


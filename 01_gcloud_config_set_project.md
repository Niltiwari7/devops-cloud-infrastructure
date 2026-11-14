# GCP Project Configuration

## Command

```bash
gcloud config set project <project_id>
```

## Purpose

Sets the default Google Cloud project for all future `gcloud` commands.

## What Happens?

After running this command, GCP resources such as:

- Compute Engine VMs
- Service Accounts
- Storage Buckets
- Networks

will be accessed from the project:

```text
<project_id>
```

unless another project is explicitly specified.

## Analogy

Imagine Google Cloud as a shopping mall.

```text
Mall
 ├── Shop A (Development)
 ├── Shop B (Staging)
 └── Shop C (Production)
```

This command tells Google Cloud:

> "I am entering the `Development` shop. Use resources from this shop by default."

## Before

```bash
gcloud compute instances list
```

`gcloud` needs to know which project to use.

## After

```bash
gcloud config set project <project_id>
```

Now:

```bash
gcloud compute instances list
```

is equivalent to:

```bash
gcloud compute instances list \
  --project=<project_id>
```

## Verify Current Project

```bash
gcloud config get-value project
```

## View All Configurations

```bash
gcloud config list
```

## Key Takeaway

```text
gcloud config set project
=
Select the default GCP workspace/project
for future commands.
```
# Create a VM, Connect with IAP, and Set It Up

So far we built the company campus:

```text
Project
├── VPC              (building)
├── Subnet           (floor / department)
├── Cloud NAT        (reception phone line, outbound only)
├── Firewall         (security guard)
└── Service account  (employee ID card)
```

Now we "hire the employee": we create the **VM**, the actual computer that does the work.

```text
VM = A computer running in the cloud
```

---

## Step 1: Create the VM

### Command

```bash
gcloud compute instances create <vm_name> \
  --zone=<zone> \
  --machine-type=e2-standard-2 \
  --image-family=ubuntu-2204-lts \
  --image-project=ubuntu-os-cloud \
  --boot-disk-size=50GB \
  --boot-disk-type=pd-ssd \
  --network=<vpc_name> \
  --subnet=<subnet_name> \
  --no-address \
  --service-account=<sa_name>@<project_id>.iam.gserviceaccount.com \
  --scopes=https://www.googleapis.com/auth/cloud-platform
```

### Configuration Explained

| Setting | Value | Meaning | Analogy |
|---------|-------|---------|---------|
| Name | `<vm_name>` | Name to identify the VM | Employee's name |
| Machine type | `e2-standard-2` | 2 vCPU, 8 GB RAM | Desk size + computer power |
| OS | Ubuntu 22.04 LTS | Operating system | Windows/macOS on a laptop |
| Zone | `<zone>` (e.g. `asia-south1-a`) | A data center inside a region | Specific building in a city |
| Boot disk | 50 GB SSD | The VM's hard disk | Laptop storage |
| Network / Subnet | `<vpc_name>` / `<subnet_name>` | Where the VM lives | Building / department |
| External IP | None (`--no-address`) | No public IP. Internet access goes through Cloud NAT | No direct door to the street |
| Service account | `<sa_name>@...` | Identity the VM uses to access GCS, Secret Manager, Logging | Employee ID card |
| Scope | `cloud-platform` | Which Google APIs the VM may call | See below |

**Region vs zone:**

```text
Region (city, e.g. asia-south1 = Mumbai)
 ├── Zone a  (data center)
 ├── Zone b
 └── Zone c
```

### OAuth Scope vs IAM (common confusion)

Many people think the `cloud-platform` scope means admin access. **It does not.**

```text
Scope = Which APIs you are allowed to ASK
IAM   = What you are actually allowed to DO
```

Example: the VM asks to read a secret.

```text
Scope: "Yes, you can ask Secret Manager."
IAM:   "Do you have permission?" → No → Access denied
```

So the usual practice is: set the scope to `cloud-platform` and control access with IAM roles (least privilege).

### What Gets Created

```text
VM
├── Ubuntu 22.04
├── 50 GB SSD
├── Private IP only
├── Connected to the VPC and subnet
└── Service account attached
```

---

## Step 2: Connect Using IAP

```bash
gcloud compute ssh <vm_name> \
  --zone=<zone> \
  --tunnel-through-iap
```

`--tunnel-through-iap` creates a secure tunnel through Google, so the VM needs no public IP and port 22 is not open to the internet.

```text
Traditional SSH: Laptop → Internet → VM public IP (port 22 open) ❌
IAP:             Laptop → Google login → IAP → VM (private IP) ✅
```

Analogy: instead of anyone knocking on the kitchen door, visitors go through reception, show their ID, and then get in.

> This needs the firewall rule from note 06 that allows port 22 from Google's IAP range.

---

## Step 3: Update Ubuntu

```bash
sudo apt update        # Get the latest package list   (check the latest catalog)
sudo apt upgrade -y    # Install available updates     (update existing software)
```

**Why first?** A new VM may have pending security patches and old packages.

```text
Create VM → Update OS → Install tools
```

---

## Step 4: Install Utilities

```bash
sudo apt install -y \
  git curl wget unzip \
  build-essential \
  ca-certificates \
  jq
```

| Tool | Used For |
|------|----------|
| `git` | Download source code (`git clone`) |
| `curl` | API calls and file downloads |
| `wget` | File downloads |
| `unzip` | Extract ZIP files |
| `build-essential` | `gcc`, `g++`, `make`, needed to compile some packages |
| `ca-certificates` | Trusted SSL certificates for HTTPS |
| `jq` | Read and filter JSON (`cat response.json \| jq .`) |

---

## Step 5: Install Node.js

The automation framework uses **Node.js + TypeScript + Playwright**. Node.js is the engine that runs JavaScript apps.

```text
Hardware → OS → Node.js → Playwright → Automation tests
```

Verify:

```bash
node --version   # e.g. v20.x.x
npm --version    # e.g. 10.x.x
```

---

## Memory Hooks

```text
Create VM        = Hire an employee
Service account  = ID card
IAP              = Reception entry pass
Ubuntu update    = Employee training
Node.js + tools  = Tools needed for the job
```

## Golden Interview Line

```text
OAuth scope decides which APIs can be requested.
IAM decides which actions are actually allowed.
```

## What Comes Next

```text
Git clone → Node.js setup → Playwright install → Read secrets → Run tests → Upload report
```

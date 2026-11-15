# IAM, Service Account, Firewall and IAP

So far we built:

```text
Project → VPC → Subnet → Router + NAT
```

Now we secure it:

- **Identity:** who the VM is (service account)
- **Permissions:** what the VM can do (IAM)
- **Network access:** who can reach the VM (firewall + IAP)

---

## Part 1: Service Account

### Concept

A VM needs to access Google Cloud resources like GCS buckets, Secret Manager, and Cloud Logging. How does it prove who it is?

| Approach | Verdict |
|----------|---------|
| Username/password in code | ❌ |
| Service account JSON key file | ❌ |
| Service account attached to the VM | ✅ Recommended |

A **service account** is an identity for a machine or application, not a human.

```text
User account    = Human identity
Service account = Application identity
```

### Analogy

```text
Master key  → Opens every door        → Dangerous ❌
ID card     → Opens only some doors   → Safe ✅

ID card says:
  ✅ Warehouse
  ❌ CEO office
  ❌ Finance room
```

A service account is that ID card.

### Command

```bash
gcloud iam service-accounts create <sa_name> \
  --display-name="<display_name>"
```

This creates an identity with this email:

```text
<sa_name>@<project_id>.iam.gserviceaccount.com
```

### Why Not JSON Keys?

A JSON key is a long-lived password stored in a file. It can:

- Leak
- Get pushed to GitHub by mistake
- Need manual rotation

Instead, attach the service account to the VM and use **ADC (Application Default Credentials)**:

```text
VM → Attached service account → ADC → Automatic authentication (no key file)
```

Analogy: the employee wears the ID card. They don't carry a separate key.

### Principle of Least Privilege

**Give only the minimum permissions needed to do the job.**

| Need | Give ✅ | Don't give ❌ |
|------|---------|--------------|
| Read/upload files in one bucket | Storage role on **that bucket only** (e.g. `roles/storage.objectViewer`, `roles/storage.objectCreator`) | Project Owner |
| Read API keys and passwords | `roles/secretmanager.secretAccessor` on the needed secrets | Full project access |
| Send application logs | `roles/logging.logWriter` | Full project access |

Analogy: if someone only needs the warehouse, give them warehouse access, not ownership of the whole company.

```text
VM
 └── Service account
      ├── GCS access (bucket level)
      ├── Secret access
      └── Logging access
```

---

## Part 2: Firewall and IAP

### Firewall

A firewall is the **security guard** of the network. It decides:

- **Who** can connect
- On **which port**
- From **where**

Each rule either **allows** or **denies** traffic.

**Rule:** allow only the traffic you need. For example, internal services can talk to each other, but outsiders cannot.

```text
Bad:  0.0.0.0/0, allow all  =  Office gate open to the whole world ❌
```

### IAP (Identity-Aware Proxy) for SSH

**Traditional SSH (bad):**

```text
Laptop → Internet → VM public IP → Port 22 open
```

Attackers can see and attack the VM.

**SSH through IAP (good):**

```text
Laptop → Google login → IAP → VM (private IP only)
```

The VM has no public SSH exposure. Only verified users can get in.

Analogy:

```text
Without IAP: Anyone can knock on the kitchen door
With IAP:    Visitor → Reception → Identity check → Entry allowed
```

For IAP to work, the firewall must allow SSH (port 22) **only from Google's IAP range**, `35.235.240.0/20`. Think of it as "only reception staff can open the internal door."

### Keep noVNC Private

noVNC is a remote desktop in the browser. If it is exposed publicly, anyone can try to log in. Keep it private and reach it through IAP.

---

## Final Security Architecture

```text
You → Google IAP → Firewall → VM (private IP only) → Cloud NAT → Internet

Inbound from internet  ❌
Outbound to internet   ✅
```

This is what most production environments want.

## Memory Hooks

```text
Service account = Employee ID card
ADC             = Automatic ID check
IAM             = Permission system
Firewall        = Security guard
IAP             = Reception desk + identity check
Cloud NAT       = Company phone line (outbound only)
Secret Manager  = Office locker
```

## Golden Interview Lines

```text
Service account → "WHO are you?"
IAM             → "WHAT are you allowed to do?"
Firewall        → "WHO can reach you?"
IAP             → "HOW do you securely get in?"
```

## Interview Explanation

**What is a service account?**
A non-human identity that applications, VMs, and cloud services use to authenticate and access Google Cloud resources securely.

**Why avoid JSON keys?**
JSON keys are long-lived credentials that can be leaked or mismanaged. Attaching the service account to the VM and using ADC is more secure and follows Google Cloud best practice.

**Why use IAP for SSH?**
IAP lets users SSH into VMs that have no public IP. Access is checked against the user's Google identity and IAM permissions, so port 22 never has to be open to the internet.

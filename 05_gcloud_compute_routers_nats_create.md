# Configure Cloud Router and Cloud NAT

## Concept

A VM with only a **private IP** (e.g. `10.10.0.5`) cannot reach the internet, because private IPs are not routable on the internet.

**Cloud NAT** (Network Address Translation) solves this. It lets private VMs make **outbound** connections (GitHub, npm, Docker Hub, apt updates) without giving them a public IP. The internet still **cannot start a connection** to the VM.

```text
Without NAT:  VM (private IP) → Internet ❌

With NAT:     VM (10.10.0.5) → Cloud NAT → Public IP (34.x.x.x) → Internet ✅
```

## Analogy

An office where employees have internal extension numbers. Outsiders cannot call them directly, but employees can call out through the reception, using the company's main phone number.

```text
VM                 = Employee (internal extension)
Cloud NAT          = Reception desk
Static external IP = Company phone number
Cloud Router       = Office that the reception desk belongs to
```

## Commands

### 1. Create a Cloud Router

```bash
gcloud compute routers create <router_name> \
  --network=<vpc_name> \
  --region=<region>
```

Cloud NAT must be attached to a Cloud Router. The router holds the NAT configuration. NAT traffic does not pass through it.

### 2. Reserve a Static External IP

```bash
gcloud compute addresses create <nat_ip_name> \
  --region=<region>
```

A fixed public IP that NAT uses for outbound traffic.

```text
Dynamic IP = Can change
Static IP  = Always the same (useful when external services allowlist your IP)
```

### 3. Create Cloud NAT

```bash
gcloud compute routers nats create <nat_name> \
  --router=<router_name> \
  --region=<region> \
  --nat-external-ip-pool=<nat_ip_name> \
  --nat-all-subnet-ip-ranges \
  --enable-logging
```

| Flag | Meaning |
|------|---------|
| `--router` | Cloud Router to attach NAT to |
| `--region` | Must be the same region as the router |
| `--nat-external-ip-pool` | Use the reserved static IP |
| `--nat-all-subnet-ip-ranges` | All subnets in the region can use NAT |
| `--enable-logging` | Save NAT logs for debugging, auditing, and security (like a visitor log book) |

## Security Benefit

```text
VM with public IP  → Internet can reach the VM ❌
VM behind NAT      → VM can reach the internet, internet cannot reach the VM ✅
```

## Verify

```bash
# List routers
gcloud compute routers list

# List NAT gateways on a router
gcloud compute routers nats list \
  --router=<router_name> \
  --region=<region>

# List static IPs
gcloud compute addresses list
```

## Infrastructure Flow

```text
Project → VPC → Subnet → VM (private IP) → Cloud NAT → Static public IP → Internet
```

## Key Takeaway

```text
Cloud Router       = Holds the NAT configuration
Cloud NAT          = Outbound internet access for private VMs
Static External IP = Public identity used by NAT
```

## Interview Explanation

Cloud NAT lets VMs without external IP addresses access the internet for outbound traffic, such as software updates, package downloads, and API calls. It improves security because the internet cannot start connections to those VMs. Cloud NAT is configured on a Cloud Router in the same region and uses a static external IP as its public identity.

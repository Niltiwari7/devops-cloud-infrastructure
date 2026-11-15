# Create a Custom VPC

## Command

```bash
gcloud compute networks create <vpc_name> \
  --subnet-mode=custom
```

## Purpose

Creates a VPC (Virtual Private Cloud) network where you create the subnets yourself.

A VPC is a private network where resources like VMs, databases, load balancers, and Kubernetes clusters communicate securely.

```text
No VPC = No network = No communication
```

## Analogy

A VPC is like a company campus: the roads and boundaries that connect the offices, meeting rooms, and parking areas inside it.

```text
VPC = Company campus
Subnet = Department inside the campus
```

## Command Breakdown

| Part | Meaning |
|------|---------|
| `<vpc_name>` | Name of the VPC being created |
| `--subnet-mode=custom` | Do not create subnets automatically. I will create them myself. |

After this command, the VPC exists but has **no subnets**.

## Auto Mode vs Custom Mode

| | Auto Mode | Custom Mode |
|---|-----------|-------------|
| Subnets | Google creates one in every region | You create them yourself |
| Example | US subnet, Europe subnet, Asia subnet | Frontend, backend, database subnets |
| Control | Less | More |
| Use for | Quick testing | Production (recommended) |

Analogy: in auto mode, the government builds the roads for you. In custom mode, you design the roads, gates, and routes yourself.

## Verify the VPC

```bash
# List all VPCs
gcloud compute networks list

# Show details of one VPC
gcloud compute networks describe <vpc_name>
```

## Next Step: Create a Subnet

```bash
gcloud compute networks subnets create <subnet_name> \
  --network=<vpc_name> \
  --range=10.0.0.0/24 \
  --region=<region>
```

## Key Takeaway

```text
VPC        = Private network
Custom VPC = Private network where you control the subnets
```

## Interview Explanation

A VPC (Virtual Private Cloud) is a logically isolated network in Google Cloud. Using `--subnet-mode=custom` lets engineers create and manage subnets manually, giving more control over IP addressing, network segmentation, and security.

# Create a Subnet

## Command

```bash
gcloud compute networks subnets create <subnet_name> \
  --network=<vpc_name> \
  --region=<region> \
  --range=10.10.0.0/24
```

## Purpose

Creates a subnet inside an existing VPC.

A subnet is a smaller part of a VPC that gives IP addresses to resources like VMs, Kubernetes nodes, databases, and internal services.

```text
No subnet = No internal IP = VM cannot communicate
```

## Analogy

```text
VPC    = Entire company campus
Subnet = One department/floor inside the campus
Region = The city where the department is located
```

Resources are placed inside a department (subnet), not directly in the campus (VPC).

## Command Breakdown

| Part | Meaning |
|------|---------|
| `<subnet_name>` | Name of the subnet |
| `--network=<vpc_name>` | VPC the subnet belongs to |
| `--region=<region>` | Region where the subnet is created (e.g. `asia-south1` = Mumbai) |
| `--range=10.10.0.0/24` | IP address range for resources in this subnet |

## Understanding CIDR

```text
10.10.0.0/24  →  10.10.0.0 to 10.10.0.255  →  256 IP addresses
```

Google Cloud reserves 4 addresses in every subnet, so **252** can be used by VMs and services.

## Verify Subnets

```bash
# List all subnets
gcloud compute networks subnets list

# Show details of one subnet
gcloud compute networks subnets describe <subnet_name> \
  --region=<region>
```

## Infrastructure Hierarchy

```text
Project
└── VPC
    └── Subnet
        ├── VM-1
        ├── VM-2
        └── VM-3
```

## Key Takeaway

```text
VPC    = Entire private network
Subnet = A section of the network with its own IP range
```

## Interview Explanation

A subnet is a logical part of a VPC network in a specific region. It gives IP addresses to the resources deployed in it. Subnets help organize infrastructure, control network traffic, and allocate IP addresses efficiently.

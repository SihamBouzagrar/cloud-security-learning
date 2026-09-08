# 🏙️ AWS VPC — Your Own Private City in the Cloud

> *"The internet is a huge, open city. A VPC is the gated, private neighborhood you build inside it — your rules, your walls, your streets."*

If your **AWS Account** is the whole shopping mall, and **EC2** is the store that rents you computers... **VPC (Virtual Private Cloud)** is the **private, walled district** where that store and everything else you build actually lives.

---

## 🧱 The Core Idea

A VPC is a **virtual network** you create inside AWS — isolated from everyone else's, with your own IP address range, your own internal roads (subnets), and your own gates (internet gateways).

```
🌍 The Internet = the entire city
🏙️ Your VPC     = your private, gated neighborhood inside it
🏠 Subnets      = the individual streets inside your neighborhood
🚪 Gateways     = the checkpoints controlling who comes in and out
```

By default, AWS gives every account a **default VPC** just to get started — but for real projects, you build your own, sized and secured exactly the way you need.

---

### 📸 Real Example — My Project's VPC

![VPC Details showing CIDR block](./images/vpc-details-cidr.png)
*Figure 1: A custom VPC (`smartovate-cicd-vpc`) dedicated to the project, with an IPv4 CIDR of `10.0.0.0/16` — a fully isolated private network reserved just for this workload.*

---

## 🧩 The Building Blocks

| Component | What it does |
|---|---|
| **VPC** | The overall private network — defined by an IP range (e.g. `10.0.0.0/16`) |
| **Subnets** | Smaller IP ranges inside the VPC, each tied to one Availability Zone |
| **IP Addressing** | IPv4/IPv6 ranges assigned to the VPC and its subnets |
| **Route Tables** | The "road signs" — decide where traffic from a subnet is allowed to go |
| **Internet Gateway** | The main gate connecting your VPC to the public internet |
| **NAT Gateway** | A one-way door — lets private resources reach the internet *out*, without letting the internet reach *in* |
| **Security Groups** | Firewall at the **instance** level (stateful — remembers connections) |
| **NACLs** | Firewall at the **subnet** level (stateless — checks every packet, in and out) |
| **VPC Endpoints** | A private tunnel to AWS services (like S3), skipping the public internet entirely |
| **Peering Connections** | A private bridge between two separate VPCs |
| **Transit Gateway** | A central hub routing traffic between many VPCs, VPNs, and on-prem networks at once |
| **VPC Flow Logs** | The security camera — records all traffic in and out of your network interfaces |
| **VPN Connections** | A secure tunnel linking your VPC back to your own office/data center |

---

## 🔓 Public Subnet vs 🔒 Private Subnet

This is the distinction that matters most in real architectures:

| | Public Subnet | Private Subnet |
|---|---|---|
| **Has a route to Internet Gateway?** | ✅ Yes | ❌ No |
| **Directly reachable from the internet?** | ✅ Yes | ❌ No |
| **Typical use** | Load balancers, bastion hosts | Application servers, databases |
| **Outbound internet access** | Direct | Only via **NAT Gateway** |

**Why this matters:** you don't want your database or app servers exposed directly to the internet. They sit in a **private subnet**, and when they *do* need to reach out (e.g. to download an update or call an external API), traffic is routed through a **NAT Gateway** sitting in a public subnet — one-way glass, essentially.

### 📸 Real Example — Resource Map

![VPC resource map showing subnets, route tables and internet gateway](./images/vpc-resource-map.png)
*Figure 2: Two public subnets spread across `eu-west-3a` and `eu-west-3b`, both attached to the same route table (`smartovate-cicd-rt-public`), which routes traffic out through the Internet Gateway (`smartovate-cicd-igw`) — the foundation for high availability across two zones.*

```
🌐 Internet
   │
   ▼
🚪 Internet Gateway
   │
   ▼
🔓 Public Subnet ──── NAT Gateway
   │                      │
   │                      ▼
   │                 🔒 Private Subnet
   │                 (app servers, DB — hidden from the internet)
   ▼
Load Balancer (public-facing)
```

---

## 🏛️ High Availability — Don't Build in Just One Zone

A well-architected VPC spreads its subnets across **at least two Availability Zones**. If one zone has an outage, the other keeps serving traffic.

```
Availability Zone A          Availability Zone B
   🔓 Public Subnet             🔓 Public Subnet
   🔒 Private Subnet            🔒 Private Subnet
```

This redundancy is often a hard requirement in production architectures (e.g. compliance baselines like "US 3.1" style standards).

---

## 🔐 Security Layers, Stacked

```
Internet
   │
   ▼
NACL (subnet-level, stateless) ──► allow/deny by IP range, checks every packet
   │
   ▼
Security Group (instance-level, stateful) ──► allow/deny by port/protocol, remembers connections
   │
   ▼
Your EC2 instance
```

Two layers, two philosophies: NACLs are the neighborhood's outer wall, Security Groups are the lock on each individual house door.

---

## 🗝️ TL;DR

| Concept | One-line summary |
|---|---|
| VPC | Your private, isolated network inside AWS |
| Subnet | A smaller IP range within the VPC, tied to one AZ |
| Public Subnet | Has a direct route to the internet |
| Private Subnet | No direct route — hidden from the internet |
| NAT Gateway | Lets private resources reach *out*, blocks the internet from reaching *in* |
| Security Group | Instance-level firewall (stateful) |
| NACL | Subnet-level firewall (stateless) |
| Multi-AZ design | Redundancy — one zone failing doesn't take everything down |

**A VPC is the foundation everything else in AWS is built on top of — get the network right, and the rest of your architecture has a solid floor to stand on.**

---

*📚 Notes from my AWS learning journey — feel free to fork, comment, or suggest edits!*

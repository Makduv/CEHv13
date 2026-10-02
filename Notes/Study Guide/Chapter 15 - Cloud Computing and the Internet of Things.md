# Chapter 15 - Cloud Computing and the Internet of Things

# Cloud Computing Overview

**Cloud computing** = outsourcing IT resources to a provider and managing them through a **web-based interface** (HTTP, HTML). All you need is a browser and an internet connection — accessible from anywhere in the world.

**Origin:** companies with more resources than they needed started **selling access** to others — called a **service bureau**. Back then, you had to send tapes or use phone lines and modems. Cloud computing replaced that with web-based access and **rapid, agile delivery**.

**Multitenancy** = the provider divides storage and compute space between multiple customers on the same infrastructure → **economies of scale** (cost shared across tenants).

**Advantages:**

- **Less cost and overhead** — no hardware to buy, power, cool or maintain
- **High availability and fault tolerance** — built-in redundancy
- **On-demand** — spin resources up or down as needed
- **Self-service** — provision through a web portal, no phone calls or contracts for every change

## Cloud Services

### IaaS (Infrastructure as a Service)

You get a **virtual machine** with an OS installed — the provider handles hardware, power, cooling, networking. You manage **everything from the OS up**: configuration, patching, applications.

Like renting a bare apartment — walls and plumbing are there, you furnish it.

### PaaS (Platform as a Service)

The provider gives you a **platform** ready to deploy on — an application server, a database engine, a runtime environment. All pre-installed and configured. You just **deploy your application** — no OS management, no middleware installation.

Like renting a fully equipped kitchen — you just cook.

### SaaS (Software as a Service)

A **finished application** delivered through a web interface. The provider manages everything — you're only concerned with the **data you enter** into the application.

Examples: CRM (Salesforce), email (Microsoft 365), collaboration (Google Workspace).

**Risk:** you're handing a lot of data to the provider — if they're compromised, your data goes with them. Authentication may use **shared providers** (Google, Facebook) or direct accounts with the service.

### STaaS (Storage as a Service)

A **storage solution** — fixed amount of space with a cloud provider, accessible via web interface or a downloadable app.

Examples: Google Drive, Apple iCloud, Microsoft OneDrive.

**Blob storage** = stores **unstructured data** — video, audio, images, logs, backups, files. Called **S3 buckets** in AWS.

### Comparison

|  | **IaaS** | **PaaS** | **SaaS** | **STaaS** |
| --- | --- | --- | --- | --- |
| **You get** | VM + OS | Platform (app server, DB) | Finished application | Storage space |
| **You manage** | OS, apps, data | Your app + data | Data only | Data only |
| **Provider manages** | Hardware, virtualisation | + OS, middleware, runtime | Everything except data | Everything except data |
| **Example** | AWS EC2, Azure VMs | Heroku, Azure SQL, App Engine | Salesforce, Office 365 | Google Drive, S3 |
| **Analogy** | Bare apartment | Equipped kitchen | Restaurant meal | Storage unit |

## Shared Responsibility Model

The provider is **always** responsible for the **physical** layer: power, **HVAC** (heating, ventilation, cooling), physical security (restricting access to the data centre), and the underlying hardware/virtualisation.

What the **customer** is responsible for depends on the service:

|  | Physical | Virtualisation | OS | Middleware/Runtime | Application | Data | IAM |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **IaaS** | Provider | Provider | **Customer** | **Customer** | **Customer** | **Customer** | **Customer** |
| **PaaS** | Provider | Provider | Provider | Provider | **Customer** | **Customer** | **Customer** |
| **SaaS** | Provider | Provider | Provider | Provider | Provider | **Customer** | Shared |

**Identity and access management (IAM):** businesses may use their existing **directory services** (Active Directory) to authenticate to cloud services — **federated identity**: a single repository of authentication information that several applications can use (SSO).

## Public vs Private Cloud

|  | **Public cloud** | **Private cloud** |
| --- | --- | --- |
| **Who uses it** | Anyone, anywhere — multiple businesses and individuals share the infrastructure | **One organisation** only — dedicated to them |
| **Tenancy** | Multiple businesses on shared servers | Multitenancy still exists (departments, teams) but **within a single organisation** |
| **Control** | Limited — no guarantee on how services are provisioned internally | Full control — you decide how everything is configured |
| **Cost** | Lower (shared) — pay for what you use | Higher — you run and maintain it |
| **Examples** | AWS, Azure, GCP | **OpenStack** (open-source platform to manage VMs, virtual apps, networking — everything needed to run your own cloud) |
| **Security** | Provider handles most of it; you trust their practices | You control security end to end |

**Also:** **hybrid cloud** (mix of public and private — some workloads internal, some outsourced) and **community cloud** (shared between organisations with common requirements, like government agencies).

## Grid Computing

**Grid computing** = increasing processing power by **distributing work across many systems** working on the same problem.

- Different processing units need to know what others are doing → requires **communication and coordination** between all systems
- A **controller** manages the work distribution and collects results
- **Botnets are an example** of grid computing — thousands of compromised machines working together (though for malicious purposes)
- **GPUs** are more effective than CPUs for this kind of parallel processing
- **CPU scavenging** = making use of **unused processor cycles** on idle computers (e.g. BOINC, Folding@Home — volunteer their spare CPU/GPU time)

---

# Cheatsheet

**Service models**

| Model | You manage | Provider manages |
| --- | --- | --- |
| **IaaS** | OS + apps + data | Hardware, virtualisation |
| **PaaS** | App + data | + OS, middleware, runtime |
| **SaaS** | Data (+ users) | Everything else |
| **STaaS** | Data | Everything else |

**Cloud types**

| Type | Who | Control |
| --- | --- | --- |
| **Public** | Anyone | Limited |
| **Private** | One org (OpenStack) | Full |
| **Hybrid** | Mix of both | Varies |
| **Community** | Shared by similar orgs | Shared |

**Key terms**

| Term | Meaning |
| --- | --- |
| **Multitenancy** | Multiple customers on shared infrastructure |
| **Shared responsibility** | Provider owns physical + infra; customer owns data + (varies) |
| **Federated identity / SSO** | Single auth repository across multiple services |
| **Blob storage / S3** | Unstructured data storage (files, video, backups) |
| **OpenStack** | Open-source private cloud platform |
| **Grid computing** | Distribute processing across many systems |
| **CPU scavenging** | Use idle processor cycles on other machines |
| **HVAC** | Heating, ventilation, cooling — always the provider's job |

**Exam reflexes**

- "VM with OS, you manage everything above" → **IaaS**
- "Deploy your app, nothing else to configure" → **PaaS**
- "Use the finished application via browser" → **SaaS**
- "Provider always responsible for" → **physical security, power, HVAC, hardware**
- "Customer always responsible for" → **data**
- "Single organisation's own cloud" → **private cloud**
- "Distribute work across many machines" → **grid computing**
- "Botnets are an example of" → **grid computing**
- "Unstructured storage in AWS" → **S3 bucket**
- "Use idle CPU cycles" → **CPU scavenging**

# Cloud Architecture and Deployment

## Lift and Shift vs Cloud-Native

**Lift and shift** = take the systems you have on-premises and move them **as-is** to a cloud provider — same configuration, same architecture, just running on someone else's hardware.

It works, but it **doesn't take advantage** of what the cloud does best (elasticity, serverless, managed services). You're paying cloud prices for an on-prem design.

**The better approach:** redesign around **cloud-native** principles — break the application into smaller, independent pieces that the cloud can scale, replace and manage.

## The evolution of deployment

```
Physical systems → Virtual machines → Containers → Serverless functions
```

Each step makes units **smaller, faster to start, and cheaper to run** — from a full machine down to a single function that exists only while it's executing.

## Responsive Design

Cloud-native components are **small and lightweight** — they start fast, and they **run only when needed** (saving cost when idle).

**Traditional scaling:** a **load balancer** sits in front of multiple servers and spreads requests across them. You provision enough servers for peak load — the rest of the time, they're underused.

**Cloud scaling:** with virtualisation and **Infrastructure as Code (IaC)**, you can adjust capacity **on the fly**. Need more? Spin up instances in seconds. Demand drops? They disappear.

**Security benefit:** resources appear and disappear dynamically — an attacker doesn't know **how long a resource will be available** or where it will be next. The attack surface is constantly shifting.

## Cloud-Native Design

### Monolithic vs microservices

**Traditional application** = **monolithic** — everything (UI, business logic, data access) lives in one executable or one execution space. One codebase, one deployment, one failure domain.

**Cloud-native** = **decentralised** — break the application into **separate services**, each handling one function in its own execution space. This is a **service-oriented / microservices** architecture.

Each small service typically runs in a **container** (lightweight, isolated, with only what it needs). Containers expose **ports** to communicate with other services.

### Containers

A container is **not a VM** — it shares the host OS kernel but isolates the application and its dependencies. Much lighter and faster to start than a full VM.

**Docker** = the most common container runtime. **Kubernetes (K8s)** = the most common orchestration platform for managing many containers.

### Serverless

**Serverless** takes it further: there's no application, no container, **no operating system** to manage. Just a **function** — a single piece of code that runs in response to an **event**.

**Event-driven:** the function only executes when something triggers it (an HTTP request, a file upload, a database change, a queue message). When nothing triggers it, it doesn't exist — you pay **zero**.

**Publish/subscribe (pub/sub):** a messaging pattern where services **publish** events to a topic, and other services **subscribe** to topics they care about. The publisher doesn't know who's listening, and the subscriber doesn't know who's publishing — they're decoupled.

**Cloud implementations:** AWS **Lambda**, Azure **Functions**, Google **Cloud Functions**.

## Deployment — Infrastructure as Code (IaC)

Deployment is **automated** using an **orchestration platform** — commonly called IaC because the entire infrastructure is **defined in code** (scripts or configuration files). No manual clicking through consoles.

### IaC tools

| Tool | How it works |
| --- | --- |
| **Ansible** | Uses a **server** where config files (playbooks in **YAML**) reside. An **agent** on the target system applies the configuration. Agentless option too (connects via SSH) |
| **Terraform** | A scripting language to **build entire environments in code** — provider-agnostic (works across AWS, Azure, GCP). Declarative: you describe what you want, Terraform figures out how |
| **PowerShell** | Can script deployments on Windows/Azure |
| **Cloud CLIs** | AWS CLI, Azure CLI, gcloud — command-line interfaces to the provider's **API** |

### Configuration formats

| Format | Used by |
| --- | --- |
| **YAML** | Ansible playbooks, Kubernetes manifests, many modern tools. Human-readable, indentation-based |
| **JSON** | Azure ARM templates, AWS CloudFormation. Machine-readable, bracket-based |
| **HCL** | Terraform's own language |

### Why IaC matters

- **Repeatable and consistent** — same configuration every time, no drift
- **Testable** — validate changes before applying them to production
- **Version-controlled** — store in Git, track every change, roll back if needed
- **Fast** — deploy an entire environment in minutes

## Dealing with REST

**HTTP is stateless** — it doesn't maintain a connection between server and client. Each request starts from scratch. The **server remembers nothing** about the client between requests.

### What REST is

**REST** (Representational State Transfer) = a **design style** for web-based APIs (not a protocol). Applications built this way are called **RESTful**.

### REST architectural properties

| Property | What it means |
| --- | --- |
| **Client-server** | Client and server are **separate** — the client handles the UI, the server handles the data and logic. They communicate over HTTP |
| **Stateless** | The server maintains **no client state** between requests. Two types of state exist: **resource state** (what the server knows — the data) and **application state** (what the client knows — where it is in a workflow). The client tells the server its state with **every request** |
| **Cacheability** | Static data sent from server to client can be **cached locally** — reduces load and improves speed |
| **Layered system** | Client and server communicate without knowing implementation details behind each other — allows **modularity** (swap components without breaking the application) |
| **Uniform interface** | No matter what happens server-side, the client uses the **same interface**. Data transferred is **self-descriptive** and clearly identified so it can be parsed correctly |

### HTTP verbs in REST

| Verb | Action | CRUD equivalent |
| --- | --- | --- |
| **GET** | Retrieve data | Read |
| **POST** | Create new data | Create |
| **PUT** | Update/replace data | Update |
| **DELETE** | Remove data | Delete |

Data is typically transmitted in **XML** or **JSON** — self-describing formats.

### Testing and attacking REST APIs

- **Burp Suite** — intercept and modify REST requests. Use **Intruder** to fuzz parameters and test for injection
- **Endpoint discovery** — API endpoints aren't always documented. Use **forced browsing** (brute-force endpoint names) with Burp Suite Intruder + an **API wordlist**, or use **ZAP** (Zed Attack Proxy)
- The challenge: you need to **find the endpoints first** before you can test them

---

# Cheatsheet

**Evolution**

| Level | What it is | Start time | You manage |
| --- | --- | --- | --- |
| **Physical** | Full machine | Minutes–hours | Everything |
| **VM** | Virtual machine | Seconds–minutes | OS + apps |
| **Container** | Isolated process (Docker) | Milliseconds–seconds | App only |
| **Serverless** | Single function (Lambda) | Milliseconds | Code only |

**Cloud-native concepts**

| Term | Meaning |
| --- | --- |
| **Lift and shift** | Move on-prem to cloud as-is — works but misses cloud benefits |
| **Monolithic** | Everything in one executable/space |
| **Microservices** | App broken into separate small services |
| **Container** | Lightweight isolation (Docker); orchestrated by **Kubernetes** |
| **Serverless** | No OS, no container — just a function, event-driven, pay-per-execution |
| **Pub/sub** | Publish events to a topic; subscribers react — decoupled communication |
| **Responsive design** | Scale up/down automatically based on demand |

**IaC tools**

| Tool | Format | Notes |
| --- | --- | --- |
| **Ansible** | YAML | Playbooks, agentless (SSH) or with agent |
| **Terraform** | HCL | Provider-agnostic, declarative |
| **PowerShell** | Scripts | Windows/Azure |
| **Cloud CLIs** | Commands | AWS CLI, Azure CLI, gcloud |

**REST**

| Property | Key idea |
| --- | --- |
| Stateless | Server keeps no client state — client sends everything each time |
| Uniform interface | Same interface regardless of server implementation |
| Verbs | GET (read), POST (create), PUT (update), DELETE (remove) |

**Testing tools:** Burp Suite (intercept + Intruder for fuzzing) · ZAP (endpoint discovery / forced browsing) · API wordlists for brute-forcing endpoints.

**Exam reflexes**

- "Move on-prem to cloud unchanged" → **lift and shift**
- "Function that runs only on events, no OS" → **serverless** (Lambda / Azure Functions)
- "Define infrastructure in code for repeatable deployments" → **IaC** (Ansible, Terraform)
- "HTTP is stateless" → server keeps no client state; client sends state each time
- "GET, POST, PUT, DELETE" → **REST verbs**
- "Break an app into independent services" → **microservices**
- "Lightweight isolation, shares host kernel" → **container** (Docker)
- "Manage many containers" → **Kubernetes**
- YAML = Ansible/K8s · JSON = Azure ARM/CloudFormation · HCL = Terraform

# Common Cloud Threats

The threats are largely **the same as on-premise** — phishing, misconfigurations, weak credentials, insider threats. The difference is the **shared responsibility model**: some protections are yours, some are the provider's, and mistakes happen when that boundary is unclear.

## Access Management

Same **IAM** (Identity and Access Management) challenges as on-premise — users need accounts, permissions, and controls.

**Best practices:**

- **Review and approve** all access — ensure each user has **only the level needed** to do their job (**least privilege**)
- Use **Role-Based Access Control (RBAC)** rather than managing individual permissions per user
- **Strong, unique passwords** — not reused from other services
- **Multi-factor authentication (MFA)** — essential for cloud access
- **Federated identity** — use your existing **Active Directory** to authenticate users to cloud services (SSO). Cloud providers also offer their own IAM modules
- **Cryptographic keys** can be created to access individual resources (API keys, SSH keys, service account keys)
- **Auditing and logging** — log all access to resources and **monitor** that access. You can't protect what you can't see

## Data Breach

**Application exposure** — an attacker compromises an application and gains access to the underlying data.

**Storage misconfiguration** — data stored in unstructured storage (like **S3 buckets**) can accidentally be left **public**. People think that because the URL isn't published anywhere, it's safe. **Hiding something by not publishing it is not protecting it** — URLs can be guessed, scraped, or discovered through enumeration.

**Protection:**

- Regularly **assess permissions** on resources to ensure access controls are correct
- Use **DLP** (Data Loss Prevention) to detect and block sensitive data leaving the environment
- **Default to private** — make every resource non-public unless explicitly needed

## Web Application Compromise

The **OWASP Top 10** applies equally to cloud-hosted applications.

**Causes:**

- **Misconfiguration** of the cloud provider or the application
- **SQL injection** and other injection attacks
- **Outdated components** — unpatched frameworks and libraries

The controls required on-premise are **the same for cloud**.

**Recommendations:**

- Start with **security in the design phase** (security by design, not as an afterthought)
- **Patch** and keep components up to date
- Have the application **tested by a third party** (pentest)
- Protect with a **WAF** (Web Application Firewall)

## Credential Compromise

- **Brute force** happens **offline** — credential dumps with hashed passwords can be cracked at the attacker's pace without triggering lockouts
- Cracked credentials are also **sold on the darknet** (via Tor)
- **Credential stuffing** — use pairs of username/password found in breaches and try them against cloud services
- **Phishing** — simply ask for the credentials

**Defence:**

- **Security awareness training** against phishing
- **Email protections** using threat intelligence
- **MFA** is essential against credential-based attacks

**MFA caveat — push fatigue:** if the attacker keeps trying to authenticate, the victim keeps receiving push notifications and eventually **approves out of frustration**. Better to use a **OTP** (One-Time Password) generated from an authenticator app or hardware device — the attacker can't trigger a prompt.

## Insider Threat

An employee who either **steals or damages** business resources. They're far more likely to make **mistakes** that result in damage or loss than to act **maliciously** — but both happen.

**Defence:**

- **Least privilege** — limit what each person can access and do
- **Logging and monitoring** access and actions — detect damage and loss early
- **Behavioural analytics** — spot unusual access patterns

## Cloud Penetration Testing — Important Difference

Cloud pentesting is **not the same** as on-premise pentesting:

- Most **IP addresses are owned by the cloud provider**, not the customer
- You must know **exactly where your application is hosted** and ensure you have **permission** to test it
- Many providers require you to **notify them** or operate within their acceptable testing policy — testing outside your scope could be treated as an attack against the provider's infrastructure

---

# Cheatsheet

**Threats**

| Threat | Key point |
| --- | --- |
| **Access management** | RBAC + least privilege + MFA + federated identity + logging |
| **Data breach** | Public S3 buckets, misconfigured storage — hiding ≠ protecting |
| **Web app compromise** | Same as on-prem — OWASP Top 10, SQLi, misconfig |
| **Credential compromise** | Offline cracking, credential stuffing, phishing |
| **Insider threat** | Mistakes more common than malice — least privilege + monitoring |

**Defences**

| Defence | What it addresses |
| --- | --- |
| **RBAC** | Access management at scale |
| **MFA (OTP > push)** | Credential compromise — push fatigue makes push MFA weaker |
| **DLP** | Data breach — detect sensitive data leaving |
| **WAF** | Web application attacks |
| **Federated identity / SSO** | Centralised auth via AD |
| **Logging + monitoring** | Everything — you can't protect what you can't see |
| **Security awareness training** | Phishing and social engineering |
| **Least privilege** | Insider threat + access management |

**Exam reflexes**

- "S3 bucket publicly accessible" → **data breach** from misconfiguration — hiding ≠ protecting
- "Push MFA fatigue" → use **OTP from an authenticator app** instead
- "Testing cloud apps" → need **permission from the provider** + know exactly what's yours
- "Employee accidentally leaks data" → **insider threat** (mistake, not malice)
- "Same controls as on-prem" → true for cloud — OWASP, WAF, patching, least privilege
- "Single auth for cloud and on-prem" → **federated identity / SSO** via AD

# Internet of Things (IoT)

**IoT** = everyday objects with **embedded software and network connectivity** — devices with **limited capability**, not much memory or computing power, but enough intelligence to communicate.

Examples: light switches, appliances, thermostats, healthcare devices, cameras, smart locks, industrial sensors — anything that needs some intelligence and networking but isn't a full computer.

**The problem:** these devices are **detectable on your network** and sometimes **to the entire internet** — often with no authentication, default credentials, or no ability to patch.

## Finding IoT devices

| Method | How |
| --- | --- |
| **Shodan** (`shodan.io`) | Search engine for internet-connected devices — finds IoT by banner, port, vendor |
| **Censys** (`censys.io`) | Similar to Shodan — indexes devices and certificates |
| **nmap** | Network scan on your own network. Use the **OUI** (first 3 octets of the MAC address) to identify the **device manufacturer** |

## Interacting with IoT devices

Embedded devices may have **open ports** and a **web server** running on them.

- Try connecting with **netcat** (`nc <ip> <port>`) — if nothing appears (no banner, no HTML), it could be an **API** that controls the device
- Test against the API with **Burp Suite** or **Postman** — generate HTTP requests to the web server and see how it responds

## How IoT communicates

IoT devices typically communicate with **cloud-based controllers** — the intelligence and management live on the internet somewhere, and the device phones home to them.

**Attack angle:** since IoT devices are controlled by remote services, you can **sniff the communication** between the device and its controller to understand the protocol, find credentials, or intercept commands.

## Fog Computing

**Fog computing** takes the concept of cloud services (processing separate from the endpoint) but **moves the service closer to the endpoint** — to the edge of the network rather than a distant data centre.

**Why:** reduces **latency and processing load** — critical for IoT devices that generate huge amounts of data or need near-real-time responses (industrial sensors, autonomous vehicles, healthcare monitors). Sending everything to the cloud and back is too slow.

**Two planes:**

| Plane | Role |
| --- | --- |
| **Data plane** | Manages the data — processing, storing, aggregating it locally before sending summaries to the cloud |
| **Control plane** | Decides how to handle incoming requests — what should be done with them, routing and policy decisions |

Think of it as: **cloud = centralised brain far away** · **fog = local brain close to the devices** · **edge = processing on or next to the device itself**.

---

# Cheatsheet

**IoT**

| Tool | Purpose |
| --- | --- |
| **Shodan** | Search for internet-exposed IoT devices |
| **Censys** | Same — device and certificate indexing |
| **nmap** | Find IoT on the local network; OUI identifies manufacturer |
| **netcat** | Connect to open ports on the device |
| **Burp Suite / Postman** | Test the device's API |

**Key terms**

| Term | Meaning |
| --- | --- |
| **IoT** | Limited-capability devices with network connectivity |
| **OUI** | First 3 octets of MAC — identifies the manufacturer |
| **Fog computing** | Cloud services moved closer to the endpoint — lower latency |
| **Edge computing** | Processing on or next to the device itself |
| **Data plane** | Processes, stores, aggregates data |
| **Control plane** | Decides how to handle requests |

**Exam reflexes**

- "Search engine for IoT devices" → **Shodan** (or Censys)
- "Identify device manufacturer from MAC" → **OUI** (first 3 octets)
- "Cloud but closer to the endpoint" → **fog computing**
- "Processing on the device itself" → **edge computing**
- "Sniff IoT traffic to understand the protocol" → captures communication between device and cloud controller
- IoT security problems: default creds, no patching, open ports, no encryption

# Operational Technology

**Industrial Control Systems (ICS)** are referred to as **operational technology (OT)** — the systems that run physical operations like gas, electricity, water utilities, and manufacturing. Unlike IT (which handles information), OT **controls physical processes**.

An ICS is a collection of **devices, controllers and interfaces** that together allow business operations to supply resources to consumers or perform manufacturing tasks.

## SCADA

One type of ICS is **SCADA** (Supervisory Control and Data Acquisition) — a system that includes many devices that need to be **monitored and controlled**. Those devices are managed by **microcontrollers**, which may be **PLCs** (Programmable Logic Controllers).

**ICS architecture (simplified):**

```
Human operator → HMI/HID → Supervisor → Microcontrollers (PLC/RTU) → Machines/Sensors
```

| Component | Role |
| --- | --- |
| **HMI** (Human Machine Interface) / **HID** (Human Interface Device) | The screen/console an operator uses to monitor and control the system |
| **Supervisor** | Coordinates the microcontrollers |
| **PLC** (Programmable Logic Controller) | Microcontroller that directly controls a machine or process |
| **RTU** (Remote Terminal Unit) | Similar to a PLC but designed for remote, distributed locations |
| **Sensors / Actuators** | The physical devices — sensors measure, actuators do the work (open valves, start motors) |

## Modbus

**Modbus** = one of the oldest and most common **communication protocols** used in ICS/SCADA environments. It allows PLCs, RTUs and supervisory systems to exchange data.

**The security problem:** Modbus was designed in **1979** with **no authentication, no encryption, and no access control**. Commands are sent in plaintext. Anyone who can reach the network can read and write to devices. Modern variants (Modbus/TCP) run over Ethernet but still have **no built-in security**.

## ICS/SCADA security issues

- **Fragile** — devices can be underpowered and **not programmed to handle anomalies** (unexpected input, port scans, or heavy traffic can crash them)
- Hard to implement **strong security controls** — strong authentication, encryption and access control are difficult on resource-constrained devices
- **Default credentials** — many devices ship with default username/password and they're never changed
- **Long lifecycles** — OT equipment runs for 15–30 years, far longer than IT equipment. Patching is rare or impossible
- **Availability is king** — in OT, uptime matters more than anything. Rebooting for a patch can mean shutting down a power plant

## The Purdue Model

Also known as the **Purdue Enterprise Reference Architecture (PERA)** — an architectural design guiding network design for businesses using **Computer-Integrated Manufacturing (CIM)**. Created to **separate OT from IT** so operational systems aren't directly exposed to the corporate network (and the internet).

### The levels

| Level | Zone | What's there |
| --- | --- | --- |
| **Level 0** | Physical process | **Sensors and actuators** — the devices that do the physical work |
| **Level 1** | Intelligent devices | **PLCs and RTUs** — interface directly with the sensors and actuators |
| **Level 2** | Control system | **SCADA systems and HMI** — communicate with PLCs/RTUs, provide operator visibility |
| **Level 3** | Site operations | **MOM** (Manufacturing Operations Management) or **MES** (Manufacturing Execution System) — manages production workflows |
| **DMZ** | — | **Segmentation between OT and IT** — recommended buffer zone |
| **Level 4** | Logistics / business planning | Business systems, ERP |
| **Level 5** | Enterprise network | Corporate IT, email, internet access |

### The key principle

Between OT (levels 0–3) and IT (levels 4–5) there should be **strong segmentation** — ideally a **DMZ** to protect both sides. Even better: OT should be **air-gapped** (physically disconnected from any network that touches the internet).

In practice, the air gap is disappearing — businesses want remote monitoring and cloud dashboards. Every connection across the boundary is a risk.

---

# Cheatsheet

**ICS/SCADA components**

| Component | Role |
| --- | --- |
| **HMI** | Operator console — monitor and control |
| **PLC** | Microcontroller that controls machines directly |
| **RTU** | Like a PLC but for remote/distributed sites |
| **SCADA** | Supervisory system — monitors and manages PLCs/RTUs |
| **Modbus** | Old ICS protocol — **no auth, no encryption** |

**Purdue Model levels**

| Level | What |
| --- | --- |
| 0 | Sensors / actuators (physical) |
| 1 | PLCs / RTUs |
| 2 | SCADA / HMI |
| 3 | Manufacturing operations (MOM/MES) |
| DMZ | Separation between OT and IT |
| 4–5 | Business / enterprise IT |

**Key terms**

| Term | Meaning |
| --- | --- |
| **OT** | Operational Technology — controls physical processes |
| **IT** | Information Technology — handles data and information |
| **Air gap** | Physical disconnection from other networks |
| **CIM** | Computer-Integrated Manufacturing |
| **PERA** | Purdue Enterprise Reference Architecture |

**Exam reflexes**

- "Controls physical processes (gas, water, electricity)" → **ICS / OT**
- "Monitors and controls remote devices" → **SCADA**
- "Directly controls a machine" → **PLC**
- "Old protocol, no authentication, no encryption" → **Modbus**
- "Separates OT from IT in network design" → **Purdue Model**
- "Best protection for OT" → **air gap**
- "Devices use default credentials, can't be patched easily" → classic **ICS security problem**
- "Availability over confidentiality" → the OT priority — uptime is everything
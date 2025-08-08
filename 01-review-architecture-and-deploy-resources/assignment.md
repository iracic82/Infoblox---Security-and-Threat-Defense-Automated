---
slug: review-architecture-and-deploy-resources
id: mc8jy5j4qnpd
type: challenge
title: Review Architecture and Deploy Resources
teaser: Review Architecture and Deploy Resources
notes:
- type: video
  url: https://cdn.instruqt.com/assets/instruqt/2024-036%20Instruqt%20101_Version%203.0.mp4
- type: video
  url: https://videos.infoblox.com/watch/DytGsd2iVoUeVhaqva1kNm?
tabs:
- id: uq6pykdlzhzo
  title: Shell
  type: terminal
  hostname: shell
- id: tdlbtxew1dgj
  title: Editor
  type: code
  hostname: shell
  path: /root/infoblox-lab
- id: krwapslelabb
  title: AWS Console
  type: browser
  hostname: aws
- id: cv33pzbujvdt
  title: Azure Console
  type: browser
  hostname: azure
- id: k9fpx6z7spww
  title: Infoblox Portal
  type: browser
  hostname: infoblox
- id: szrkdziaaxd6
  title: Lab Diagram
  type: browser
  hostname: lab
difficulty: ""
timelimit: 0
lab_config:
  default_layout_sidebar_size: 0
enhanced_loading: null
---
# Overview

DNS remains one of the most exploited, and yet least secured layers in modern network infrastructure.
As enterprises expand into cloud and hybrid architectures, DNS visibility becomes fragmented — and threat actors exploit the gaps.

This hands-on lab demonstrates how to enforce DNS security across distributed environments using Infoblox NIOS X-as-a-Service and Threat Intelligence. We simulate a hybrid setup with:
	•	An AWS VPC acting as a remote branch/client site
	•	A centralized DNS hub using Infoblox SaaS
	•	IPsec-secured tunnels for DNS transport
	•	Live DNS threat enforcement in the data path

# Use Case Story: ACME Corp

ACME Corp, a global enterprise with dozens of branch offices and cloud regions, has just migrated one regional site to AWS and they have presence in Azure. Their goals:
- Securely resolve internal and external domains
- Prevent split-brain DNS
- Block known bad domains using RPZ
- Log and audit DNS queries across environments
- Provide authoritative responses for synced Azure Private DNS zones (e.g. infolab.com) using NIOS X-as-a-Service


To solve this, ACME deploys NIOS X-as-a-Service in the cloud and connects the AWS branch via IPsec VPN. All DNS traffic is routed to Infoblox for policy enforcement and visibility. In Azure, ACME is using the Azure native DNS Service that is synchronized with UDDI (Universal DDI) and is managed via Infoblox Portal.

# DNS Security in Action

Once Infoblox Threat Defense is up and running, it immediately kicks into gear to protects against:
- Typosquatted domains like amaz0n-update.com get blocked on the spot
- A centrally logged DNS repository with full query details for easy investigation and curated to make hunting easier.
- Real-time enforced protection using TIDE and RPZ policies from dozens of feeds such as MalwareC2, BOT, APT, Suspicious, and many more.
- Dozens of curated threat intel feeds are pre-loaded and ready to block malicious traffic.
- Instant inline AI-driven blocking stops threats live, with no waiting for backend analysis.
- Zero Day protection blocks never-before-seen suspicious domains, verifying them with a <99.9% false positive rate.
- High-risk content like gambling and adult sites is filtered with built-in web protection.
- App blocking for unsanctioned file sharing, messaging, or remote access tools directly at the DNS level.


In this lab, you’ll simulate attacks like:

- DNS data exfiltration using base64-encoded subdomains (exfil.highvelocitynetworking.com)
- Lookups to known malware, botnet, and command-and-control (C2) domains
- Optional category-based filtering (like blocking adult content or proxies)

You’ll witness the difference with and without Infoblox in the path.


# Business Value

Without DNS-layer security:
- Malware and AI agents can leak sensitive data over DNS
- Users may access risky domains without detection
- DNS queries bypass firewalls, proxies, and EDR tools

With Infoblox DNS security:
- DNS is centrally enforced across on-prem, cloud, and hybrid infrastructure
- Exfiltration attempts are blocked before data leaves
- Teams gain real-time visibility, audit trails, and analytics
- Consistent policy application via removing management silos and aligning DNS security policies across all types infrastructure... including the cloud



> [!IMPORTANT]
> **NOTE:** This environment is *real*! AWS Cloud Accounts have been created for each student. No bitcoin mining, please! :)

In this section we will:
1) Review the cloud architecture
2) Login to your cloud account console's
3) Deploy resources onto your cloud regions
4) Create your Infoblox Portal user




---

## 1) Review Cloud Architecture
===

First let’s review the cloud architecture that has been provisioned for your Infoblox Lab experience

Navigate to the *Lab Diagram* tab above and review the diagram. This is what we're building today!




## 2) Login to your cloud account consoles
===

🔐 Access Instructions

Using the credentials below, log in to the AWS and Azure Web Consoles.

---
# AWS Credentials ☁️

🔐 Logging In to the AWS Console

👉 First, open the “AWS Console” tab on the left-hand side of your Instruqt lab environment. This will launch the AWS login page in a new browser panel.

![Screenshot 2025-07-12 at 11.23.29.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/86d80bec0e3af0161dbb62f6e26e2626/assets/Screenshot%202025-07-12%20at%2011.23.29.png)

Then follow these steps:
1.	Select “IAM Account”
On the login screen, choose IAM Account (not root).

![Screenshot 2025-07-12 at 11.23.29.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/86d80bec0e3af0161dbb62f6e26e2626/assets/Screenshot%202025-07-12%20at%2011.23.29.png)

2.	Enter the AWS Account ID, AWS IAM username, and password by copying and pasting the values from the section below.

📝 Note: Avoid the root account login — this lab is configured for IAM users only.

**AWS Account ID**
```
[[ Instruqt-Var key="INSTRUQT_AWS_ACCOUNT_INFOBLOX_DEMO_ACCOUNT_ID" hostname="shell" ]]
```

**AWS IAM Username**
```
[[ Instruqt-Var key="INSTRUQT_AWS_ACCOUNT_INFOBLOX_DEMO_USERNAME" hostname="shell" ]]
```

**AWS Password**
```
[[ Instruqt-Var key="INSTRUQT_AWS_ACCOUNT_INFOBLOX_DEMO_PASSWORD" hostname="shell" ]]
```
---
# AZURE Credentials ☁️

👉 Open the Instruqt tab on the left labeled “AZURE Console”
This will launch the Azure login page directly inside your sandbox environment.

⸻

🧭 Step-by-Step Instructions
1.	Click “Sign In”
On the landing page, click the “Sign in” button at the top right or in the black banner.
2.	Enter Credentials
Use the Azure username and password provided under **AZURE CREDENTIALS**.
3.	Skip the Microsoft Onboarding Tour
If prompted with a “Get Started with Azure” setup wizard or tour:
•	Click “Skip”, “Maybe later”, or “Dismiss”
•	Do not start a free trial or create new subscriptions
•	You are already working in a pre-provisioned lab environment
4.	Ready to Go
Once logged in, use the top search bar to navigate to services like:
•	Virtual Network
•	Private DNS Zones
•	Resource groups

AZURE CREDENTIALS

---

**AZURE SUBSCRIPTION**
```
[[ Instruqt-Var key="INSTRUQT_AZURE_SUBSCRIPTION_INFOBLOX_TENANT_SUBSCRIPTION_ID" hostname="shell" ]]
```

**AZURE Username**
```
[[ Instruqt-Var key="INSTRUQT_AZURE_SUBSCRIPTION_INFOBLOX_TENANT_USERNAME" hostname="shell" ]]
```

**AZURE Password**
```
[[ Instruqt-Var key="INSTRUQT_AZURE_SUBSCRIPTION_INFOBLOX_TENANT_PASSWORD" hostname="shell" ]]
```

---





## 3) Deploy resources onto your cloud regions
===

🧱 Deploying the Lab Infrastructure

Now that you’ve logged into both cloud consoles, it’s time to deploy the infrastructure that reflects the architecture shown in the lab diagram.

👉 Switch back to the “**>_ Shell**” tab in the left-side panel of your Instruqt lab to proceed.

⸻

📦 What Terraform Will Deploy

This step provisions key cloud resources using Terraform — designed to mirror the topology shown in the lab diagram, with components deployed into specific regions.

🌍 AWS Region: eu-west-2
- A dedicated VPC and subnet
- Internet Gateway (IGW) for outbound access
- Route Table for subnet association
- Subnets
- EIPs
- Security Groups
- Key Pair & Private Key File
- TGW optional
- VPN Gateway for secure connectivity to Azure
- A web server instance (WebSvcvProdEU1) with private IP 10.20.10.100

📌 Note: AWS DNS is not configured in this phase — all DNS management is demonstrated from the Azure and Infoblox side.

⸻

☁️ Azure Region: northerneurope
- Two VNets: WebProdEU_Vnet1 and WebProdEU_Vnet2
- Subnets
- NICs
- NSG
- SSH Access & Keys
- One VM per VNet:
- 10.10.1.100 (Vnet1)
- 10.30.1.100 (Vnet2)
- A Private DNS Zone with: A records for workloads
- VNet-to-Zone linkage to ensure name resolution works across VNets


### 1. Deploy AWS resources in EU

Apply the resources:

```run
cd ~/infoblox-lab/secure-ai-infoblox/terraform
terraform apply --auto-approve  -target=module.aws__instances_eu  -target=aws_vpn_gateway.vgw
```

### 2. Deploy Azure resources in North Europe

Apply the resources:

```run
terraform apply --auto-approve -target=module.azure_instances_eu
```

Set up the DNS infrastructure with the appropriate Vnet associations and A-records as outlined in the lab diagram.


```run
terraform apply --auto-approve -target=azurerm_private_dns_zone.private_dns_azone -target=azurerm_private_dns_zone_virtual_network_link.eu_vnet_links -target=azurerm_private_dns_a_record.eu_dns_records
```


## 4) Create an Admin User for your Infoblox Portal tenant
===

> [!IMPORTANT]
> Note: Use your Business Email for User Creation

Your user account and sandbox tenant have already been created. The next step is to input your password and activate your account.

> [!IMPORTANT]
> If you’ve never accessed the Infoblox Portal before using the email address you used to start this lab, please follow the steps outlined below in Section 1 to activate your account.
However, if you’ve previously engaged with the Infoblox Portal using the same email, your account is likely already active and associated with the correct tenant. In that case, you can skip Section 1, and optionally skip Section 2, unless you’ve forgotten your password and need to reset it.

### Section 1

1. Please check the inbox of the email account you used to register for the Infoblox Lab.
2. You will receive an email with the subject “Infoblox User Account Activation”. Open this email and click on the “Activate Account” button to proceed.

![Screenshot 2025-04-01 at 11.15.44.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/93d7e021e28c20d105ca34aace35d149/assets/Screenshot%202025-04-01%20at%2011.15.44.png)

4. A new browser window or tab will open, prompting you to create a new password. Please enter your desired password to complete the setup.

![Screenshot 2025-04-01 at 11.19.01.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/0fd5315d47a283fce641de81b289ac64/assets/Screenshot%202025-04-01%20at%2011.19.01.png)

5. Once you’ve set your password, close the newly opened tab or window. We’ll be logging in through the Instruqt tab labeled "Infoblox Portal".
6. In the Instruqt tab labeled "Infoblox Portal", log in using the credentials you set up in the previous steps.
7. After logging in, please mark the first window as shown in the example below. This confirms that you have successfully accessed the Infoblox Portal.

![Screenshot 2025-04-01 at 11.01.03.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/a082b5c7276e1c2e27eaaf7bee832089/assets/Screenshot%202025-04-01%20at%2011.01.03.png)

7. In the upper-left corner of the Infoblox Portal, click on the drop-down menu. Use the “Find Account” field to search for your sandbox by entering the "Sandbox-ID" shown below. It is important that you select your specific Sandbox-ID from the list and click on it to proceed.

**Your Sandbox ID**
```
[[ Instruqt-Var key="Sandbox_ID" hostname="shell" ]]
```

---


![Screenshot 2025-07-18 at 09.16.24.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/09e41ec1cf57a6f2cbe5d2c47721a26b/assets/Screenshot%202025-07-18%20at%2009.16.24.png)

---

### Section 2 ⚠️ (Troubleshooting) Help! I Forgot My Password


✅ Existing User

1.	Go to Infoblox Portal tab on the left.
2.	Log in using your existing email address and password.
3.	Once authenticated, the lab tenant will be automatically added to your list of available tenants (you’ll see it in the top-right tenant switcher).

![Screenshot 2025-07-18 at 09.16.24.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/09e41ec1cf57a6f2cbe5d2c47721a26b/assets/Screenshot%202025-07-18%20at%2009.16.24.png)

In order to RESET the password follow the steps below:

1. Please navigate to the Infoblox Portal page by clicking on the Infoblox Portal tab within the Instruqt lab environment. This will direct you to the appropriate login interface.
2. Once you’re on the Infoblox Portal page, click on “Need Assistance” located at the bottom of the login form.

![Screenshot 2025-04-01 at 10.52.47.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/0bacd7dc4193c2770df54b65c7eceb3a/assets/Screenshot%202025-04-01%20at%2010.52.47.png)

3. After clicking on “Need Assistance”, select “Forgot Password” from the available options to initiate the password reset process.

![Screenshot 2025-04-01 at 10.52.57.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/3971464a872c428ee8879f3d7287b201/assets/Screenshot%202025-04-01%20at%2010.52.57.png)

4. You will receive an email with the subject “Account Password Reset.” Open this email and click on the “Reset Password” button to proceed with setting a new password.

![Screenshot 2025-04-01 at 11.42.21.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/263657baa43cefce4e8fc2062d2371f1/assets/Screenshot%202025-04-01%20at%2011.42.21.png)

5. Once you’ve set up your password, please return to "Section 1" of the instructions and continue from Step 4 onward.




## Time for the Next Challenge

Now we've inspected the playing field its game time. Click **NEXT**!

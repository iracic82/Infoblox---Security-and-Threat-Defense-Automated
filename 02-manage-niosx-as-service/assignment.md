---
slug: manage-niosx-as-service
id: zfxllu1rlixh
type: challenge
title: Deploying and Activating Infoblox NIOSX-as-a-service
teaser: Deploying Infoblox UDDI SaaS offering - DNS and Security Services Enabled
notes:
- type: video
  url: https://videos.infoblox.com/watch/TYegXjnJ2ocSZZyyaVAqTo?
tabs:
- id: cftkckvdahvz
  title: Shell
  type: terminal
  hostname: shell
- id: fbs6xkzlh4zl
  title: Editor
  type: code
  hostname: shell
  path: /root/infoblox-lab
- id: b5m2xf8jndyq
  title: AWS Console
  type: browser
  hostname: aws
- id: t9k5a3mu55ev
  title: Azure Console
  type: browser
  hostname: azure
- id: rotsniymtyi2
  title: Infoblox Portal
  type: browser
  hostname: infoblox
- id: bwk4qga7kssi
  title: Lab Diagram
  type: browser
  hostname: lab
difficulty: ""
timelimit: 0
lab_config:
  default_layout_sidebar_size: 0
enhanced_loading: null
---
🔧 Challenge Intro: Activating Infoblox NIOS X-as-a-Service (NIOS XaaS)

DNS is foundational — but in a multicloud world, managing and securing it across fragmented infrastructure can quickly become a liability.

In this challenge, you’ll activate Infoblox NIOS X-as-a-Service — a fully managed, cloud-delivered DNS platform operated by Infoblox. This is not a virtual appliance you deploy — it’s a zero-footprint SaaS model where Infoblox handles all the heavy lifting:
	•	No VM provisioning
	•	No patching
	•	No scaling headaches

Just plug it in, and go.

![Screenshot 2025-07-03 at 20.28.27.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/b4d88e7b711cf18a0054479f3ecc8bea/assets/Screenshot%202025-07-03%20at%2020.28.27.png)



🚀 What You’ll Do

You’ll walk through the activation steps via the Infoblox Cloud Services Portal using the official guide:
Creating As-a-Service

Key actions include:
	•	Selecting your cloud region
	•	Assigning a service name
	•	Enabling DNS visibility and security services
	•	Connecting downstream sites or cloud VPCs

Once provisioned, your NIOS-X-as-a-service will act as a cloud-native DNS control plane for enforcing policy, inspecting queries, and managing DDI services globally — no infrastructure required.


![Screenshot 2025-07-03 at 20.29.32.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/e16bccc5da63c7678165476f470dbb07/assets/Screenshot%202025-07-03%20at%2020.29.32.png)

## 1) Login to your cloud account consoles
===

🔐 (Optional) Log in to Your Cloud Account Consoles

You should already be signed in to both the AWS and Azure web consoles via the embedded tabs on the left-hand side of the Instruqt interface.

⚠️ Only perform this step if you’re logged out or the session has expired.

- Use the credentials provided in the lab instructions to sign in.
- Skip any onboarding wizards or tutorials (especially on Azure).
- If prompted for account type on AWS, select IAM Account.

---
# AWS Credentials ☁️

Select "IAM Account" and enter the **AWS ID**:
```
[[ Instruqt-Var key="INSTRUQT_AWS_ACCOUNT_INFOBLOX_DEMO_ACCOUNT_ID" hostname="shell" ]]
```

**AWS Username**
```
[[ Instruqt-Var key="INSTRUQT_AWS_ACCOUNT_INFOBLOX_DEMO_USERNAME" hostname="shell" ]]
```

**AWS Password**
```
[[ Instruqt-Var key="INSTRUQT_AWS_ACCOUNT_INFOBLOX_DEMO_PASSWORD" hostname="shell" ]]
```
---

# AZURE Credentials ☁️

**AZURE SUBSCRIPTION**
```
[[ Instruqt-Var key="INSTRUQT_AZURE_SUBSCRIPTION_INFOBLOX_TENANT_SUBSCRIPTION_ID" hostname="shell" ]]
```

**AZURE USERNAME**
```
[[ Instruqt-Var key="INSTRUQT_AZURE_SUBSCRIPTION_INFOBLOX_TENANT_USERNAME" hostname="shell" ]]
```

**AZURE PASSWORD**
```
[[ Instruqt-Var key="INSTRUQT_AZURE_SUBSCRIPTION_INFOBLOX_TENANT_PASSWORD" hostname="shell" ]]
```


---


## ☁️ Cloud Discovery Overview ( Azure)
===

**Infoblox Universal Asset Insights** automatically discovers and tracks cloud resources using native cloud APIs.

This reduces manual overhead, keeps your inventory fresh, and enables deeper asset visibility across your multicloud environment.

⸻

🔹 Azure Discovery

Infoblox connects to Azure using a service principal (App registration) with a custom or built-in role assignment at the subscription or resource group level. Discovered resources include:

- Virtual Machines and associated NICs
- Virtual Networks (VNets), subnets, peerings
- Network Security Groups, DNS zones and records
- Resource groups, tags, and regional metadata

⸻

🔐 Azure Permissions Required for Universal DDI



✅ Minimum Roles Required


- Reader:

Grants read-only visibility into Azure resources.

Used for IPAM sync, asset discovery, and general inventory collection.

✅ Safe default for read-only discovery.

NOTE: For more information, see [Reader role](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles/general#reader).

⸻

- DNS Zone Contributor:


Full management of public DNS zones and record sets.

❌ Does not grant access control (RBAC).

NOTE: For more information, see [DNS Contributor role](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#dns-zone-contributor)

⸻

- Private DNS Zone Contributor:

Same as above, but scoped to private DNS zones.

❌ Does not control virtual network links.

NOTE: For more information, see [Private DNS Zone Contributor](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#private-dns-zone-contributor).

⸻


⚙️ Resource Group Management


If your setup creates or manages resource groups, the following actions are required:

- Microsoft.Resources/subscriptions/resourceGroups/write
- Microsoft.Resources/subscriptions/resourceGroups/delete



📌 Used when the deployment dynamically creates resource groups for forwarders or discovery.


⸻

🧾 App Registration Prerequisites


To register an application (service principal), your Azure user must have the:

- Cloud Application Administrator role
- Fulfill [App Registration prerequisites](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app#prerequisites)



⸻


🔍 Permissions Required for Cloud Forwarder Discovery Only


If you only need to read DNS Resolver config, the following permissions are sufficient:


```
- Microsoft.Network/dnsResolvers/read
- Microsoft.Network/dnsResolvers/inboundEndpoints/read
- Microsoft.Network/dnsResolvers/outboundEndpoints/read
- Microsoft.Network/dnsForwardingRulesets/read
- Microsoft.Network/dnsForwardingRulesets/forwardingRules/read
- Microsoft.Network/dnsForwardingRulesets/virtualNetworkLinks/read
```

⸻

## 2) Onboarding Azure account onto Infoblox Portal
===


Azure DNS is a cloud DNS web service that routes end users’ requests to internet applications by resolving domain names into IP addresses and vice versa. In Azure, DNS records are organized into hosted zones, which can be configured using the Azure Portal, CLI, or Resource Manager templates.

Universal DDI provides the capability to synchronize and integrate both public and private hosted zones with Azure DNS, allowing users to view and manage Azure DNS data directly from the Infoblox Portal. Additionally, Infoblox NIOS-X-as-a-Service servers can be configured to serve zones synchronized from Azure.


The Infoblox Azure DNS integration offers much more, but let me highlight some of the capabilities:
- ✅ Two-way synchronization of public and private DNS zones and records between Azure and Universal DDI, after initial configuration is complete.
- 🔐 DNSSEC record types are not supported for synchronization.
- 🛠️ Azure Private Zones are fully read-write, not read-only. Records can be created, modified, or deleted from within UDDI and will sync to Azure.
- ⚡ When changes are made in UDDI, they are immediately pushed to Azure DNS.
- ⏱️ The sync interval defines how often UDDI polls Azure to check for changes made natively within Azure (Azure → UDDI).
- 🧩 Universal DDI supports multi-subscription discovery, making it ideal for distributed and enterprise-scale DNS architectures.

### Step-by-Step Guide: Azure Discovery Configuration via Infoblox Portal

#### 1. Navigate to the Infoblox Portal and go to Configure → Administration → Credentials.

![Screenshot 2025-04-02 at 21.47.14.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/f1ba860ea63974329f57911a5bbcad83/assets/Screenshot%202025-04-02%20at%2021.47.14.png)

#### 2. Click Create and select Microsoft Azure from the dropdown menu.

![Screenshot 2025-04-02 at 21.47.48.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/1b8e062242f5ed8318d4aceaa3e9847b/assets/Screenshot%202025-04-02%20at%2021.47.48.png)



#### 3. Fill in all required fields marked with an asterisk (*) using the details provided below and click "Save&Close".

> [!IMPORTANT]
> NOTE: Please do not forget to give it a Name at the top.


**AZURE Tenant ID**
```
[[ Instruqt-Var key="INSTRUQT_AZURE_SUBSCRIPTION_INFOBLOX_TENANT_TENANT_ID" hostname="shell" ]]
```

**AZURE Client ID**
```
[[ Instruqt-Var key="INSTRUQT_AZURE_SUBSCRIPTION_INFOBLOX_TENANT_SPN_ID" hostname="shell" ]]
```

**AZURE Client Secret**
```
[[ Instruqt-Var key="INSTRUQT_AZURE_SUBSCRIPTION_INFOBLOX_TENANT_SPN_PASSWORD" hostname="shell" ]]
```

#### 4. Access the Infoblox Portal

1.	Navigate to the Infoblox Portal
2.	From the top navigation menu, go to:

Configure → Networking → Discovery.


#### 5. Configure Azure Discovery
1.	Within the Discovery section, select the Cloud tab.
2.	Click on Create Azure to begin setting up cloud discovery for Azure.


#### 6. Fill in all required fields marked with an asterisk (*) using the details provided below.

![Screenshot 2025-04-02 at 22.01.10.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/6cbc9dd38a1dc014d2ddb8ed7df4ac4f/assets/Screenshot%202025-04-02%20at%2022.01.10.png)

> [!IMPORTANT]
> Please give it a name and Select "Type of Access" -> Static→ under "Credentials" select the one you have created in the previous step.

Azure subscription id can be found below.

**AZURE SUBSCRIPTION ID**
```
[[ Instruqt-Var key="INSTRUQT_AZURE_SUBSCRIPTION_INFOBLOX_TENANT_SUBSCRIPTION_ID" hostname="shell" ]]
```

#### 7.On the next page, configure the settings to match those shown in the screenshot below, then click "Next" to continue.


💡 Tip: The ‘Exclude Asset Types’ section isn’t just for filtering — it’s a powerful way to see exactly which asset types are being discovered from Azure.

➡️ Click the expand arrows (▸) next to each asset group to reveal the full list of resource types being ingested.

This helps you understand the depth of Azure visibility Infoblox Universal DDI provides — from VNets and NICs to DNS zones, Private Endpoints, and more.

![Screenshot 2025-04-02 at 22.09.04.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/098541c13a407a9df0da2581d6f2970b/assets/Screenshot%202025-04-02%20at%2022.09.04.png)

#### 8.On the next page, configure the settings to match those shown in the screenshot below, then click "Next" to continue.

![Screenshot 2025-04-02 at 22.10.42.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/6b6c728e758842db9ca7325b6a5cad7b/assets/Screenshot%202025-04-02%20at%2022.10.42.png)

#### 9.On the next page, configure the settings to match those shown in the screenshot below, then click "Save&Close" to continue.

![Screenshot 2025-04-02 at 22.11.18.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/51b0aaef0a1de4b8cdba1bf96735eb2a/assets/Screenshot%202025-04-02%20at%2022.11.18.png)


✅ Step 1: Verify Discovery Sync

Navigate to Infoblox > Networking > Discovery and confirm that your Azure Discovery Job is in a Synced state:

> [!IMPORTANT]
> ⏱ Discovery jobs typically take up to 2x the sync interval (default: 15 mins) to complete. Plan for ~30 minutes after job creation before expecting a Synced status.

![Screenshot 2025-07-03 at 06.13.17.png](https://play.instruqt.com/assets/tracks/ywozzymyekgv/806b671c5740f908a52bd6b756010b22/assets/Screenshot%202025-07-03%20at%2006.13.17.png)


🧠 Why it matters:

Why is syncing important?

When a discovery job is not in a Synced state, you’re operating with outdated or incomplete visibility. Once sync completes, Infoblox UDDI pulls the most recent datasets (like zones, records, VMs, VNets) from Azure into its database. This enables:
- Real-time visibility of DNS configurations
- Accurate audit logging
- Consistent control across multicloud
- Baseline for automation workflows
If the sync isn’t completed, these capabilities are hindered—especially for tasks that rely on current state data.

> [!NOTE]
> 📝 Note:
After configuring the Azure Discovery Job, you can proceed with the next steps immediately. The Azure sync process will run in the background and may take a few minutes to complete, depending on the size of your environment.



## 3) Deploying NIOS-X as a Service
===

# NIOS-X as a Service

NIOS-X as a Service offers advanced cloud delivery for critical network services in hybrid, multi-cloud environments. It ensures operational efficiency and reliability by using public cloud points of presence across multiple regions, eliminating the need for physical or virtual appliances.
The industry’s most advanced DDI deployment model for hybrid, multi-cloud environments.

## Workflow
To enable and use NIOS-X as a Service perform the following workflow:
1. Create a Service Deployment for NIOS-X as a Service.
2. Acquire the Identity string and Cloud Service IP from the Service Deployment.
3. Establish an IPsec tunnel between an AWS VPN and Infoblox(Re-Config).
4. Test/Verify the configuration.


> [!IMPORTANT]
> Note: Ensure all configurations follow the lab topology as shown in the diagram - Lab Tab. Use the AWS region eu-west-2 (London) for all resource deployments.



## 1. Create a Service Deployment on Infoblox Portal for NIOS-X as a Service.


This portion of the guide is intended to guide Infoblox Portal administrators through the creation of a Service
Deployment for NIOS-X as a Service. The Service Deployment acts as the point of access for NIOS-X as a
Service’s DNS, and DNS Security. To create a Service Deployment for NIOS-X as a Service complete the
following steps:
1. Navigate to the As-A-Service page of the Infoblox Portal by highlighting **Configure,** clicking **Service Deployment**, then by clicking **As-A-Service** in the navigation panel.
![Jun-23-2025_at_21.47.25-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/ef250f39faaa4f82a041b5e648d6f017/assets/Jun-23-2025_at_21.47.25-image.png)
2. Click **Add Service** near the bottom left of the As-A-Service page.
![Jun-23-2025_at_21.48.01-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/60883e25c289b6db671ce943806ac912/assets/Jun-23-2025_at_21.48.01-image.png)
3.  Give the new Service a **Name**.
![Jun-23-2025_at_21.55.52-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/f8f237f5620096e2b627957810fc001f/assets/Jun-23-2025_at_21.55.52-image.png)
4.  (Optional) Input a **Description**.
![Jun-23-2025_at_21.56.34-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/e36fbd69cfa831608d16bdcfe579951f/assets/Jun-23-2025_at_21.56.34-image.png)
5.  Click **Add** on CAPABILITIES option and select each service that NIOS-X as a Service will be serving.

> [!NOTE]
> 📌 Note: “Capabilities” define what services the deployment will support, such as DNS resolution or DNS Security (Threat Protection). Choose what aligns with your use case.

![Jun-23-2025_at_22.02.25-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/93ab9e2e7e0b83e03438ded03cbde983/assets/Jun-23-2025_at_22.02.25-image.png)
6.  Select **DNS** here.
![Screenshot 2025-08-07 at 13.31.06.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/7ac935709da3ddf994d4d749035be891/assets/Screenshot%202025-08-07%20at%2013.31.06.png)
![Screenshot 2025-07-10 at 16.32.17.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/9351147ebbee194d378a0a37e44cd1be/assets/Screenshot%202025-07-10%20at%2016.32.17.png)
7.  Keep the default settings as they are and click **Add Capability**.
![Screenshot 2025-07-10 at 16.23.49.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/9142ec33e9893647f058899664683d48/assets/Screenshot%202025-07-10%20at%2016.23.49.png)

8.Click **Add** under CAPABILITIES Section again.

![Screenshot 2025-07-10 at 16.24.10.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/ec67f36955f8fee2001ab3c153698477/assets/Screenshot%202025-07-10%20at%2016.24.10.png)

9.Select **Security** here.

![Screenshot 2025-07-10 at 16.24.20.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/db28a58988abbd1ed863cd17f2ebdf67/assets/Screenshot%202025-07-10%20at%2016.24.20.png)

10.Click **Add Capability** on the bottom.

11.Switch to **Deployment** tab.

> [!IMPORTANT]
> NOTE: ⚠️We are still on Add Service Wizard Page.

![Jun-24-2025_at_00.10.26-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/bcb672c7fb25ba1c4245f008e626c27c/assets/Jun-24-2025_at_00.10.26-image.png)
12.  Select **Add Service Deployment**.
![Jun-23-2025_at_22.06.11-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/9a8efe0efe962857148c6eb8e4f372f9/assets/Jun-23-2025_at_22.06.11-image.png)
13.  Give the new Deployment a **Name**.
![Jun-23-2025_at_22.07.32-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/eef25cf3bd06b9bd601de829f62c78f1/assets/Jun-23-2025_at_22.07.32-image.png)
14.  Configure **Service Location** as given.
-  **Size** -  Select **S**
-  **Provider** -  Select **AWS**
-  **Uncheck** Use Recommended Location
-  **Location** -  **As  preferred  by you**

> [!IMPORTANT]
> 🗺️ Region Selection – Frankfurt (eu-central-1)
In this lab, we will use the AWS Europe (Frankfurt) Region – eu-central-1.

📍 This region selection is important because Infoblox NIOS-X-as-a-Service will be offered from this region to the end customer. That means all core infrastructure, service endpoints, and resources tied to the NIOS-XaaS deployment will be hosted in the Frankfurt regional Point-of-Presence (POP).


-  **Service IP**- **this will be IP for your NIOS-X (DNS resolver IP)** (try to keep it far away from your AWS VPC network CIDR they should not fall in same CIDR )

> [!IMPORTANT]
> NOTE: Service IP should be set to 10.10.10.3

Copy Service IP from below into the Infoblox portal

```
10.10.10.3
```


🛑 Important: The Service IP is a private IP( can not be Public)  address. Ensure it does NOT overlap with any AWS VPC CIDR range.

![Screenshot 2025-07-08 at 07.46.36.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/70145290091530abf58a7f668f7faa2f/assets/Screenshot%202025-07-08%20at%2007.46.36.png)

15. Configure **Service Location Routing** as given:
- **Routing** -  Select **Dynamic(BGP)**
- **Service Location ASN**  -  Enter **65500**

🧠 Note: This ASN will be used when configuring the Customer Gateway (CGW) in AWS. Memorize or save it.

- **Primary Source IP **
- **Secondary Source IP **


> [!IMPORTANT]
> NOTE: Primary and Secondary Source IPs should be set retrospectively to 10.10.10.4 and 10.10.10.5.

**Primary Source IP**
```
10.10.10.4
```
**Secondary Source IP**
```
10.10.10.5
```

📌 These source IPs are used for services like zone transfers and DNS forwarding.

![Screenshot 2025-07-08 at 07.47.18.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/9133754c8a7d27cf9d514ce5e02c2474/assets/Screenshot%202025-07-08%20at%2007.47.18.png)

16.  Configure **Access Location**:
- Click **Add**
- **Provider** -  **AWS**
- **Type** - **Cloud VPN**
- **Region** - **Select as stated on the Lab Diagram - Europe(London)**
- **Name** -  **name your access location**

Name for access location

```
Instrqt-AL
```

![Screenshot 2025-08-08 at 11.14.57.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/5a1b1624f5af488f3a7d37e681176726/assets/Screenshot%202025-08-08%20at%2011.14.57.png)
17.  Configure **CONNECTION**.
17.1 click **Add Primary**.
![Jun-23-2025_at_23.18.57-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/6de0d05fa4742d9e6a281a7af4aa4a48/assets/Jun-23-2025_at_23.18.57-image.png)
- give name to your primary connection
- configure **Tunnel**
- click **Add Primary**
![Screenshot 2025-08-08 at 10.00.08.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/8046778c72f8abf6a43158b6f8957cf4/assets/Screenshot%202025-08-08%20at%2010.00.08.png)
- Configure **Add Primary Tunnel**
![Screenshot 2025-08-08 at 10.01.22.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/9a8e391b0ec54e8c05ca66b47dbd66e8/assets/Screenshot%202025-08-08%20at%2010.01.22.png)
- **Tunnel Outside IP**  -  Give in a dummy IP as we will configure this later again.
- Click  **Add Credential**
- Select **New**
![Jun-23-2025_at_23.27.38-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/bebd8949b63de9bc605f58dabbd0dc64/assets/Jun-23-2025_at_23.27.38-image.png)
- **Name** - any string is fine but you cannot repeat them while adding it on an another connection
- **Description**  -  enter your desired description
- **Pre-shared Key**  -  Please enter at least 16 characters Value must contain at least one special character either a underscore(_) or a dot (.) {remember this value as it will be used to configure the AWS VPN}

Pre-shared key value for the lab you can find below:

```
InfobloxDNSLab2025.
```

- Click **Add Credential**
![Jun-23-2025_at_23.46.30-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/43bdbf13333297d12646d12d39553f83/assets/Jun-23-2025_at_23.46.30-image.png)
- Configure **BGP**
- Enter the Dummy values at  **Inside IPv4 CIDR**  and  **Access Location ASN   as it will be reconfigured.
- Click **Add Primary Tunnel**
![Screenshot 2025-08-08 at 10.03.28.png](https://play.instruqt.com/assets/tracks/26xnz6aweydm/8aefc8c1eb02aaaaded59ceaff9ec17e/assets/Screenshot%202025-08-08%20at%2010.03.28.png)
- Click **Add Primary Connection**
![Jun-23-2025_at_23.33.40-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/9a26d9c5857aa778823016db47fca9b8/assets/Jun-23-2025_at_23.33.40-image.png)
17.2 (Optional) click **Add Secondary**.

🛡️ Why Create a Secondary Connection?

•	Ensures redundancy and high availability — NIOS-X-as-a-Service always deploys two virtual appliances across separate Availability Zones (AZs) for fault tolerance.
•	Follow the same steps as the primary connection, but use a different name while keeping the same pre-shared key.



![Jun-23-2025_at_23.41.20-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/341cc0537721ae693255e72b7a45a9e5/assets/Jun-23-2025_at_23.41.20-image.png)
- give name to your secondary connection
- configure **Tunnel**
- click **Add Secondary**
![Screenshot 2025-08-08 at 10.04.15.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/b10ce1db09d998ad8566d119c89c3ff6/assets/Screenshot%202025-08-08%20at%2010.04.15.png)
- **Tunnel Outside IP**  -  Give in a dummy IP as we will configure this later again.
- Click  **Add Credential**
- Select **New**
![Jun-23-2025_at_23.44.36-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/de93ac2733f1895567a7d6f612f86f1f/assets/Jun-23-2025_at_23.44.36-image.png)
- **Name** - any string is fine but you cannot repeat them while adding it on an another connection
- **Description**  -  enter your desired description
- **Pre-shared Key**  -  Please enter at least 16 characters Value must contain at least one special character either a dash(_) or a dot (.) {remember this value as it will be used to configure the AWS VPN}

Pre-shared key value for the lab you can find below:

```
InfobloxDNSLab2025.
```

- Click **Add Credential**
![Jun-23-2025_at_23.46.30-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/43bdbf13333297d12646d12d39553f83/assets/Jun-23-2025_at_23.46.30-image.png)
- Configure **BGP**
- Enter the Dummy values at  **Inside IPv4 CIDR**  and  **Access Location ASN**   as it will be reconfigured.
![Screenshot 2025-08-08 at 10.05.46.png](https://play.instruqt.com/assets/tracks/26xnz6aweydm/94d528496651f10cb298ca3c80a6bdcf/assets/Screenshot%202025-08-08%20at%2010.05.46.png)
- Click **Add Secondary Tunnel**
![Screenshot 2025-08-08 at 10.05.53.png](https://play.instruqt.com/assets/tracks/26xnz6aweydm/410eb0aca7ce1098481e67670752f56b/assets/Screenshot%202025-08-08%20at%2010.05.53.png)
- Click **Add Secondary Connection**
![Jun-23-2025_at_23.48.39-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/20187257b5fabe28df90fa9b985e8a04/assets/Jun-23-2025_at_23.48.39-image.png)
- Finally click **Add Location**
![Jun-23-2025_at_23.49.59-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/0292d885d013c51a6b63219811845420/assets/Jun-23-2025_at_23.49.59-image.png)
- Click **Add Deployment**
![Jun-23-2025_at_23.50.51-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/3c81136d742b21361dd092d245fcdf53/assets/Jun-23-2025_at_23.50.51-image.png)
18.  Click **Save** to save changes.

> [!NOTE]
> 📝 NOTE:
After editing the instructions, make sure to save your changes. To reveal the “Save Changes” button scroll around the portal using the trackpad or mouse until the button appears.
The button may be hidden by default depending on screen size or browser zoom level.

![Jun-23-2025_at_23.51.38-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/48c0f3e058adfa068d6b08f800a4b348/assets/Jun-23-2025_at_23.51.38-image.png)
19.  Wait approximately 3 minutes, then Refresh the Webpage
20.  You will see a Ready Status here.
![Jun-24-2025_at_00.03.36-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/925ad34b8029024c613494d7a41feedd/assets/Jun-24-2025_at_00.03.36-image.png)
21.  On that same page where is says **Ready Status** ( screenshot in the previous step 20 ) click on **Instrqt-AL** - it's a hyperlink below Location.
![Screenshot 2025-07-08 at 08.06.04.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/3201bd36904b56325226f89657c918ee/assets/Screenshot%202025-07-08%20at%2008.06.04.png)
22.  Locate and copy the Cloud Service IPs displayed on your screen — you’ll need them in a later step.

> [!IMPORTANT]
> Note: Cloud Service IPs may take up to 5 minutes to appear. Please refresh the page periodically and allow time for propagation.


🔍 Heads-Up!

If you’re unable to see the Cloud Service IP under the Service Deployment section, it might be due to your screen resolution or window size.

✅ Try minimizing the guide or dragging it to the right to reveal the full Infoblox Portal view.

On smaller screens or in split view, this section might be partially hidden — especially on iPads or laptops. Make sure you’re in full-screen mode for best visibility.


## 2. Configuring AWS

Go to the AWS Web Console and please make sure you are in **AWS Europe(London) region - eu-west-2**.

![Screenshot 2025-07-08 at 08.08.33.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/473d33368078adbac4cbbb57c119756b/assets/Screenshot%202025-07-08%20at%2008.08.33.png)!

In the left window of the lab environment, click on the AWS Console tab to launch the console.

Use the login credentials provided in the previous section 1)  to sign in.

1. Now we will create 2 x  **Costumer Gateways**.
2. Go to Virtual private network (VPN)>Customer gateways.
3. Click on **Create Costumer Gateway**.(creating the first CGW ).
![Jun-24-2025_at_00.51.52-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/c60386be95ea7d93eedef258630122df/assets/Jun-24-2025_at_00.51.52-image.png)
4. Fill in Details as mentioned below:
- **Name tag** - name for you CGW (optional).
- **BGP ASN** - give the BGP same value as you have given in step 12 at Service Location ASN : **65500**
- **IP Address** -  this will be the value of first IP in Cloud Service IP under Service Deployment.(These are the Public IPs)
- Fill in the option fields if you wish to
![Jun-24-2025_at_10.39.43-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/86f6db94075cc1e20e596f538aba50a6/assets/Jun-24-2025_at_10.39.43-image.png)
5. Click on **Create Costumer Gateway**.(creating the second CGW ).
![Jun-24-2025_at_00.51.52-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/c60386be95ea7d93eedef258630122df/assets/Jun-24-2025_at_00.51.52-image.png)
6. Fill in Details as mentioned below:
- **Name tag** - name for you CGW (optional).
- **BGP ASN** - give the BGP same value as you have given in step 12 of 1 at Service Location ASN : **65500**
- **IP Address** -  this will be the value of second IP in Cloud Service IP under Service Deployment.
- Fill in the option fields if you wish to
![Jun-24-2025_at_10.41.58-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/b15aa64ce0606207cb8d4db2101deb79/assets/Jun-24-2025_at_10.41.58-image.png)

7. Now you will be creating two **Site-to-Site VPN connections**.

In the AWS Console, use the search bar at the top and type VPC, then click on the VPC service.
🔹 Make sure you are in the eu-west-2 region, as shown in the lab diagram.

8. Go to Virtual Private Network (VPN) --> Site-to-Site VPN connections.
9. Creating First Site-to-Site VPN connections.
- Click **Create VPN Connection**
![Jun-24-2025_at_11.09.24-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/887cac81f291c1b356a2ed488118be2d/assets/Jun-24-2025_at_11.09.24-image.png)
- **Name tag - optional** - enter the name for your VPN connection
- **Target gateway type** - keep it as **Virtual private gateway** and select the one available from the drop down menu
      > [!NOTE]
> NOTE: VGW name is VGW-Lab
- **Customer gateway** -  keep it as **Existing** and select the one you created in step 10 of 2
- **Routing options**-  keep it as **Dynamic (requires BGP)**
- **Pre-shared key storage** - keep it as **Standard**
![Jun-24-2025_at_11.18.26-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/26f273e7c0200545603725645567f84a/assets/Jun-24-2025_at_11.18.26-image.png)
- Expand **Tunnel 1 options  - optional **
![Jun-24-2025_at_11.20.27-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/49bb4bfa3db0437166d3d7622a9e1867/assets/Jun-24-2025_at_11.20.27-image.png)
- **Inside IPv4 CIDR for tunnel 1** - A size /30 IPv4 CIDR block from the 169.254.0.0/16 range.(Must be a valid CIDR)

> [!IMPORTANT]
> NOTE:  Subnet should be 169.254.21.0/30


```
169.254.21.0/30
```

- **Pre-shared key for tunnel 1** - Entered the key from step 14.1 of 1
- **Advanced options for tunnel 1** -  Select **Edit tunnel 1 options**
- leave all the options as they are
- **Startup Action** -  **Start**
- Click **Create VPN connection**
![Jun-24-2025_at_11.26.07-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/5f84fcebd0715247a709046e527a514e/assets/Jun-24-2025_at_11.26.07-image.png)
10. Creating Second Site-to-Site VPN connections:
- Click **Create VPN Connection**
![Jun-24-2025_at_11.09.24-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/887cac81f291c1b356a2ed488118be2d/assets/Jun-24-2025_at_11.09.24-image.png)
- **Name tag - optional** - enter the name for your VPN connection
- **Target gateway type** - keep it as **Virtual private gateway** and select the one you created in step 14 of 2
- **Customer gateway** -  keep it as **Existing** and select the one you created in step 12 of 2
- **Routing options**-  keep it as **Dynamic (requires BGP)**
- **Pre-shared key storage** - keep it as **Standard**
![Jun-24-2025_at_11.38.36-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/f8638d4a2a56c50366551989c43970a8/assets/Jun-24-2025_at_11.38.36-image.png)
- Expand **Tunnel 1 options  - optional **
![Jun-24-2025_at_11.20.27-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/49bb4bfa3db0437166d3d7622a9e1867/assets/Jun-24-2025_at_11.20.27-image.png)
- **Inside IPv4 CIDR for tunnel 1** - A size /30 IPv4 CIDR block from the 169.254.0.0/16 range. (Must be a valid CIDR)

> [!IMPORTANT]
> NOTE:  Subnet should be 169.254.22.0/30

```
169.254.22.0/30
```

- **Pre-shared key for tunnel 1** - Entered the key from step 14.2 of 1
- **Advanced options for tunnel 1** ---->. Select **Edit tunnel 1 options**
-** leave all the options as they are**
- **Startup Action** -  **Start**

> [!IMPORTANT]
> NOTE - Make sure you select START option - AWS initiates the connection

![Screenshot 2025-07-02 at 12.59.35.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/c81caaacf57221c43bbaaa562bacdc5b/assets/Screenshot%202025-07-02%20at%2012.59.35.png)

![Screenshot 2025-07-02 at 12.59.49.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/2e1726f5baf52ce33a919e7714288ee5/assets/Screenshot%202025-07-02%20at%2012.59.49.png)

- Click **Create VPN connection**
![Jun-24-2025_at_11.26.07-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/5f84fcebd0715247a709046e527a514e/assets/Jun-24-2025_at_11.26.07-image.png)


11. Enable the Route Propogation on your VPC.
12. Go to VPC.
13. Select the section **Route Tables**.

![Screenshot 2025-07-01 at 09.21.35.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/15c8a261a0fe9f3066f46ddf9d156ef5/assets/Screenshot%202025-07-01%20at%2009.21.35.png)

14. Go to **Route Propagation** tab.
![Jun-24-2025_at_11.55.27-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/ccc94a5ade7ceace6a2a67c48cbaee92/assets/Jun-24-2025_at_11.55.27-image.png)
15. Click **Edit Route Propagation**.
![Jun-24-2025_at_11.57.03-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/3ddbbb4a393067f373d1ae9baf77e6a2/assets/Jun-24-2025_at_11.57.03-image.png)
16. Check **Enable** and hit **Save**.
![Jun-24-2025_at_11.57.48-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/ae65906796d6f59133ef5e56e9ab6324/assets/Jun-24-2025_at_11.57.48-image.png)


## 3.  Establish an IPsec tunnel between a AWS VPN and Infoblox(Re-Config).

1. In your AWS console go to Site-to-Site VPN connections.
2. Go to Tunnel-1 that you configured in step 23 of 2.
3. Copy **Outside IP address** of Tunnel-1.
4. Log in to your Infoblox Portal again and navigate to  Configure >Service Deployment >As-A-Service > **Your service**.
5. Click on three dots associated with your NIOS-X-as-a-Service deployment service name and click Edit.
![Jun-24-2025_at_12.06.47-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/91cf73f8583026f6103666275ac94d88/assets/Jun-24-2025_at_12.06.47-image.png)
6. Click on three dots in front of your service deployment in the Service Deployments panel and click Edit.
![Jun-24-2025_at_12.07.10-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/adad18ceee12e2a5453f289c6db0477b/assets/Jun-24-2025_at_12.07.10-image.png)
7. In the **Access Location** click on the drop downs and select your Access Location and click on three dots and select edit.
![Screenshot 2025-07-08 at 08.52.57.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/9a767deff7cb972f2298d0d547733b6b/assets/Screenshot%202025-07-08%20at%2008.52.57.png)
8. On **Edit Access Location window** select **Primary Connection**.
![Jun-24-2025_at_13.29.19-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/9a91abda47aedd83ab9f691e81024933/assets/Jun-24-2025_at_13.29.19-image.png)
9. Under the Connection header click Primary.
![Screenshot 2025-08-08 at 10.27.59.png](https://play.instruqt.com/assets/tracks/26xnz6aweydm/8684a9ab0849ad5363a96c77d8e8d060/assets/Screenshot%202025-08-08%20at%2010.27.59.png)
10. Change Inside IPv4 CIDR as **Outside IP address** of Tunnel-1   of VPN-1from step 3 of 3.
11. On **BGP** in **Inside IPv4 CIDR**   update the **Inside IPv4 CIDR** of Tunnel-1 on AWS side.

> [!IMPORTANT]
	> NOTE: Neighbor IP is 169.254.21.1/30

13. For **Access Location ASN** copy paste the value from **Amazon ASN**  **64512**.

```
64512
```

![Screenshot 2025-08-08 at 10.29.20.png](https://play.instruqt.com/assets/tracks/26xnz6aweydm/b0f84a150264779463c4f958df747784/assets/Screenshot%202025-08-08%20at%2010.29.20.png)

14. Click Edit Primary Tunnel.
![Screenshot 2025-08-08 at 10.29.44.png](https://play.instruqt.com/assets/tracks/26xnz6aweydm/8e10e7dbb55d7cffbf796bbf65a5791e/assets/Screenshot%202025-08-08%20at%2010.29.44.png)
15. Click Edit Primary Connection.
![Jun-24-2025_at_13.35.58-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/4f432f179e2ae47dd1d294d8cf50b54a/assets/Jun-24-2025_at_13.35.58-image.png)
16. On **Edit Access Location window** select **Secondary Connection**.
![Jun-24-2025_at_13.37.15-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/cd5fdbf2ce9f6acdada6a4de379e0b8d/assets/Jun-24-2025_at_13.37.15-image.png)
17. Under the Tunnel header, click Secondary.
![Screenshot 2025-08-08 at 10.30.37.png](https://play.instruqt.com/assets/tracks/26xnz6aweydm/2abeb04604b20cad3824d30790843c3b/assets/Screenshot%202025-08-08%20at%2010.30.37.png)
18. Change Tunnel Outside IP as **Outside IP address** of Tunnel-1   of VPN-2 from step 3 of 3.
19. On **BGP** in **Inside IPv4 CIDR**   update the **Inside IPv4 CIDR** of Tunnel-1 of VPN-2 on AWS side.

> [!IMPORTANT]
> NOTE: Neighbor IP is 169.254.22.1/30


20. For **Access Location ASN** copy paste the value from **Amazon ASN**  **64512**.

```
64512
```

21.  Click Edit Secondary Tunnel.
![Screenshot 2025-08-08 at 10.30.58.png](https://play.instruqt.com/assets/tracks/26xnz6aweydm/7368af1152702ac69d6bd312a2118db8/assets/Screenshot%202025-08-08%20at%2010.30.58.png)
22. Click Edit Secondary Connection.
![Jun-24-2025_at_13.49.11-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/54e8706e0a06c6ce3a9d9d1d2cb3d9c9/assets/Jun-24-2025_at_13.49.11-image.png)
23. Click Update Location.
![Jun-24-2025_at_13.50.15-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/38458b7ba2911d35a03df65b7a6b2dfa/assets/Jun-24-2025_at_13.50.15-image.png)
24. Click Update.
![Jun-24-2025_at_13.50.40-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/b7ed57f432cb1517ec75b2b46eecb834/assets/Jun-24-2025_at_13.50.40-image.png)
25. Click the **Save** button located at the top-right corner of the screen.
![Screenshot 2025-07-10 at 16.45.02.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/a4c509136bf917bc2920f49c47e9815a/assets/Screenshot%202025-07-10%20at%2016.45.02.png)
![Jun-24-2025_at_13.51.04-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/55d40f83c2d3f76ec00fb089450b31f7/assets/Jun-24-2025_at_13.51.04-image.png)
26. Now you should see a green square and connected as status in front of your Access Location under SERVICE STATUS.
![Jun-24-2025_at_14.10.05-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/a9b8a0d64a181aea6f7e4ec787c48f15/assets/Jun-24-2025_at_14.10.05-image.png)



## 4. Test/Verify the configuration.

1. On the Infoblox CSP tenant side you will see a green square and connected as status in front of your Access Location under SERVICE STATUS which indicates the service is UP.
![Jun-24-2025_at_14.10.05-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/a9b8a0d64a181aea6f7e4ec787c48f15/assets/Jun-24-2025_at_14.10.05-image.png)
2. On AWS console go to VPC and section Route Tables and select **WebSvcsProdEu1-RT** route table.
3. In Routes you must see the Service IPs - **Service IP: 10.10.10.3**.
![Jun-24-2025_at_14.32.04-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/39a9d2ebe179aca2923194d7f89ac9c4/assets/Jun-24-2025_at_14.32.04-image.png)
4. On AWS console go to Site-to-Site VPN connections and then navigate to VPN-1 that you created.
5. Notice the Tunnel state in Tunnel details  for Tunnel 1 status should be Up.
![Jun-24-2025_at_14.34.39-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/4c3b11b9e18ae30daa9dcefa725c5722/assets/Jun-24-2025_at_14.34.39-image.png)
6. On AWS console go to Site-to-Site VPN connections and then navigate to VPN-2 that you created.
7. Notice the Tunnel state in Tunnel details  for Tunnel 1 status should be Up.
![Jun-24-2025_at_14.37.05-image.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/e889d9b41101f6f259e2117185fc2700/assets/Jun-24-2025_at_14.37.05-image.png)



✅ NIOS-X-as-a-Service Deployment Complete
==

You’ve now successfully deployed Infoblox NIOS-X- as-a-Service in AWS using the Universal DDI Portal.

This sets the foundation for highly available DNS services and advanced policy enforcement.


🧠 Why this matters:

By deploying Infoblox NIOS-X-as-a-Service, you’ve stood up a cloud-native DNS server in just a few clicks — no manual software installs, no infrastructure headaches.

This setup gives you:

- High availability with automatic deployment across multiple AZs
- Centralized management from the Infoblox Portal
- Seamless integration with hybrid and multicloud environments

It’s DNS the way it should be — reliable, scalable, and effortless to manage.

⸻

🚀 Next Up

Get ready to:
	•	Configure AWS DHCP Option Sets to automatically push Infoblox DNS to EC2 instances
	•	Integrate Infoblox NIOS-X as the Authoritative DNS for Azure Private Zones


## 🧭 Configure AWS DHCP Option Set for Infoblox DNS
==

To automatically direct EC2 instances to use Infoblox DNS resolvers, create a custom DHCP option set and associate it with your target VPC. You can do this from the AWS Web Console — navigate to it via the left-hand side navigation panel.

---

### Step 1: Create a DHCP Option Set

Go to:
`VPC Console > DHCP Option Sets > Create DHCP Option Set`

![Screenshot 2025-07-01 at 13.27.32.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/067654efb858bc3fb04f6be4d278226d/assets/Screenshot%202025-07-01%20at%2013.27.32.png)

Set the following:

- **Name**: `infoblox-lab`
- **Domain name**: `infolab.com` (or as needed)
- **Domain name servers**: `10.10.10.3` *(replace with your Infoblox DNS IP)*
- *(Optional)* Configure NTP, NetBIOS if needed


> 💡 Tag the DHCP option set for easier identification:
> `Key: Name` → `Value: infoblox-lab`

Click **Create DHCP Option Set**.


---

### Step 2: Associate DHCP Option Set with Your VPC

Navigate to:
`VPC Console > Your VPCs`

1. Select your target VPC (e.g., `WebSvcsProdEu1`).
2. Click **Actions > Edit VPC Settings**.
3. In the **DHCP option set** dropdown, select `infoblox-lab`.
4. Click **Save**.

![Screenshot 2025-07-01 at 13.30.06.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/053f8952a3b2889d94b31dfbc994b2d6/assets/Screenshot%202025-07-01%20at%2013.30.06.png)

---

### ✅ Result

All new EC2 instances in the VPC will now automatically use the DNS server defined in your DHCP option set (e.g., your Infoblox resolver). This ensures DNS queries are visible and controlled by Infoblox Threat Defense.

---


## 4) Configuring Infoblox NIOS-X as Authoritative DNS for Azure Private Zone
===

In this section, we will extend our setup by integrating Infoblox NIOS-X-as-a-Service as the authoritative DNS for the infolab.com Azure Private DNS zone.

After syncing the zone from Azure using API-based synchronization, we will configure the authoritative DNS servers under Infoblox’s “Configure” section to explicitly designate NIOS-X as the DNS authority for the zone. This enables Infoblox to answer DNS queries for Azure workloads and any other clients resolving *.infolab.com.

> [!IMPORTANT]
> NOTE: Make sure that Azure Discovery Job is sync'd before proceeding.

![Screenshot 2025-07-03 at 12.19.08.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/4f07761984ec7a1b1566f7259923d41d/assets/Screenshot%202025-07-03%20at%2012.19.08.png)

You’ll also see how to:
	•	Identify synced zones under the DNS view
	•	Modify the zone properties to add authoritative DNS instances
	•	Validate resolution by running dig from an EC2 instance ( from AWS Region ) pointing to the NIOS-X DNS IP

This is key for hybrid or multicloud deployments where unified visibility, control, and logging over DNS is critical.

🛠 Prerequisites:

Zone (infolab.com) already created in Azure Private DNS

API sync already done, visible under DNS View AWS.private-1

NIOS-X service instance deployed


✅ Step-by-Step: Configure Infoblox as Authoritative DNS

1. Navigate to DNS Configuration

In the Infoblox I portal, go to **Configure → Networking → DNS → Zones**
Make sure to select the correct DNS View  and click on it.


![Screenshot 2025-07-03 at 12.02.01.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/3e9b376f1265734e32da2e56f54500f5/assets/Screenshot%202025-07-03%20at%2012.02.01.png)

2. Locate the Synced Zone

You should see  **infolab.com**  listed under the synced zones
Click the checkbox next to the zone, then hit Edit


![Screenshot 2025-07-03 at 12.04.46.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/d6e88db2845e7796854ab0582aafb4fc/assets/Screenshot%202025-07-03%20at%2012.04.46.png)

3. Add Authoritative DNS Server

Scroll down to Authoritative DNS Servers section
In the Service Instances list, select the Infoblox NIOS-X instance (e.g. TEST) with type NIOS-X (BloxOne) DDI
Click the arrow >> to move it to the selected list


![Screenshot 2025-07-03 at 12.05.43.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/2ca415e8ff4d1c51aa738eec013f0075/assets/Screenshot%202025-07-03%20at%2012.05.43.png)

![Screenshot 2025-07-03 at 12.05.57.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/316201e3b183cfb32e16f97338245589/assets/Screenshot%202025-07-03%20at%2012.05.57.png)

5. Save & Close
	•	Click Save and Close
	•	Zone will now reflect NIOS-X as the authoritative server



![Screenshot 2025-07-03 at 12.07.32.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/80aa2277761deeab265382c4bd83de2e/assets/Screenshot%202025-07-03%20at%2012.07.32.png)


🔍 Step 6: Validate DNS Resolution


### Step 1: Access the EC2 Instance

From the **Shell tab** in your lab, run the following:

```run
cd ~/infoblox-lab/secure-ai-infoblox/terraform
terraform output
```

This will show you the SSH command to access the EC2 instance - **WebServerProdEu1**

![Screenshot 2025-07-01 at 13.37.20.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/f72fb1c74b7fb04e90c2d623448bc434/assets/Screenshot%202025-07-01%20at%2013.37.20.png)


> [!IMPORTANT]
> NOTE:Please use your specific SSH command from the `terraform output` to access the instance.

```
ssh -i 'EU_WEST_WebProd1.pem' ec2-user@35.177.254.177
```

![Screenshot 2025-07-01 at 13.39.13.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/1356e48269998e5d3e923b8fccb1c51a/assets/Screenshot%202025-07-01%20at%2013.39.13.png)


### Step 2: Check DNS Settings

Once inside the EC2 host **WebServerProdEu1** , confirm it’s using the Infoblox DNS server:

```
cat /etc/resolv.conf
```

![Screenshot 2025-07-01 at 13.39.54.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/a9bdf0f65c4ad5b23244069f37c8394a/assets/Screenshot%202025-07-01%20at%2013.39.54.png)

> [!IMPORTANT]
> ✅ You should see: 10.10.10.3



If not reboot the instance and login again.

![Screenshot 2025-07-03 at 09.33.14.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/a009a9a8c182dea0dc94c41fbaa0d4bd/assets/Screenshot%202025-07-03%20at%2009.33.14.png)


From WebServerProdEu1 run the following command:

```run
dig azure-webprodeu2.infolab.com
```

Example output:

![Screenshot 2025-07-03 at 09.00.54.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/03c3cf9529e9993a6d2d0fa9e4c6f56e/assets/Screenshot%202025-07-03%20at%2009.00.54.png)


🎯 Success means:
	•	ANSWER SECTION is returned
	•	AUTHORITY SECTION shows your Infoblox record
	•	It resolves against @10.10.10.3 (your Infoblox DNS IP)


## Time for the Next Challenge

 Click **NEXT**!


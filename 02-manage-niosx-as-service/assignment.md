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

🎯 Goal

Automate the full setup of NIOS-X-as-a-Service as your authoritative DNS and security platform for cloud workloads — including VPN connectivity to Infoblox’s regional PoP and enabling DNS and route propagation.

You’re not clicking through GUIs — you’re treating infrastructure like code and DNS like a cloud-native service.

🛠 Actions

1️⃣ Launch and Validate NIOS-XaaS Instance Health

Run the health-check script to validate that your NIOS-XaaS instance is provisioned and operational:

```run
cd  /root/infoblox-lab/secure-ai-infoblox/scripts/
python3 infoblox_vpn_configure_final.py
```

![Screenshot 2025-08-08 at 21.14.36.png](https://play.instruqt.com/assets/tracks/qy6yppfre8ou/1c80fd67cac6b820947ff185e937887b/assets/Screenshot%202025-08-08%20at%2021.14.36.png)


> [!IMPORTANT]
> ⚠️ Important Before Proceeding

Before running Step 2, wait ~5 minutes for Service IPs for the Infoblox PoP to become available.

You can confirm this in the Infoblox Portal UI under:
Configure → Service Deployment → As-A-Service → Cloud → AWS → [Region] → SITE

![Screenshot 2025-07-22 at 11.24.37.png](https://play.instruqt.com/assets/tracks/prsxwy2uwmxg/e2384910ce5ad0ff6d550f7e2a845491/assets/Screenshot%202025-07-22%20at%2011.24.37.png)


Look for the Cloud Service IP, when those show up please proceed with the Step2.


2️⃣ Discover Required Cloud Service Details

```run
cd  /root/infoblox-lab/secure-ai-infoblox/scripts/
python3 get_cnames.py
```

![Screenshot 2025-07-22 at 11.09.22.png](https://play.instruqt.com/assets/tracks/prsxwy2uwmxg/cd08df0874f76aa3df4beeb8ab793b23/assets/Screenshot%202025-07-22%20at%2011.09.22.png)

📄 Fetches and saves Servcie IPs  (e.g., used later for VPN or DNS assignment).

3️⃣ Create the Cloud VPN to Infoblox PoP

```run
cd  /root/infoblox-lab/secure-ai-infoblox/scripts/
python3 create_aws_vpn.py
```

![Screenshot 2025-07-22 at 11.09.31.png](https://play.instruqt.com/assets/tracks/prsxwy2uwmxg/63a69133f4fe9b7a28c70765a6324f08/assets/Screenshot%202025-07-22%20at%2011.09.31.png)

🌍 Automatically creates two VPN tunnels to the Infoblox SaaS PoP using CGW+VGW configuration:
- Creates CGWs (Customer Gateways)
- Creates VPN Connections for both tunnels


4️⃣ Extract Tunnel IPs

```run
cd  /root/infoblox-lab/secure-ai-infoblox/scripts/
python3 extract_tunnels.py
```


![Screenshot 2025-07-22 at 11.09.40.png](https://play.instruqt.com/assets/tracks/prsxwy2uwmxg/8d14a8d8e3b464b05d4d0708b70a9c5c/assets/Screenshot%202025-07-22%20at%2011.09.40.png)

📥 Saves all IPs (Tunnel 1 & 2 per VPN) for later configuration with Infoblox UDDI or routing.


5️⃣ Update UDDI with the Tunnel IP

```run
cd  /root/infoblox-lab/secure-ai-infoblox/scripts/
python3 update_uddi_tunnel_final.py
```
![Screenshot 2025-07-22 at 11.09.48.png](https://play.instruqt.com/assets/tracks/prsxwy2uwmxg/e2b972eb690af7fdb6da14cac7ee6f15/assets/Screenshot%202025-07-22%20at%2011.09.48.png)


🚀 Updates the UDDI config to point to the correct Tunnel IPs


6️⃣ Enable Route Propagation in VPC

```run
cd  /root/infoblox-lab/secure-ai-infoblox/scripts/
python3 enable_propagation_vpc.py
```

![Screenshot 2025-07-22 at 11.09.54.png](https://play.instruqt.com/assets/tracks/prsxwy2uwmxg/1276598291149487b0d392c58d03b571/assets/Screenshot%202025-07-22%20at%2011.09.54.png)


📡 Ensures AWS route tables are updated with the VGW for traffic routing — essential for DNS and security inspection to flow via Infoblox.


✅ What You Now Have
- 🟢 A running NIOS-X-as-a-Service instance
- 🛡️ Connected securely to Infoblox’s regional PoP
- 🌐 Route propagation enabled for your VPC
- 📘 Ready to enable DNS, Security, Application Visibility, or DTC policies — all SaaS-native

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


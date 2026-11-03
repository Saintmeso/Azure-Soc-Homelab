# Azure-Soc-Homelab

Project Overview:
<img width="904" height="583" alt="Azure SOC Lab Layout" src="https://github.com/user-attachments/assets/5282192d-b06e-454a-a4a3-d48bdc556160" />


Thinking Breakdown:

Firstly I wanted to explain the entire point of this project, I wanted to build SOC on Azure with real attackers and logging via LAW (Log Analytics Workspaces) and Microsoft Sentinel as well as microsoft defender since many of the Sentinel tools are now on defender.

The purpose of this lab is to be able to capture real logs with a honeypot VM rather than simulated logs like atomic redteam and then build out a map via LAW workbooks that shows on a map where attacks are coming from based on event ID. (For the specific picture, I used event 4625)

Honestly this project really surprised me on how fast people attack you when your machine or network is vulnerable and discoverable.

Back to the project,

I first made a Resource group named RG-SOC-Lab.
Now I didn't take a screenshot of this because an RG is very straight foward, I like to think of it like a folder for all your resources for a project.

After that I created a Virtual network, within this virtual network I created a Windows 11 VM.
This windows vm acts as our victim machine, it is the one being breached (or trying to be breached)

<img width="1276" height="1163" alt="Azure SOC VM creation" src="https://github.com/user-attachments/assets/b1666277-e215-4a5b-a755-15605e72f09e" />


Now in azure, when you deploy a VM, it also deploys other resources required for that VM to work such as a NIC, NSG, Disk, and other Resources that make the VM function as it should.

What I did for the project was make it to where the NSG had an allow all rule.

The reasoning behind this is that I wanted everyone and everything to see that this VM is not only discoverable but vulnerable which makes it enticing to try and see what is inside, but in reality... its a honeypot.

<img width="1278" height="1180" alt="Azure SOC Inboud Security Rules (DANGER)" src="https://github.com/user-attachments/assets/6272c29c-e0f0-4ce6-9138-7274bf21d192" />

Now in an enterprise environment, this should NEVER be done, this is solely for testing because this vm is a honeypot and nothing else.

After applying the NSG rule and opening the NSG and effectively making it ineffective, I RDP'd into my Windows 11 VM to turn off the Windows Defnder Firewall Profiles,
To do this simply after RDP'ing into the VM, I typed wf.msc and it took to its firewall settings. I then when and turned off each profile to make this VM even more enticing to attack. (I didn't take a Screenshot of every single tab but each tab was disabled)

<img width="401" height="455" alt="Azure SOC wf msf OFF" src="https://github.com/user-attachments/assets/ad144679-466f-4f74-90a7-a8dfe99f3ba8" />

After turning off the Windows firewall, I wanted to ensure the VM was pingable from my local, to ensure that every firewall and security feature was disabled.

<img width="1113" height="431" alt="Azure SOC Ping VM" src="https://github.com/user-attachments/assets/c20fd330-d04b-4ee6-b5c0-5bfb8659cf8c" />

As you can see, we were getting a response from our VM, which means the NSG and WF (Windows Firewall) was fully disabled.

After pinging my VM and ensuring it can communicate with my pc, I then created the Log Analytics Workspace.

<img width="1274" height="765" alt="Azure Soc Log Analytics WS Creation" src="https://github.com/user-attachments/assets/1277989c-aa4c-4f0b-9b34-ac787fd9e48a" />

The LAW is used to collect logs from our vm, specifically windows logs.

After creating the LAW, I created a Microsoft Sentinel workspace and Installed the Windows Security Events to ingest security events from our VM using the windows agent into microsoft sentinel

<img width="1280" height="1250" alt="Azure SOC Microsoft Sentinel (Windows Security Events Install)" src="https://github.com/user-attachments/assets/9aafe41a-afcb-4811-8aef-ca9cfa872891" />

Now currently our Microsoft Sentinel and VM are not connected to each other so we have to create a Data Collection Rule from our Resourcegroup and to our VM. This is what connects our Azure Environments to our montioring and SIEM applications.


<img width="1276" height="1186" alt="Azure SOC WSE via AMA" src="https://github.com/user-attachments/assets/58ebcd1e-c045-4884-b3ed-f8327729164f" />

To verify that it was correctly connected, I went to the VM and went to the Extentions and the connection was there as it was supposed to be.

<img width="1278" height="915" alt="Azure SOC VM Extentions verification" src="https://github.com/user-attachments/assets/7373afcf-7347-473f-9e63-22b295d7f032" />


Now this is the actual exciting part, after waiting around 30 mins (give or take a bit more), My VM started to get login attempts from all sorts of IP's.

<img width="1268" height="1200" alt="Azure SOC LAW Logs (Attacked more)" src="https://github.com/user-attachments/assets/678db621-e2dd-4a74-90c3-20268a29a518" />

Now in these logs, they do contain their IP Address to know where we are getting attacked from but I wanted to take it a step further and have a Workflow via Microsoft defender to fully see on a map exactly where I'm being attacked from.

What I first did was make a watchlist named "geoip" based on IP Addresses via Microsoft defender. I imported a CSV file that contained relative IP Addresses from specific locations around the world where ISP's have their IP's based in.

I did this to build the basis on where the attacks are coming in the world and to correctly show up on the map.

<img width="1268" height="1276" alt="Azure SOC lab GeoIP MC Def Succeeded" src="https://github.com/user-attachments/assets/4d6e0319-58b8-4abc-a279-fb214ebce2ac" />

Now, going back to Azure LAW (Log Analyics Workspace), When I use the KQL query 

"SecurityEvent

_GetWatchlist("geoip")"

I am able to get a log query where it shows the country in which these attacks are coming from.

<img width="2543" height="1248" alt="Azure SOC Location GeoIP" src="https://github.com/user-attachments/assets/7ab2201b-a101-445b-8be4-7c5345960497" />

I think that facinating part about this is what when I was first being attacked, It was from South Korea, and the after a few minutes it was India, then Russia and then all over the world.

This just truly went to show that the internet is not a safe place without security measures in place to protect your machine and network.

After this, I wanted to create the Map in which it will show the location of attacked based on the specific eventID.

For this, I used a JSON template that took information such as the Longitude, Latiude, Failure Count, and a heat map to create and fill a Map with alerts based on the attacks and the country they were coming from based off of eventID.

<img width="878" height="1211" alt="Azure SOC Lab Map creation JSON code" src="https://github.com/user-attachments/assets/41f1c38a-42db-4fe8-a48f-52cd5062c260" />

I had the map query eventID 4625 which is failed Login Attempts within Windows.


<img width="862" height="427" alt="Azure SOC Lab map Done  event 4625" src="https://github.com/user-attachments/assets/bf710a9c-ab60-4397-bc7b-71a9ddc2fe26" />

As you can see, most of these attacks with the EventID 4625 came from Brisbane, Australia and Newport, United States.

If I had used another EventID, I would've gotten results from different countrys but for the sake of documentation, I only covered one EventID.



















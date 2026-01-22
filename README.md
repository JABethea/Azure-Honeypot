<h1>Microsoft Azure Honeypot Home Lab</h1>



<h2>Description</h2>
In this lab, I have created a honeypot in Azure.
I launched a virtual machine and connected it to the public internet, so that it would get attacked quickly.  

I configured log forwarding to forward the logs and failed attack attempts into a centralized repository.  

I then connected the centralized repository to a SIEM and then created an attack map that displays where all of the attackers are coming from.  

My objective with this lab is to not only showcase skills in Azure and Microsoft Sentinel, but also to demonstrate just how quickly an insecure machine is attacked and at what volume.  



I created a free Azure subscription and logged in to my Azure account. I then spun up an Azure Virtual Machine and created new Windows 10 Virtual Machines and create log in credentials. 
<img width="975" height="724" alt="image" src="https://github.com/user-attachments/assets/91dec9df-1d05-4548-b201-bfb78ecb2b12" />
<img width="975" height="306" alt="image" src="https://github.com/user-attachments/assets/1ea5cdb1-e1ca-48fb-942e-1c1cbcc27914" />


To begin the honeypot creation process, I first went to the Network Security Group and created a rule to allow all inbound traffic. (Start > wf.msc > properties > all off)

<img width="975" height="718" alt="image" src="https://github.com/user-attachments/assets/c8f128e5-e28d-4f0b-afe8-2e867df9edc4" />
<img width="975" height="770" alt="image" src="https://github.com/user-attachments/assets/f8d94869-d554-4294-a890-9fa99ed04a7a" />
<img width="868" height="872" alt="image" src="https://github.com/user-attachments/assets/89da0e59-6a71-40e2-9280-fbdebe3b373f" />

Next, complete the VM creation and then confirm the VM is connected to the VN 

<img width="975" height="505" alt="image" src="https://github.com/user-attachments/assets/bafba57c-18a3-4437-b907-2f8832baf5fc" />
<img width="974" height="856" alt="image" src="https://github.com/user-attachments/assets/555ed090-1e27-4801-ae13-1813906c5aaf" />
<img width="974" height="625" alt="image" src="https://github.com/user-attachments/assets/1a8f0a8c-3727-4602-b6fb-598a9498823c" />

Once everything is created, confirm the security rules configured for the VM and VN, ensure that the rules that have been created are extremely insecure and label them appropriately. Ignore the warnings. 

<img width="975" height="825" alt="image" src="https://github.com/user-attachments/assets/01ac3a07-3124-4c1d-a196-f03bc7c0f1ef" />
<img width="2481" height="1047" alt="image" src="https://github.com/user-attachments/assets/967662b3-6cf0-4466-be1c-b86e95511c5f" />
<img width="871" height="1350" alt="image" src="https://github.com/user-attachments/assets/90376214-a91e-4df2-838c-657f8ceddd1b" />


Spin up the VAM and then Fail 3 logins as an employee or some other username
As confirmation, login to the VM and open up Event Viewer and inspect the security logs, confirming the 3 failed in logins as employee with the event ID XXXX.
<img width="975" height="489" alt="image" src="https://github.com/user-attachments/assets/27d029f8-b420-4fe6-9c47-fd1eccbedfca" />
<img width="975" height="725" alt="image" src="https://github.com/user-attachments/assets/3b6297e9-1ec6-4c3a-b630-62581cbd01c9" />
<img width="975" height="485" alt="image" src="https://github.com/user-attachments/assets/f03c65c4-4021-4a57-ab26-92ddf3bf4937" />
<img width="975" height="485" alt="image" src="https://github.com/user-attachments/assets/c83fd294-a981-49ee-aa13-e966512ad802" />
<img width="975" height="545" alt="image" src="https://github.com/user-attachments/assets/bd5c81bb-5f5e-4fae-8d1d-b824cb63acba" />


The next step was creating a central log repo called a LAW (Log Analytics Workspace). 
I then launched a Sentinel Instance and connected it to Log Analytics. 
<img width="974" height="254" alt="image" src="https://github.com/user-attachments/assets/b206285c-7b76-44cb-836a-4725d12b163e" />
<img width="975" height="185" alt="image" src="https://github.com/user-attachments/assets/1d80b1d8-6a3f-458a-a971-a0159ffed110" />
<img width="975" height="446" alt="image" src="https://github.com/user-attachments/assets/bed51275-b5bb-440e-aec0-b3d3ede1b9a6" />

Then configure the Windows Security Events via AMA connector and then create the DCR within sentinel and watch for extension creation. 

<img width="975" height="717" alt="image" src="https://github.com/user-attachments/assets/35470377-af33-450a-9bcc-83eb034f2a82" />
<img width="975" height="432" alt="image" src="https://github.com/user-attachments/assets/8efc0fcb-c3f4-469c-bc14-e55f5969ea96" />


After that, I queried the logs within the LAW and the SIEM, sentinel directory. Look at all of these failed logins!
<img width="975" height="517" alt="image" src="https://github.com/user-attachments/assets/876a349b-d890-4c81-a529-d269a0363544" />
Let’s narrow this down to the first Account name we see or project some of the more relevant fields to narrow the scope of data. 
<img width="975" height="411" alt="image" src="https://github.com/user-attachments/assets/7d24d28c-8bb6-4cb0-9d2a-d4f46c05fe61" />

Observe some of the VM logs:

SecurityEvent
| where EventId == 4625

There is no location data, only IP addresses, which we can then use to derive locational data. To do so, I imported a spreadsheet “Sentinel Watchlist”) which contains geographic info for each block of IP addresses 

Download: geoip-summarized.csv https://raw.githubusercontent.com/joshmadakor1/lognpacific-public/refs/heads/main/misc/geoip-summarized.csv 

Now I can look the geolocation up for one of the IP addresses that this account is trying to log in to the Honeypot VM from and see that it is in the Netherlands. Although that is an interestingly non-Dutch ISP and Org name.
<img width="975" height="942" alt="image" src="https://github.com/user-attachments/assets/7cdbf27d-627e-4c35-8858-461506ea44cf" />

Look at all of these failed login attempts!
<img width="975" height="546" alt="image" src="https://github.com/user-attachments/assets/1cc6bf4d-0572-4677-b99d-bd87a14314ca" />

In less than a minute there are hundreds of failed log in attempts to this honey pot
<img width="975" height="601" alt="image" src="https://github.com/user-attachments/assets/20d72244-694b-423b-9b4d-b04e668af45b" />


Within Sentinel, create the watchlist:

Name/Alias: geoip
Source type: Local File
Number of lines before row: 0
Search Key: network
Once the watchlist is fully imported, it should have around 54,000 rows.
<img width="975" height="546" alt="image" src="https://github.com/user-attachments/assets/25fbf9b9-96a1-4e7b-8b98-5ccc599e0db7" />
In less than a minute there are hundreds of failed log in attempts to this honey pot
<img width="975" height="601" alt="image" src="https://github.com/user-attachments/assets/6b91a0bc-d35b-4306-8704-d8efeca593ad" />


Now that the csv file that groups IP addresses by geolocation, I pull that from sentinel into the LAW and KQL query it to get a result set of all of the different attackers that are attacking the honeypot and where they’re attacking from
<img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/52f05845-b691-4df2-8b88-ac6092de0499" />

<img width="975" height="488" alt="image" src="https://github.com/user-attachments/assets/dc124e36-b540-4253-81c9-25f34298b352" />

Location data in a real world scenario would come from a live source or updated automatically on the back end by the service provider.

Now that the logs have geographic information, I can now see where the attacks are coming from:
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where IpAddress == <attacker IP address>
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents

Through this more advanced KQL query, I can see the location data for the same random attacker that I chose earlier, with additional information like their country and city name. 
<img width="975" height="443" alt="image" src="https://github.com/user-attachments/assets/ce844372-f49c-4708-a2ce-e26e7d36d895" />



Now I am going to create a new workbook, Azure moved this from sentinel to defender portal, create a new workbook and delete all of the pre-populated fields in the workbook, then I am going to import a JSON file for the mapping and add a query and generate the map of data 
<img width="975" height="608" alt="image" src="https://github.com/user-attachments/assets/a968d5ec-f800-4988-b411-55ad8df63308" />

So the attack map is showing that there are many attacks originating in Europe, especailly in places like the Netherlands and Bulgaria. But remember from the specific example that I took earlier of once of the IPs, the origin of the attack IP is not necessarily the original source of the attack!
 

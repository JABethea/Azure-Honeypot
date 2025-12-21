<h1>Microsoft Azure Honeypot Home Lab</h1>



<h2>Description</h2>
In this lab, I have created a honeypot in Azure.
I launched a virtual machine and connected it to the public internet, so that it would get attacked quickly. 
I configured log forwarding to forward the logs and failed attack attempts into a centralized repository. 
I then connected the centralized repository to a SIEM and then created an attack map that displays where all of the attackers are coming from. 


I created a free Azure subscription and logged in to my Azure account. I then spun up an Azure Virtual Machine and created new Windows 10 Virtual Machines and create log in credentials. 


To begin the honeypot creation process, I first went to the Network Security Group and created a rule to allow all inbound traffic. (Start > wf.msc > properties > all off)

Fail 3 logins as an employee or some other username
As confirmation, login to the VM and open up Event Viewer and inspect the security logs, confirming the 3 failed in logins as employee with the event ID XXXX.

The next step was creating a central log repo called a LAW (Log Analytics Workspace). 
I then launched a Sentinel Instance and connected it to Log Analytics. 

Then configure the Windows Security Evens via AMA connector and then create the DCR within sentinel and watch for extension creation. 

After that, I queried the logs within the LAW and the SIEM, sentinel directory. 


Observe some of your VM logs:

SecurityEvent
| where EventId == 4625

There is no location data, only IP addresses, which we can then use to derive locational data. To do so, I imported a spreadsheet “Sentinel Watchlist”) which contains geographic info for each block of IP addresses 

Download: geoip-summarized.csv https://raw.githubusercontent.com/joshmadakor1/lognpacific-public/refs/heads/main/misc/geoip-summarized.csv 

Within Sentinel, create the watchlist:

Name/Alias: geoip
Source type: Local File
Number of lines before row: 0
Search Key: network
Once the watchlist is fully imported, it should have around 54,000 rows.
Location data in a real world scenario would come from a live source or updated automatically on the back end by the service provider.

Now that the logs have geographic information, I can now see where the attacks are coming from:
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where IpAddress == <attacker IP address>
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents

Now within Sentinel I create a new Workbook, delete the preuploaded elements and add a “Query” element. I then go to the editor tab and add in a map JSON file for the finishing touches on the attack map. 
 

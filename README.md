# SEIM Project With World Map Visualization

## SEIM Project
A Security Information and Event Management (SEIM) solution built on Microsoft Azure to enhance security monitoring and visualize threat origins through geolocation mapping.

## Overview
The SEIM project leverages Azure services to monitor, analyze, and visualize security events. It provides insights into potential threats by mapping attack origins on a global scale, making it ideal for cybersecurity enthusiasts and professionals.

## Key Features

1. **Azure Infrastructure**: Provisions a Virtual Machine (VM) and Resource Group in Azure to support attack simulation and analysis.

2. **Log Analytics Workspace**: Centralizes log storage and ingestion for security event data.

3. **Custom Logs**: Captures geolocation data of attack origins within the VM.

4. **SEIM Visualization**: Integrates with Log Analytics to display attack origins on a world map.

5. **Microsoft Sentinel**: Enhances analytics with cloud-native SIEM capabilities, including threat intelligence and advanced visualization.

## Test Scenario and Geolocation Mapping

As part of the testing process, a specific test case was conducted to evaluate the SEIM solution's functionality. By logging into the VM using the public IP address and intentionally entering an incorrect password, the project simulated an unauthorized login attempt. This action triggers the creation of an audit failure log, which captures the IP address of the system attempting to access the VM.
<p align="center">

<img src="https://imgur.com/qqcC8cz.jpg" width="650" height="350">

</p>

To track the location of the attacking system, I created a Log Analytics Workspace to log the events. From there i created a Mircosoft Sentinel Instance. Configuered a “Windows Security Events via AMA” connector, and created the DCR within Sentinel.

Observe some of your VM logs:

SecurityEvent
| where EventId == 4625
<p align="center">

<img src="https://imgur.com/AMQiDV0.jpg" width="650" height="350">

</p>

Observing the SecurityEvent logs in the Log Analytics Workspace. At this point there is no location data, only IP address, which we can use to derive the location data.  After some research I imported a spreadsheet (as a “Sentinel Watchlist”) which contains geographic information for each block of IP addresses. I downloaded a file called "geoip-summarized.csv". Within Sentinel, I created the watchlist adding 55,000 locations to match the IP address to.

[**geoip-summarized.csv**](https://drive.google.com/file/d/13EfjM_4BohrmaxqXZLB5VUBIz2sv9Siz/view?usp=sharing)
<p align="center">

<img src="https://imgur.com/eU9JcRc.jpg" width="650" height="350">

</p>

Observe the logs now have geographic information, so you can see where the attacks are coming from.  We can now retrieve latitude, longitude, and general area information associated with the IP address. This geolocation data is then used to plot the attack origins on a world map, providing a visual representation of the security events.

let GeoIPDB_FULL = _GetWatchlist("geoip");

let WindowsEvents = SecurityEvent

    | where IpAddress == <attacker IP address>
    
    | where EventID == 4625
    
    | order by TimeGenerated desc
    
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
    
WindowsEvents

### Firewall Configuration and Accessibility
During the testing phase, the VM's firewall was temporarily disabled to ensure easy accessibility for testing purposes. It is important to note that this configuration is not recommended for production environments, and proper security measures should be implemented.

<p align="center">

<img src="https://imgur.com/WaYTQsX.jpg" width="650" height="350">

</p>

### Visualization and Analysis
To visualize the gathered data, a custom log was created in Azure to store information about all failed login attempts to the system. The raw data from the log was extracted and processed to separate the relevant information into individual columns, including latitude and longitude. The Microsoft Sentinel workbook was utilized to present the geolocation data on a world map, providing an intuitive and comprehensive overview of the attacks.

<p align="center">

<img src="https://imgur.com/uLkkEjo.jpg" width="650" height="300">

</p>

<p align="center">

<img src="https://imgur.com/9tZxcqn.jpg" width="650" height="300">

</p>


### SEIM World Map Visualization

The SEIM project is an ongoing endeavor, continuously collecting and analyzing security event data. The information presented in the screenshot provides an initial update.
![World Map with Attacker Locations](https://github.com/meghabyte-og/SEIM/assets/135510418/347184fd-4a0e-49a1-9762-05cdcaae1f76)

 <sub>Note: The SEIM project serves educational and testing purposes and should be adapted and deployed with appropriate security considerations for production environments.</sub>

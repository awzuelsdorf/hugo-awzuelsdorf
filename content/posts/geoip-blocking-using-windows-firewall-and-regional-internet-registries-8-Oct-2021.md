---
title: "Geoip Blocking Using Windows Firewall and Regional Internet Registries"
date: 2021-10-08T00:00:00-04:00
draft: true
---

***Overview***

This article will describe using data provided by the Regional Internet Registries (RIRs) APNIC and RIPE NCC to block IP addresses located in Russia, China, Iran, and North Korea. The method described here can be used to block any country whose IP address space is managed by APNIC or RIPE NCC, not just Russia, China, Iran, or North Korea.
			
***Motivation: Why Block a Whole Country?***

Blocking a whole country's IP address space can be useful when one has no legitimate reason to visit any website or access any resource within that country, either now or in the near future. For example, many Americans don't have any reason to go to a Russian, Chinese, Iranian, or North Korean website or otherwise access a computing resource in those countries. However, given that many unsophisticated but effective phishing schemes use servers located in another country to host fake login pages to steal passwords, allowing applications on your computer to make connections to computing resources within those countries can be unnecessary risk for no reward, especially in organizations with many users with differing technical skill levels. Therefore, it makes sense in many cases to block outbound connections (connections from your computer to another computing resource) and even inbound connections to a given country.
			
***Getting the Data***

The first step is to determine which IP address ranges correspond to which countries. There is no one place to find this data since there are different RIRs for different countries. RIPE NCC, the RIR for Europe, West Asia, Central Asia, and parts of Russia, can be used to get data for Iran and some of Russia. APNIC, the RIR for the Asia-Pacific region, can be used to get data for North Korea, China, and the areas of Russia that RIPE NCC does not cover. APNIC and RIPE NCC provide the gzip-compressed text files [apnic.db.inetnum.gz](https://ftp.apnic.net/apnic/whois/apnic.db.inetnum.gz) and [ripe.db.inetnum.gz](https://ftp.ripe.net/ripe/dbase/split/ripe.db.inetnum.gz), respectively, that describe which IP address ranges correspond to which countries and organizations within those RIRs. For example at publish time, the range 202.14.146.0 through 202.14.146.255 has been allocated to Jack Strangio Software in Australia according to apnic.db.inetnum.gz and ripe.db.inetnum.gz indicates that RPN Holding in France has been allocated 194.206.161.47 (in this case, the range is just a single IP address).
			
***Transforming the Data***

The data are stored in a text format that is not particularly machine-friendly. Instead of working with these files as-is, one can use [inetnum_file_parser.py](https://github.com/awzuelsdorf/geoip_windows_firewall/blob/main/inetnum_file_parser.py), which will output a CSV file, a SQLite database, or both from a copy of these files (the files have to be decompressed before using them with the script). This will also convert the IP ranges of these files into IP CIDRs under the column "ip_cidr" for use in adding firewall rules.
			
***Consolidating IP CIDRs***

Once the files from before have been decompressed and converted to a CSV file using [inetnum_file_parser.py](https://github.com/awzuelsdorf/geoip_windows_firewall/blob/main/inetnum_file_parser.py), one could add each IP range that belongs to the country codes CN (China), IR (Iran), RU (Russia), and KP (North Korea) to an outbound rule and an inbound rule in Windows Firewall. However, there are hundreds of thousands of IP ranges for these countries individually, and firewall rules are not free. Therefore, it makes sense to combine overlapping IP address ranges where possible to limit the number of rules needed. To do this, one can use [consolidate_ips.py](https://github.com/awzuelsdorf/geoip_windows_firewall/blob/main/consolidate_ips.py), which will produce a minimal set of IP CIDRs given a list of country codes. Running this code using a relatively download of the inetnum files from before and the country codes CN, KP, IR, and RU should yield a file that reduces the IP ranges from 678818 to 22804, a reduction of about 96 percent.
			
***Applying firewall rules***

Once IP ranges have been consolidated, one can use the [Import-FirewallBlocklist.ps1](https://github.com/awzuelsdorf/geoip_windows_firewall/blob/main/Import-FirewallBlocklist.ps1) Powershell script, courtesy of [Jason Fossen of Enclave Consulting LLC](http://www.sans.org/sec505), to create and update inbound and oubound firewall rules using the consolidated IP CIDR file (Example: `.\Import-FirewallBlocklist.ps1 -inputfile .\consolidated_china_iran_russia_north_korea.txt`). It may be necessary to use a Powershell terminal with administrator privileges and update the execution policy at least temporarily to Bypass using `Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope CurrentUser` in order to apply the rules.
			
Once this is done, attempting to visit a website or other resource in China (such as weibo.com), Russia, Iran, or North Korea should fail, reducing one's attack surface significantly.

***Recommendations, Caveats, and Future Work***

While limiting outbound and inbound connections from certain countries makes sense for many individuals, it may not make sense for some businesses or some divisions of some businesses. So before applying these rules to others' computing resources, be sure to get written permission.

If possible, one should use an alerting solution to ensure that requests for resources in blocked countries can be detected and investigated proactively, as it could be that a malicious program was installed, monitored the computer's usage, and later attempted to make a connection to a resource in one of the blocked countries, presumably to send back sensitive data it has collected while observing the computer's usage.
			
It could also be that there is a new but legitimate reason to reach out to these countries (for example, winning a new contract with a company). In this case, it is better to address the situation proactively than to wait for others to complain, which could trigger an overreaction by authorities within the organization that results in removing all of the outbound or inbound rules to the blocked countries (this could ultimately do more damage via future compromise than the current inconvenience of a blocked connection, but making that case is easier or even unnecessary if one is proactive in fixing the issue). The SQLite file generated using inetnum_file_parser.py, when loaded into a database viewer such as DB Browser, can be useful for determining which ranges to permit and without throwing away all rules.

A caveat of using this or any geographic IP blocking solution is that a sophisticated adversary will often proxy his/her attacks through resources in an unblocked country. This consideration is one of the main arguments against using geographic IP blocking. However, there is an almost farcical number of sophisticated organizations that have been compromised through unsophisticated attacks to devastating effect on those organizations' credibility and, often, profitability. Many of these could have been prevented using geographic IP blocking, which should be reason enough to at least consider using geographic IP blocking where possible.
			
***Future work***

It would be interesting to expand the consolidate_ips.py script to filter by organization in addition to country. It would also be very useful to modify consolidate_ips.py to process the three other RIRs' analogues to ripe.db.inetnum.gz and apnic.db.inetnum.gz, as this would provide the ability to block connections in any country.

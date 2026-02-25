---
layout: default
title:  Networking Analysis
---
# CASE_STUDY: Malware Traffic Analysis - LUMMA IN THE ROOM-AH 

Tools used: Wireshark

Right away looking deeper into the POST request, the hypertext transfer protocol reveals the attacker host name is: whitepepper.su.

Following the identification of the whitepepper.su C2 domain, an investigation was launched to determine the initial ingress vector and the timeline of the network infiltration.

## Initial Analysis 
Initial protocol-based filtering for common artifact extensions (.exe, .zip, .js) yielded null results. Further inspection of the session handshake confirmed the use of TLS 1.3 cryptographic encapsulation, indicating the payload was delivered via an encrypted tunnel to bypass signature-based detection.

This necessitated a pivot to traffic flow analysis to verify active communication between the external infrastructure and the internal host. Subsequent inspection confirmed a successful payload delivery event.

While on the hunt digging for more information, I found that the payload was indeed encrypted using the TLSv1.3 protocol which is one of the most powerful encryption methods. 

This led me to my next check which was analyzing the DNS protocol. Before even touching the malware, the victim's PC needs to know which server it is communicating with in order to download the malicious program. 

Using the “dns.flags.response == 0 && ip.src == 10.1.21.58 && frame.time_relative > 60 && frame.time_relative < 95” filter allowed me to go on a deep analysis looking for any suspicious sites the victim may have gone on before the DNS came into contact with the attacker host whitepepper.su. 

![DNS Query Ingress Analysis](../../assets/images/dns-query-ingress-analysis.png)
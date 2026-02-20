# Auth  

We're told that a security team intercepted network traffic from a company's "secure" file server. We get a .pcap file (basically a recording of network traffic) and access to a live server to exploit. I think this challenge gives a little exposure to a beginner like me on how verification works behind the scenes.  

## Approach  

Diving into the packets, I tried to first understand how the HTTP traffic worked. From the packets, I got to understand how the authentication progressed for each request.  


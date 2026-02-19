# Rainbow Bridge

Participants were provided with an excel file of wifi log, consisting of timestamps, SSID and RSSI (dBm). The question basically wants us to locate where this one person is currently at based on a record of his previous locations (SSID) and his wifi signal's strength (RSSI). Since this is a new form of OSINT challenge for me, it was honestly fun to do!

## Approach
![Rainbow Bridge Wifi Log.png](https://github.com/c4ramel0dyssey/CTF-Writeups/blob/main/EschatonCTF2026/OSINT/Rainbow%20Bridge%20Wifi%20Log.png)

I didn't have any idea about RSSI at first. I looked it up and found out that -30 dBm is a very strong signal, meanwhile -70 dBm or below indicates a weak signal.  
  
Since the question asks to locate the current position of our main character, I just went straight to reviewing the last two locations on the wifi log. Logically, it must be around that area right? Also, considering the RSSI information, the wifi signal potrayed in those two areas seem pretty strong, meaning that the character were closed to the wifi source.  






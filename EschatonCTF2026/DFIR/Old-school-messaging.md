# Old School Messaging

> Back in my day, we didn't have fancy encrypted messengers. We had dial-up modems and the sweet sound of a 56k handshake. Find the flag to be worthy of the BBS.

Based on the challenge, there are some hints that we could extract (or at least assume) to begin our approach. First, the fact that they mention non-encrypted message could be a direct nod to SMTP protocol. Also, I would like to also consider "BBS" as a keyword that we can use in filtering to find related messages.

## Approach  

![OSM1.png](https://github.com/c4ramel0dyssey/CTF-Writeups/blob/main/EschatonCTF2026/DFIR/OSM1.png)

Here, we find a packet that fullfil the two hints. Right click at the packet and I followed the TCP stream to see the contents of it.  

```
From: SysOp Dave <sysadmin@retro-bbs.net>
To: New User <user42@dial-up.com>
Subject: RE: BBS Account Verification - Action Required

Hello fellow Netizen!

Thank you for registering at Retro-BBS! We're excited to have you join our
community of dial-up enthusiasts and message board aficionados.

To complete your account verification, please scan the attached QR code...

```

The email found inside the packet really is suspicious. I was like, QR code?? That must be some kind of lead to the flag. Scrolling further down, the email had a base64-encoded PNG file attached called `verify_account.png`. I copied that part and pasted it into a base64 png decoder online but nothing appeared. Looked like the file is corrupted.  

Wait, the email also mentions:

> P.S. If the image doesn't open, your mail client might have corrupted it during transmission. Those darn 8-bit clean issues!

This is a huge hint that the image might be intentionally broken!  

Idea: I tried to encode some pngs into base64 to see what they would actually look like. What I found was interesting:  

iVBORw0KGgoAAAANSUhEUgAABf4AAAMWCAYAAACgPpdhAAAAAX...
iVBORw0KGgoAAAANSUhEUgAAB4AAAAQ4CAYAAADo08FDAAAAAXNSR0I...
iVBORw0KGgoAAAANSUhEUgAAB4AAAAQ4CAYAAADo08FDAAAAAXNSR0...
iVBORw0KGgoAAAANSUhEUgAAAYIAAAC7CAYAAABo+JlYAAAAAXNSRr...  

From the following, I notice that all pngs have signature bits in the beginning of their base64 code. However, if we look at the base64 code provided for our QR code: AFBORw0KGgoAA...  

It is very clear that the few early bits are messed with on purpose. I simply changed AFBOR -> iVBOR and tried to decode it once again. This time, a QR code was generated from the base64 code!    

I scanned the QR code and got the flag: 

esch{c0mmunicat1on_d3c0d3d_th3_0ld_way}





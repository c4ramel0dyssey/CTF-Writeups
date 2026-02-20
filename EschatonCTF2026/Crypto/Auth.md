# Auth  

We're told that a security team intercepted network traffic from a company's "secure" file server. We get a .pcap file (basically a recording of network traffic) and access to a live server to exploit. I think this challenge gives a little exposure to a beginner like me on how verification works behind the scenes.  

## Approach  

Diving into the packets, I tried to first understand how the HTTP traffic worked. From the packets, I got to understand how the authentication progressed for each request.  

![Auth.png](https://github.com/c4ramel0dyssey/CTF-Writeups/blob/main/EschatonCTF2026/Crypto/Auth.png)  

We can see that for each  
```
GET /challenge
```

the response would be  
```
"{"type": "challenge", "challenge": ".....", "nonce": ..}
```

and the for the next part: verification:  
```
> POST /verify  
{"response": "some hash",  
 "nonce": previous nonce + 1,  
 "challenge": "similar to the previous challenge"}
```
The thing with this type of authentication is that it usually requires a secret key. Finding the secret key is not that hard. It took me a couple scroll down the stream to find the secret key unencrypted in the DNS record.  

```
TXT: v=auth1 key=k3yM4st3r_S3cr3t!
```

So basically:  
1. Server gives you a random challenge and a nonce
2. We need to compute some hash using the secret key and send it back
3. The serves checks wether it is correct. If yes, we pass the authentication

Now, it's time to figure out how to get the correct hash. The problem is, we don't know the correct combination. Some combinations that I've tried include:  
- challenge + nonce + key
- key + challenge + nonce
- nonce + challenge + key

I tried using the information obtained from the photo attached, and compare hashes from each attempt to see if it matches the one in the photo.  

After some try and error, the winner was:

```
SHA256(key + challenge + nonce)
```



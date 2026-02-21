# Now You See Me  

This is the first ever web challenge that I got to solve lol. Cause' turned out the flag was hidden in plain sight, and we just need to decode the invisible part which was written in Unicode characters to reveal the flag. I didn't solve it during the ctf tho. It took me studying other people writeups to be able to solve them.

## Approach  

The reasoan why I didn't manage to solve this challenge at first was because of my limited knowledge in web challenges. I first tried the basic attempts when it comes to web challenges such as right clicking and CTRL + U to view source but all were blocked.  

I looked into the Javascript code (index.js) under the network inspection section, and it was nothing unsual at first. Just bunch of codes describing the creepy eyeballs animation on the web page. But then, I stumbled upon this one part:  

```
new Proxy(
  {},
  {
    get: (_, n) =>
      eval(
        [...n].map((n) => +("ﾠ" > n)).join``.replace(/.{8}/g, (n) =>
          String.fromCharCode(+("0b" + n)),
        ),
      ),
  },
)
  .ﾠ
```
  
Nothing suspicious right? But wait till you tried to copy them and see the wordcount.  

  
![Nowuseeme.png](https://github.com/c4ramel0dyssey/CTF-Writeups/blob/main/EschatonCTF2026/Web/Nowuseeme.png)  

There are actually thousands of hidden characters!  

## Understanding the obfuscation  

```
get: (_, n) => eval(
    [...n]                              // Name passed to get handler as n
    .map((n) => +("ﾠ" > n))             // Compare each to ﾠ: true=1, false=0
    .join``                             // Join into binary string
    .replace(/.{8}/g, (n) =>            // Binary grouped into 8-bit chunks
        String.fromCharCode(+("0b" + n)) // Convert binary to ASCII
    )
)
```

So basically: Unicode characters -> Binary -> ASCII -> Javascript  

Since the payload executes only once during the page load, a good option is to decode the Unicode offline. For this, we need a script that can:  

1. Extract invisible characters
2. Converts Unicode comparisons into binary
3. Converts binary into ASCII
4. Read the resulting Javascript output

After a few failed attempts (+ some helps from other people's writeups and ChatGPT), this is my final script:  

```
with open('index.js', 'r', encoding='utf-8') as f:
    content = f.read()

# Invisible Unicode characters
halfwidth = '\uffa0'  # ﾠ → represents 0
hangul = '\u3164'     # ㅤ → represents 1

# Step 1: Extract invisible characters
invisible = ''
for c in content:
    if c == halfwidth or c == hangul:
        invisible += c

# Step 2: Convert invisible characters to binary
binary = ''
for c in invisible:
    if c == hangul:
        binary += '1'
    else:
        binary += '0'

# Step 3: Fix bit misalignment (drop leading sync bit)
binary = binary[1:]

# Step 4: Convert binary to ASCII
result = ''
for i in range(0, len(binary), 8):
    byte = binary[i:i+8]
    if len(byte) == 8:
        result += chr(int(byte, 2))

# Step 5: Print the decoded message / flag
print(result)
```

And we got our flag: esch{y0u_s33_,_but_u_d0_n0t_0bs3rv3}

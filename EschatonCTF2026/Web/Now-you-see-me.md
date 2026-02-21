# Now You See Me  

This is the first ever web challenge that I got to solve lol. Cause' turned out the flag was hidden in plain sight, and we just need to decode the invisible part which was written in Unicode characters to reveal the flag. I didn't solve it during the ctf tho. It took me studying other people writeups to be able to solve them.

## Approach  

The reasoan why I didn't manage to solve this challenge at first was because of my limited knowledge in web challenges. I first tried the basic attempts when it comes to web challenges such as right clicking and CTRL + U to view source but all were blocked.  

I looked into the Javascript code and it was nothing unsual at first. Just bunch of codes describing the creepy eyeballs animation on the web page. But then, I stumbled upon this one part:  

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

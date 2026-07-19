## 1. Invoice Without a Bank

A zipped folder was given with a lottt of ```.eml``` files.  

The flag consist of information about the following:
- The exact PDF attachment filename
- The identifier from the subject after Fatura Emitida

This challenge can be solved easily using linux command when investigating the give files.  
Instead of analyzing all files one by one, I decided to find a file containing the name "Fatura Emitida" using the following command:  

```
grep -H "Fatura Emitida" *.eml
```

It then output the name of the file, as well as the line containing the name. This is already a good start since only one file contains the name, so I just focus on the file for further investigation.  
Oh and also, the line being printed earlier also contained the identifier as per mentioned in the second instruction. So we got one important detail already.  

In this case, the file responsible for the flag was ```sample-717.eml```. So i use the command ```cat sample-717.eml``` to see the contents of the file.  
From there, the pdf attachment can be found.  

**Flag:  grodno{Vl6s3kCIKaUvwaUAeY.pdf_6ZFYeMmltso}**  

## 2. USB  

An arhive with USB acquisition artifacts and a short description of the experiment being done was provided.  
This challenge is quite straightforward and all that needed to be done was to recover the MD5 hashes of the following two Windows FTK Imager acquisitions:  

1. Flash-firstrun.001
2. Flash-secondrun.001

FTK Imager normally craetes an image plus metada files. In the Flash-firstrun directory:  

- Flash-firstrun.001
- Flash-firstrun.txt
- Flash-firstrun.log

were found. We just need to find the md5 value written in one of these files.  

**Flag: grodno{09817bced4213360c1cb2749aa375523_2bdab2c08b5b507876bf2f2d7e548cc5}**  

## 3. Prompt ... (something. I forgot)  

A large set of Windows eevent logs were given and it was my first time looking at it. Multiple folders containing ```.evtx``` file were given with the names like  
command and control, credential access, defense evasion, discovery, lateral movement etc.  

The instruction include finding the staged Powershell credential-phishing chain, and recovering:  
1. The name of the function that asks for credentials
2. The placeholder marker left in the script for the next action

Since it kinda give away a hint that this is about credetian theft, I looked into the credential access folder.  

There were a list of Windows event logs and there were no way for me to go throughe each. Did some researching, and discovered that Powershell Script Block Logging most likely to occur with event IDs like: 4104, 4103, 4688.  
I though there were only one file with the event ID 4104 at the time and wasted my time digging into other folders.  
But then I found my way back into this one excel sheet provided which contained all event IDs available for each folder, and discovered that there were actually two files with the event ID 4104!  

I checked the other file and found the function.  

<img width="936" height="654" alt="prompt" src="https://github.com/user-attachments/assets/b78cd158-64c8-463f-b921-8ff3453df8af" />


**Flag: grodno{Invoke-LoginPrompt_ R{START_PROCESS}}**


# Payload Writeup  

Just a disclaimer: This writeup is written wayyy beyond the date of the CTF. Which means, I've forgotten most bits of it
and am currently relying on the my past notes. With that being said, you might see some solutions not being explained with much detailed solution.  

## Flag 1  

> During execution, the malware initializes the COM library on its main thread. Based on the imported functions, which DLL is responsible for providing this functionality? (filename.ext)

As someone who is new to this whole concept, I did some study while solving the challenge.<br/>
1. What is COM?<br/>
COM stands for Component Object Model. It’s a Windows technology that allows programs to:
- Create and use objects across different processes.
- Enable communication between software components, even if they’re written in different languages.
- Think of it as a shared object library system for Windows.

2. Why initialize COM?<br/>
Before a thread in a program can use COM objects, the COM library must be initialized on that thread.<br/>
This is done with CoInitialize or CoInitializeEx. It basically “turns on” COM support for that thread so the program can safely create or manipulate COM objects.
<br/>
In our Kali Linux,<br/>

```
objdump -x AetherDesk-v74-77.exe | grep -i coinitialize -A 5
```

This search for mentions of the function CoInitialize (or CoInitializeEx) inside the executable.<br/>
If the malware calls CoInitialize, it will appear either in the import table or as a string reference. Seeing CoInitialize tells you: the malware uses COM, and it initializes it on the main thread.<br/><br/>
From there, we know the responsible DLL is ```ole32.dll```, because that’s the Windows DLL exporting the COM functions.  

## Flag 2  

> Which GUID is used by the binary to instantiate the object containing the data and code for execution? (********-****-****-****-************)

Note: COM objects are identified by GUIDs, usually called CLSIDs (Class IDs). To find this, I simply use Ghidra.  

## Flag 3  

> Which .NET framework feature is the attacker using to bridge calls between a managed .NET class and an unmanaged native binary? (string)

Based on my findings on the internet, there are only a few features of .NET framework and COM Interop is the only one that allows .NET classes to expose themselves as COM objects or use existing COM objects.<br/>
Using COM (CoInitialize, CoCreateInstance) likely exposing .NET classes to unmanaged code.<br/><br/>
This points directly to COM Interop.  

## Flag 4  

> Which Opcode in the disassembly is responsible for calling the first function from the managed code? (** ** **)  

Note: An opcode (short for operation code) is a part of a machine language instruction that specifies the operation to be performed by the processor. It is a fundamental component of the instruction set architecture (ISA) of a CPU or other processing unit. 
The opcode, along with its operands, forms a complete machine instruction that the processor can execute.<br/>

If we were to describe the process of finding the call function:<br/>
1. The disassembled main function started at address 140001c40.
2. Using Ghidra, we see that In the decompiled code, we see that the first call into the COM object was (**(code **)(*local_208[0] + 0x68)). This means the function being called is at the offset of 0x68 from the object's virtual function table (vtable).
3. Finally, we located the instruction CALL qword ptr [RAX + 0x68] at address 140001d23.

The corresponding opcode for this is ```FF 50 68```.  

## Flag 6  

> Which Opcode in the disassembly is responsible for calling the decryption logic from the managed code? (** ** **)

The easy way is to search for the string "** code **" in the dissambly. The answer popped up real quick such that:
CALL qword ptr [RAX + 0x58] at address 1400020f6.<br/> 

The opcode for this instruction is ```FF 50 58```.  

## Flag 7  

> Which Win32 API is being utilized by the binary to resolve the killswitch domain name? (string)

So among winAPI functions that are usually being used to resolve domain name are :gethostbyname, getaddrinfo.<br/>
I searched up the strings in Kali such as the following:  
```
objdump -x AetherDesk-v74-77.exe | grep -i getaddrinfo
```

And it resulted with
```
6e5e      964    K32  EnumProcessModules
         7454    224  NetShareEnum
```
getaddrinfo is the standard Win32 API for performing DNS lookups to resolve hostnames to IP addresses. The result of this call determines whether the "Kill Switch Triggered" message is printed.  

## Flag 8  

> Which network-related API does the binary use to gather details about each shared resource on a server? (string)

We know that network-related API to enumerate files on a server are: NetShareEnum, NetOpenEnum, NetServerEnum.<br/>
The easy way is to search for "enum" string in Kali and we will be provided with:  
```
6e5e      964  K32  EnumProcessModules
         7454  224  NetShareEnum
```

Or, we can use Ghidra to look into network-related functions in NETAPI32.dll. 
And then, we can look into the Ghidra's Symbol Tree and analyze the function there.

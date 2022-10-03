# Unicorn-Crack-Me-Patcher

A patcher wrote for the Unicorn Crack-Me

I was bored and I decided to write a patcher for the Unicorn Crack-Me from https://reverse.put.as/wp-content/uploads/2010/05/2-Unicorn.zip

Normally, I'd bother to reverse engineer the key generation (MD5-based in this case), but I found it less time-consuming to simply patch the instructions that deal with the verification.

Normally the instructions that decide whether you get the "Wrong serial" message are in the "validate" method:
```asm
...
00002b41         test       al, al
00002b43         jnz        0x2b6e                                 ; Here's the conditional jump based on the previous comparison
00002b45         mov        dword [ss:esp+0x10], 0x0               ; The afore "JNZ" isn't executed so we go here and here's the Fail case
00002b4d         mov        dword [ss:esp+0xc], 0x0
00002b55         mov        dword [ss:esp+0x8], 0x3054             ; @"Try again"
00002b5d         mov        dword [ss:esp+0x4], 0x3064             ; @"The serial is not valid."
00002b65         mov        dword [ss:esp], 0x3074                 ; @"Error!"
00002b6c         jmp        0x2b95
00002b6e         mov        dword [ss:esp+0x10], 0x0               ; We're on the good scenario theritory
...
00002b76         mov        dword [ss:esp+0xc], 0x0               
imp___symbol_stub__NSRunAlertPanel
00002b7e         mov        dword [ss:esp+0x8], 0x3084             ; @"OK"
imp___symbol_stub__NSRunAlertPanel
00002b86         mov        dword [ss:esp+0x4], 0x3094             ; @"The serial is valid."
imp___symbol_stub__NSRunAlertPanel
00002b8e         mov        dword [ss:esp], 0x30a4
```
All we have to do is to pretty much change the `JNZ` instruction, which is a conditional jump, to a `JMP` which will always jump regardless of the `test al,al`.
All the rest of the program is arhitecture detection, structures in the FAT Mach-O and verbose stuff.

### Contact me
Twitter: GeoSn0w (@FCE365)
WWW: https://idevicecentral.com

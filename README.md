# ASCII_Encoder
x86 ASCII Encoder via AND, SUB, PUSH, POP

**Introduction:**

This encoder is based on egghunter found in [https://www.exploit-db.com/exploits/5342]().
Core idea is that every dword can be derived using 3 SUB instructions
with operands consisting strictly of ASCII compatible bytes.

**What it does?:**  

Suppose that we want to push `\x05\xEB\xD1\x8B (0x8BD1EB05)` to the stack.
Than we can do it as follows:

```
AND EAX, 3F465456
AND EAX, 40392B29         ; Two AND instructions zero EAX
SUB EAX, 3E716230         ; Subtracting 3 dwords consisting
SUB EAX, 5D455523         ; of ASCII compatible bytes from 0x00000000
SUB EAX, 5E5D7722         ; we get EAX = 0x8BD1EB05
PUSH EAX
```

**Mandatory bytes:**

```
\x25  AND EAX, imm32
\x2d  SUB EAX, imm32
\x50  PUSH EAX
\x61  POPAD
```

**How to use:**  

Edit the `SETTINGS` section and simply run as  

```
$ ./ASCIIencoder
```

**ProTip:**  

Take special attention to the memory between the end of decoder instructions
and the beginning of decoded shellcode. Program flow must seamlessly step over
this memory. If this "bridge memory area" contains illegal opcodes they can
be rewritten with additional PUSH instruction appended to the end of generated
shellcode. Use for example PUSH 0x41414141.
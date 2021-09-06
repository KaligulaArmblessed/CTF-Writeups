## Unknown

#### TLDR
Binary was packed with UPX and needs to be unpacked first. It can then be seen that the string "tryharder" (lol) was thrown through a caesar cipher (ROT2), where every ASCII character was incremented by 2 to give the final password. Entering the correct password will give you the flag. 

#### Details
Running the binary we get: 
```
./med_re_1
Enter the password: cat 
Access Denied! Try Harder :p
```
However, when I chucked it into Radare2, I got a mess. I then realized that the executable was packed with UPX:
```
checksec med_re_1_original 
    Arch:     amd64-64-little
    RELRO:    No RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
    Packer:   Packed with UPX
```
Unpacking the binary: 
```
upx -d med_re_1
                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2020
UPX 3.96        Markus Oberhumer, Laszlo Molnar & John Reiser   Jan 23rd 2020

        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
    925424 <-    329428   35.60%   linux/amd64   med_re_1

Unpacked 1 file.
```
Putting the binary through Radare2 and examining the main function: 
```asm
┌ 231: int main (int argc, char **argv, char **envp);
│           ; var int64_t var_80h @ rbp-0x80
│           ; var int64_t var_7ah @ rbp-0x7a
│           ; var int64_t var_72h @ rbp-0x72
│           ; var int64_t var_70h @ rbp-0x70
│           ; var int64_t var_8h @ rbp-0x8
│           0x00401e14      f30f1efa       endbr64
│           0x00401e18      55             push rbp
│           0x00401e19      4889e5         mov rbp, rsp
│           0x00401e1c      4883c480       add rsp, 0xffffffffffffff80
│           0x00401e20      64488b042528.  mov rax, qword fs:[0x28]
│           0x00401e29      488945f8       mov qword [var_8h], rax
│           0x00401e2d      31c0           xor eax, eax
│           0x00401e2f      48b874727968.  movabs rax, 0x6564726168797274 ; 'tryharde'
│           0x00401e39      48894586       mov qword [var_7ah], rax
│           0x00401e3d      66c7458e7200   mov word [var_72h], 0x72    ; 'r' ; 114
│           0x00401e43      c74580000000.  mov dword [var_80h], 0
│       ┌─< 0x00401e4a      eb1c           jmp 0x401e68
│       │   ; CODE XREF from main @ 0x401e7a
│      ┌──> 0x00401e4c      8b4580         mov eax, dword [var_80h]
│      ╎│   0x00401e4f      4898           cdqe
│      ╎│   0x00401e51      0fb6440586     movzx eax, byte [rbp + rax - 0x7a]
│      ╎│   0x00401e56      83c002         add eax, 2
│      ╎│   0x00401e59      89c2           mov edx, eax
│      ╎│   0x00401e5b      8b4580         mov eax, dword [var_80h]
│      ╎│   0x00401e5e      4898           cdqe
│      ╎│   0x00401e60      88540586       mov byte [rbp + rax - 0x7a], dl
│      ╎│   0x00401e64      83458001       add dword [var_80h], 1
│      ╎│   ; CODE XREF from main @ 0x401e4a
│      ╎└─> 0x00401e68      837d8063       cmp dword [var_80h], 0x63
│      ╎┌─< 0x00401e6c      7f0e           jg 0x401e7c
│      ╎│   0x00401e6e      8b4580         mov eax, dword [var_80h]
│      ╎│   0x00401e71      4898           cdqe
│      ╎│   0x00401e73      0fb6440586     movzx eax, byte [rbp + rax - 0x7a]
│      ╎│   0x00401e78      84c0           test al, al
│      └──< 0x00401e7a      75d0           jne 0x401e4c
│       │   ; CODE XREF from main @ 0x401e6c
│       └─> 0x00401e7c      488d3d81110b.  lea rdi, str.Enter_the_password:_ ; 0x4b3004 ; "Enter the password: "
│           0x00401e83      b800000000     mov eax, 0
│           0x00401e88      e8f3ee0000     call 0x410d80
│           0x00401e8d      488d4590       lea rax, [var_70h]
│           0x00401e91      4889c6         mov rsi, rax
│           0x00401e94      488d3d7e110b.  lea rdi, [0x004b3019]       ; "%s"
│           0x00401e9b      b800000000     mov eax, 0
│           0x00401ea0      e86bf00000     call 0x410f10
│           0x00401ea5      488d5586       lea rdx, [var_7ah]
│           0x00401ea9      488d4590       lea rax, [var_70h]
│           0x00401ead      4889d6         mov rsi, rdx
│           0x00401eb0      4889c7         mov rdi, rax
│           0x00401eb3      e878f2ffff     call 0x401130
│           0x00401eb8      85c0           test eax, eax
│       ┌─< 0x00401eba      7518           jne 0x401ed4
│       │   0x00401ebc      488d3d59110b.  lea rdi, str.Access_Granted__Kudos_:D ; 0x4b301c ; "Access Granted! Kudos :D"
│       │   0x00401ec3      e8d8ed0100     call 0x420ca0
│       │   0x00401ec8      bf01000000     mov edi, 1
│       │   0x00401ecd      e883feffff     call 0x401d55
│      ┌──< 0x00401ed2      eb0c           jmp 0x401ee0
│      ││   ; CODE XREF from main @ 0x401eba
│      │└─> 0x00401ed4      488d3d5a110b.  lea rdi, str.Access_Denied__Try_Harder_:p ; 0x4b3035 ; "Access Denied! Try Harder :p"
│      │    0x00401edb      e8c0ed0100     call 0x420ca0
│      │    ; CODE XREF from main @ 0x401ed2
│      └──> 0x00401ee0      b800000000     mov eax, 0
│           0x00401ee5      488b4df8       mov rcx, qword [var_8h]
│           0x00401ee9      6448330c2528.  xor rcx, qword fs:[0x28]
│       ┌─< 0x00401ef2      7405           je 0x401ef9
│       │   0x00401ef4      e8c7ab0500     call 0x45cac0
│       │   ; CODE XREF from main @ 0x401ef2
│       └─> 0x00401ef9      c9             leave
└           0x00401efa      c3             ret
```
Looking at the important part of the main function: 
```asm
│       ┌─< 0x00401e4a      eb1c           jmp 0x401e68
│       │   ; CODE XREF from main @ 0x401e7a
│      ┌──> 0x00401e4c      8b4580         mov eax, dword [var_80h]
│      ╎│   0x00401e4f      4898           cdqe
│      ╎│   0x00401e51      0fb6440586     movzx eax, byte [rbp + rax - 0x7a]
│      ╎│   0x00401e56      83c002         add eax, 2
│      ╎│   0x00401e59      89c2           mov edx, eax
│      ╎│   0x00401e5b      8b4580         mov eax, dword [var_80h]
│      ╎│   0x00401e5e      4898           cdqe
│      ╎│   0x00401e60      88540586       mov byte [rbp + rax - 0x7a], dl
│      ╎│   0x00401e64      83458001       add dword [var_80h], 1
│      ╎│   ; CODE XREF from main @ 0x401e4a
│      ╎└─> 0x00401e68      837d8063       cmp dword [var_80h], 0x63
│      ╎┌─< 0x00401e6c      7f0e           jg 0x401e7c
│      ╎│   0x00401e6e      8b4580         mov eax, dword [var_80h]
│      ╎│   0x00401e71      4898           cdqe
│      ╎│   0x00401e73      0fb6440586     movzx eax, byte [rbp + rax - 0x7a]
│      ╎│   0x00401e78      84c0           test al, al
│      └──< 0x00401e7a      75d0           jne 0x401e4c
│       │   ; CODE XREF from main @ 0x401e6c
│       └─> 0x00401e7c      488d3d81110b.  lea rdi, str.Enter_the_password:_ ; 0x4b3004 ; "Enter the password: "
```
1. var_80h is the counter. When var_80h hits 9, the loop will stop as all the letters of "tryharder" would have been looped through. 
2. var_80h starts from 0. 
3. Each character of "tryharder" (at rbp+rax-0x7a) is moved into eax
4. 2 is added to eax, hence each character is incremented by 2
5. var_80h is incremented by one, then the loop continues
6. The final password gets stored into rbp+rax-0x7a

So all we need to do is to examine what's in rbp+rax-0x7a after the loop terminates, and that would be our password! 
```asm
[0x00401e7c]> x/32x @rbp+rax-0x7a
- offset -       0 1  2 3  4 5  6 7  8 9  A B  C D  E F  0123456789ABCDEF
0x7fff882d2a66  7674 7b6a 6374 6667 7400 0000 0000 0000  vt{jctfgt.......
0x7fff882d2a76  0000 0100 0000 0000 0000 0100 0000 0000  ................
```
Entering the password after running the binary gives us the flag: GrabCON{str1ngs_m4gic_440f1}

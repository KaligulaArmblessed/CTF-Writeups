## Babyrev

#### TLDR
Disassembling the binary will show that XOR operations with 0xdeafbeef were performed on 5 hexadecimal numbers. The result of the XOR operations (in hexadecimal) was then converted into ASCII, which gave the flag.

#### Details
This binary does not print anything when run, so let's chuck it through Radare2 and examine the main function:
```asm
┌ 129: int main (int argc, char **argv, char **envp);
│           ; var int64_t var_34h @ rbp-0x34
│           ; var int64_t var_30h @ rbp-0x30
│           ; var int64_t var_2ch @ rbp-0x2c
│           ; var int64_t var_28h @ rbp-0x28
│           ; var int64_t var_24h @ rbp-0x24
│           ; var int64_t var_20h @ rbp-0x20
│           ; var int64_t var_1ch @ rbp-0x1c
│           ; var int64_t var_18h @ rbp-0x18
│           ; var int64_t var_10h @ rbp-0x10
│           ; var int64_t var_8h @ rbp-0x8
│           0x55c43a265129      f30f1efa       endbr64
│           0x55c43a26512d      55             push rbp
│           0x55c43a26512e      4889e5         mov rbp, rsp
│           0x55c43a265131      c745cc8ddfdf.  mov dword [var_34h], 0x99dfdf8d
│           0x55c43a265138      8b45cc         mov eax, dword [var_34h]
│           0x55c43a26513b      35efbeadde     xor eax, 0xdeadbeef
│           0x55c43a265140      8945d0         mov dword [var_30h], eax
│           0x55c43a265143      c745d494f0e2.  mov dword [var_2ch], 0x9de2f094
│           0x55c43a26514a      8b45d4         mov eax, dword [var_2ch]
│           0x55c43a26514d      35efbeadde     xor eax, 0xdeadbeef
│           0x55c43a265152      8945d8         mov dword [var_28h], eax
│           0x55c43a265155      48b88b8ddfac.  movabs rax, 0x7830acdf8d8b
│           0x55c43a26515f      488945e8       mov qword [var_18h], rax
│           0x55c43a265163      488b45e8       mov rax, qword [var_18h]
│           0x55c43a265167      35efbeadde     xor eax, 0xdeadbeef
│           0x55c43a26516c      8945dc         mov dword [var_24h], eax
│           0x55c43a26516f      48b896dfdaea.  movabs rax, 0x5feadadf96
│           0x55c43a265179      488945f0       mov qword [var_10h], rax
│           0x55c43a26517d      488b45f0       mov rax, qword [var_10h]
│           0x55c43a265181      35efbeadde     xor eax, 0xdeadbeef
│           0x55c43a265186      8945e0         mov dword [var_20h], eax
│           0x55c43a265189      48b8df8fcbb8.  movabs rax, 0x5f33b8cb8fdf
│           0x55c43a265193      488945f8       mov qword [var_8h], rax
│           0x55c43a265197      488b45f8       mov rax, qword [var_8h]
│           0x55c43a26519b      35efbeadde     xor eax, 0xdeadbeef
│           0x55c43a2651a0      8945e4         mov dword [var_1ch], eax
│           0x55c43a2651a3      b800000000     mov eax, 0
│           0x55c43a2651a8      5d             pop rbp
└           0x55c43a2651a9      c3             ret
```

There are 5 hexadecimal numbers: 
* 0x99dfdf8d
* 0x9de2f094
* 0x7830acdf8d8b
* 0x5feadadf96
* 0x5f33b8cb8fdf

We notice that each number goes through a similar "set" of instructions as seen above in the code. 
```asm
mov dword [var_34h], 0x99dfdf8d
mov eax, dword [var_34h]
xor eax, 0xdeadbeef
mov dword [var_30h], eax
```
1. The hexadecimal number is moved into a variable
2. The variable is moved into a register
3. The variable (which is in the register) is XORed with 0xdeadbeef
4. The resulting hexadecimal number is moved into another variable

From here, we know that to get the flag, all we need to do is to XOR each of these numbers with 0xdeadbeef, convert the resulting hexadecimal number into ASCII and we would have the flag!
* 0x99dfdf8d ➞ 47726162 ➞ Grab
* 0x9de2f094 ➞ 434f4e7b ➞ CON{
* 0x7830acdf8d8b ➞ 783072723364 ➞ x0rr3d
* 0x5feadadf96 ➞ 5f34776179 ➞ _4way
* 0x5f33b8cb8fdf ➞ 5f3366663130 ➞ _3ff10

Adding the curly bracket at the end: 
GrabCON{x0rr3d_4way_3ff10}

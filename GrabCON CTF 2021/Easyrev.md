## Easyrev

#### TLDR
The binary takes an input as the "password", checks the password, and prints the flag if the password is correct. The correct password can be obtained by XORing 0x1466c7 with 0xdeadbeef followed by 0xdeaddead (or it can just be extracted from the compare password function). 

#### The proper way
Running the binary shows that it prompts for a password:
```
./baby_re_2
Looking for the flag?
Enter the key: cat
Wrong! Try Again ...
```
Chucking it through Radare2 as usual, we examine the main function, and realize that it is calling another function: 
```asm
┌ 25: int main (int argc, char **argv, char **envp);
│           0x55a53523c31f      f30f1efa       endbr64
│           0x55a53523c323      55             push rbp
│           0x55a53523c324      4889e5         mov rbp, rsp
│           0x55a53523c327      b800000000     mov eax, 0
│           0x55a53523c32c      e846ffffff     call fcn.55a53523c277
│           0x55a53523c331      b800000000     mov eax, 0
│           0x55a53523c336      5d             pop rbp
└           0x55a53523c337      c3             ret
```
Examining the other function: 
```asm
┌ 168: fcn.55a53523c277 ();
│           ; var int64_t var_18h @ rbp-0x18
│           ; var int64_t var_14h @ rbp-0x14
│           ; var int64_t var_10h @ rbp-0x10
│           ; var int64_t var_ch @ rbp-0xc
│           ; var int64_t var_8h @ rbp-0x8
│           0x55a53523c277      f30f1efa       endbr64
│           0x55a53523c27b      55             push rbp
│           0x55a53523c27c      4889e5         mov rbp, rsp
│           0x55a53523c27f      4883ec20       sub rsp, 0x20
│           0x55a53523c283      64488b042528.  mov rax, qword fs:[0x28]
│           0x55a53523c28c      488945f8       mov qword [var_8h], rax
│           0x55a53523c290      31c0           xor eax, eax
│           0x55a53523c292      c745ecc76614.  mov dword [var_14h], 0x1466c7
│           0x55a53523c299      8b45ec         mov eax, dword [var_14h]
│           0x55a53523c29c      35efbeadde     xor eax, 0xdeadbeef
│           0x55a53523c2a1      8945f0         mov dword [var_10h], eax
│           0x55a53523c2a4      8b45f0         mov eax, dword [var_10h]
│           0x55a53523c2a7      35addeadde     xor eax, 0xdeaddead
│           0x55a53523c2ac      8945f4         mov dword [var_ch], eax
│           0x55a53523c2af      488d3d530d00.  lea rdi, [0x55a53523d009] ; "Looking for the flag?"
│           0x55a53523c2b6      e8c5fdffff     call sym.imp.puts       ; int puts(const char *s)
│           0x55a53523c2bb      488d3d5d0d00.  lea rdi, str.Enter_the_key:_ ; 0x55a53523d01f ; "Enter the key: "
│           0x55a53523c2c2      b800000000     mov eax, 0
│           0x55a53523c2c7      e8d4fdffff     call sym.imp.printf     ; int printf(const char *format)
│           0x55a53523c2cc      488d45e8       lea rax, [var_18h]
│           0x55a53523c2d0      4889c6         mov rsi, rax
│           0x55a53523c2d3      488d3d550d00.  lea rdi, [0x55a53523d02f] ; "%d"
│           0x55a53523c2da      b800000000     mov eax, 0
│           0x55a53523c2df      e8ccfdffff     call sym.imp.__isoc99_scanf ; int scanf(const char *format)
│           0x55a53523c2e4      8b45e8         mov eax, dword [var_18h]
│           0x55a53523c2e7      3945f4         cmp dword [var_ch], eax
│       ┌─< 0x55a53523c2ea      750c           jne 0x55a53523c2f8
│       │   0x55a53523c2ec      b800000000     mov eax, 0
│       │   0x55a53523c2f1      e8b3feffff     call 0x55a53523c1a9
│      ┌──< 0x55a53523c2f6      eb0c           jmp 0x55a53523c304
│      │└─> 0x55a53523c2f8      488d3d330d00.  lea rdi, str.Wrong__Try_Again_... ; 0x55a53523d032 ; "Wrong! Try Again ..."
│      │    0x55a53523c2ff      e87cfdffff     call sym.imp.puts       ; int puts(const char *s)
│      │    ; CODE XREF from fcn.55a53523c277 @ 0x55a53523c2f6
│      └──> 0x55a53523c304      b800000000     mov eax, 0
│           0x55a53523c309      488b55f8       mov rdx, qword [var_8h]
│           0x55a53523c30d      644833142528.  xor rdx, qword fs:[0x28]
│       ┌─< 0x55a53523c316      7405           je 0x55a53523c31d
│       │   0x55a53523c318      e873fdffff     call sym.imp.__stack_chk_fail ; void __stack_chk_fail(void)
│       └─> 0x55a53523c31d      c9             leave
└           0x55a53523c31e      c3             ret
```
We notice that the important part that generates the password is in this specific part of the function: 
```asm
mov dword [var_14h], 0x1466c7
mov eax, dword [var_14h]
xor eax, 0xdeadbeef
mov dword [var_10h], eax
mov eax, dword [var_10h]
xor eax, 0xdeaddead
mov dword [var_ch], eax
```
1. 0x1466c7 is moved into a variable
2. The variable is moved into eax
3. The value in the eax (0x1466c7) is XORed with 0xdeadbeef to give eax = 0xdeb9d828
4. 0xdeb9d828 is moved into a variable
5. The value in the variable (0xdeb9d828) is moved into eax
6. The value in eax (0xdeb9d828) is XORed with 0xdeaddead to give eax = 0x140685

However, as we can see in the disassembly of the function above, the input is a signed decimal, hence, we have to convert 0x140685 to decimal which would be 1312389.
Using 1312389 as the password will give us the flag: GrabCON{y0u_g0t_it_8bb31}

#### The scammy way
We notice this part of the code from the disassembly of the function: 
```asm
│           0x55a53523c2e4      8b45e8         mov eax, dword [var_18h]
│           0x55a53523c2e7      3945f4         cmp dword [var_ch], eax
│       ┌─< 0x55a53523c2ea      750c           jne 0x55a53523c2f8
│       │   0x55a53523c2ec      b800000000     mov eax, 0
│       │   0x55a53523c2f1      e8b3feffff     call 0x55a53523c1a9
│      ┌──< 0x55a53523c2f6      eb0c           jmp 0x55a53523c304
│      │└─> 0x55a53523c2f8      488d3d330d00.  lea rdi, str.Wrong__Try_Again_... ; 0x55a53523d032 ; "Wrong! Try Again ..."
│      │    0x55a53523c2ff      e87cfdffff     call sym.imp.puts       ; int puts(const char *s)
│      │    ; CODE XREF from fcn.55a53523c277 @ 0x55a53523c2f6
│      └──> 0x55a53523c304      b800000000     mov eax, 0
```
If we set breakpoints and examine the operation flow, we realize that our input is stored into var_18, and the password is stored into var_ch. So all we need to do is to examine the value of var_ch and we will get the password! :D
```asm
[0x55a53523c2e7]> x/32x @rbp-0xc
- offset -       0 1  2 3  4 5  6 7  8 9  A B  C D  E F  0123456789ABCDEF
0x7fff241d3474  8506 1400 004c 796c f3b3 1f2d 9034 1d24  .....Lyl...-.4.$
0x7fff241d3484  ff7f 0000 31c3 2335 a555 0000 40c3 2335  ....1.#5.U..@.#5
```
As we can see, the part we are interested in is "850614". However, as little endian is used, the value is 0x140685. Converting it into decimal will give 1312389, which is the password, and will give us the flag: GrabCON{y0u_g0t_it_8bb31}

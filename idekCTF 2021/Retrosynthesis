## Retrosynthesis

#### TLDR
Disassembling the binary will show that each of the "reagents" is tied to a function, which performs a specific mathematical operation with certain numbers as specified in the functions. The "reaction" proceeds as the starting material (also a number) undergoes these operations depending on whether the functions are activated. In order to synthesize the "final product", cubane (in this case, also a number), the correct operations have to be activated. This can be done by patching the binary (e.g. using radare2). The functions that have to be activated to generate the final product are: NBS, Diels-Alder, SOCl2, Favorskii. 

#### Introduction
When we run the binary, we get:
```
./retrosynthesis   
    ____       __                              __  __              _     
   / __ \___  / /__________  _______  ______  / /_/ /_  ___  _____(_)____
  / /_/ / _ \/ __/ ___/ __ \/ ___/ / / / __ \/ __/ __ \/ _ \/ ___/ / ___/
 / _, _/  __/ /_/ /  / /_/ (__  ) /_/ / / / / /_/ / / /  __(__  ) (__  ) 
/_/ |_|\___/\__/_/   \____/____/\__, /_/ /_/\__/_/ /_/\___/____/_/____/  
                               /____/                                    
-------------------------------------------------------------------------
Note: You do not need to know Chemistry to do this challenge, though it will help :)
---------------------------------STATUS----------------------------------
Math: 28550354772583778 [Not done] | Chem: Benzene [Not added]
Math: 5456462 [Not done] | Chem: NBS [Not added]
Math: 495739824484 [Not done] | Chem: Diels-Alder Reaction [Not done]
Math: 216564715347 [Not done] | Chem: SOCl2 [Not added]
Math: 310568239917 [Not done] | Chem: Decarboxylation [Not done]
Math: 465725320289 [Not done] | Chem: Aldol Reaction [Not done]
Math: 87492136360x0000172e430815590 [Not done] | Chem: Favorskii Reaction [Not done]
---------------------------------RESULTS---------------------------------
Starting material: 8749110455712246664
Desired product: 111524754650467 Cubane
Obtained product: 8749110455712246664
-------------------------------------------------------------------------
You did not synthesize the final product! Synthesis failed. :(
-------------------------------------------------------------------------
```

Oh no! Our synthesis failed! But there does not seem to be a way to input steps or reagents into the binary. Let's disassemble it with r2. Taking a look at the functions that we have: 
```
r2 -d retrosynthesis
...
[0x7faecf103050]> aaa
...
[0x7faecf103050]> afl
0x55cb85b3d0e0    1 42           entry0
0x55cb85b40fe0    4 4124 -> 4135 reloc.__libc_start_main
0x55cb85b3d110    4 41   -> 34   sym.deregister_tm_clones
0x55cb85b3d140    4 57   -> 51   sym.register_tm_clones
0x55cb85b3d180    5 57   -> 54   sym.__do_global_dtors_aux
0x55cb85b3d0d0    1 6            sym.imp.__cxa_finalize
0x55cb85b3d1c0    1 9            entry.init0
0x55cb85b3d000    3 23           map._home_calsx_Downloads_CTF_idekctf_retrosynthesis_Retrosynthesis_retrosynthesis.r_x
0x55cb85b3d66c    1 22           sym.NBS
0x55cb85b3dd10    1 1            sym.__libc_csu_fini
0x55cb85b3d6ba    1 28           sym.decarb
0x55cb85b3dd14    1 9            sym._fini
0x55cb85b3d650    1 28           sym.benzene
0x55cb85b3d6f2    1 28           sym.favorsky
0x55cb85b3d6d6    1 28           sym.aldol
0x55cb85b3d69e    1 28           sym.SOCl2
0x55cb85b3d1c9   10 254          sym.str2md5
0x55cb85b3dcb0    4 93           sym.__libc_csu_init
0x55cb85b3d682    1 28           sym.diels
0x55cb85b3d2c7    1 84           sym.compute_md5
0x55cb85b3d0c0    1 6            sym.imp.MD5_Init
0x55cb85b3d080    1 6            sym.imp.strlen
0x55cb85b3d0a0    1 6            sym.imp.MD5_Update
0x55cb85b3d090    1 6            sym.imp.MD5_Final
0x55cb85b3d70e   31 1429         main
0x55cb85b3d030    1 6            sym.imp.printf
0x55cb85b3d040    1 6            sym.imp.snprintf
0x55cb85b3d050    1 6            sym.imp.puts
0x55cb85b3d060    1 6            sym.imp.putchar
0x55cb85b3d070    1 6            sym.imp.malloc
0x55cb85b3c000    3 126  -> 181  loc.imp.__gmon_start__
0x55cb85b3d0b0    1 6            sym.imp.sprintf
```

Taking a look at the very long main function, we see that there is a huge chunk of variables being defined on top...
```asm
0x55cb85b3d70e      55             push rbp
0x55cb85b3d70f      4889e5         mov rbp, rsp
0x55cb85b3d712      4881ec000100.  sub rsp, 0x100
0x55cb85b3d719      48b8886b62cd.  movabs rax, 0x796b159acd626b88
0x55cb85b3d723      488945f8       mov qword [var_8h], rax
0x55cb85b3d727      c745e4000000.  mov dword [var_1ch], 0
0x55cb85b3d72e      c745e0000000.  mov dword [var_20h], 0
0x55cb85b3d735      c745dc000000.  mov dword [var_24h], 0
0x55cb85b3d73c      c745d8000000.  mov dword [var_28h], 0
0x55cb85b3d743      c745d4000000.  mov dword [var_2ch], 0
0x55cb85b3d74a      c745d0000000.  mov dword [var_30h], 0
0x55cb85b3d751      c745cc000000.  mov dword [var_34h], 0
```
...And that there is some sort of function where the value of these variables is compared to 1. If the value of the variable equals to 1, some function will be activated, and the functions seem to be our "reagents" in this synthesis (in this case, the benzene function): 
```asm
            0x55cb85b3d7df      837de401       cmp dword [var_1ch], 1
│       ┌─< 0x55cb85b3d7e3      7521           jne 0x55cb85b3d806
│       │   0x55cb85b3d7e5      488b45f8       mov rax, qword [var_8h]
│       │   0x55cb85b3d7e9      4889c7         mov rdi, rax
│       │   0x55cb85b3d7ec      e85ffeffff     call sym.benzene
│       │   0x55cb85b3d7f1      488945f8       mov qword [var_8h], rax
│       │   0x55cb85b3d7f5      488d05ec0a00.  lea rax, str.Math:_28550354772583778__Done___Chem:_Benzene__Added_ ; 0x55cb85b3e2e8 ; "Math: 28550354772583778 [Done] | Chem: Benzene [Added]"
│       │   0x55cb85b3d7fc      4889c7         mov rdi, rax
│       │   0x55cb85b3d7ff      e84cf8ffff     call sym.imp.puts       ; int puts(const char *s)
│      ┌──< 0x55cb85b3d804      eb0f           jmp 0x55cb85b3d815
│      │└─> 0x55cb85b3d806      488d05130b00.  lea rax, str.Math:_28550354772583778__Not_done___Chem:_Benzene__Not_added_ ; 0x55cb85b3e320 ; "Math: 28550354772583778 [Not done] | Chem: Benzene [Not added]"                                                                                                                                                                               
│      │    0x55cb85b3d80d      4889c7         mov rdi, rax
│      │    0x55cb85b3d810      e83bf8ffff     call sym.imp.puts       ; int puts(const char *s)
│      │    ; CODE XREF from main @ 0x55cb85b3d804
│      └──> 0x55cb85b3d815      837de001       cmp dword [var_20h], 1
```
We also notice that the starting material is ```0x796b159acd626b88 = 8749110455712246664```. We also know that the end product is ```0x656e61627563 = 111524754650467``` (which is actually Cubane :D). 

This is where there are two ways to do this challenge (and where the Chemistry comes in!). We can either analyze each of the functions, determine what they do (and the math behind it), or we can figure out (or Google) the synthesis of Cubane and figure out which reagents we need/reactions to perform. Retrosynthesis! :D

#### The Math Way
Here we try to figure out the math behind all 7 of the functions: benzene, NBS, Diels-Alder, SOCl2, decarboxylation, Aldol reaction, and Favorskii reaction. (Small note: All the numbers involved in the math operations actually translate to the chemicals/reactions :DD Chemistry is cool!)
_Function 1: Benzene_
```asm
[0x7faecf103050]> pdf @sym.benzene
            ; CALL XREF from main @ 0x55cb85b3d7ec
┌ 28: sym.benzene (int64_t arg1);
│           ; var int64_t var_8h @ rbp-0x8
│           ; arg int64_t arg1 @ rdi
│           0x55cb85b3d650      55             push rbp
│           0x55cb85b3d651      4889e5         mov rbp, rsp
│           0x55cb85b3d654      48897df8       mov qword [var_8h], rdi ; arg1
│           0x55cb85b3d658      48b862656e7a.  movabs rax, 0x656e657a6e6562 ; 'benzene'
│           0x55cb85b3d662      480145f8       add qword [var_8h], rax
│           0x55cb85b3d666      488b45f8       mov rax, qword [var_8h]
│           0x55cb85b3d66a      5d             pop rbp
└           0x55cb85b3d66b      c3             ret
```
This one is pretty obvious; var_8h contains our starting material, and we can see that 0x656e657a6e6562 is being added to it. 
_Function 2: NBS_
```asm
[0x7faecf103050]> pdf @sym.NBS
            ; CALL XREF from main @ 0x55cb85b3d822
┌ 22: sym.NBS (int64_t arg1);
│           ; var int64_t var_8h @ rbp-0x8
│           ; arg int64_t arg1 @ rdi
│           0x55cb85b3d66c      55             push rbp
│           0x55cb85b3d66d      4889e5         mov rbp, rsp
│           0x55cb85b3d670      48897df8       mov qword [var_8h], rdi ; arg1
│           0x55cb85b3d674      488145f84e42.  add qword [var_8h], 0x53424e ; [0x53424e:8]=-1
│           0x55cb85b3d67c      488b45f8       mov rax, qword [var_8h]
│           0x55cb85b3d680      5d             pop rbp
└           0x55cb85b3d681      c3             ret
```
0x53424e is being added to the number that we have (starting material + 0x656e657a6e6562 if the benzene function is activated, starting material if the benzene function is not activated). From now on the number would be referred to as the result. 
_Function 3: Diels-Alder_
```asm
[0x7faecf103050]> pdf @sym.diels
            ; CALL XREF from main @ 0x55cb85b3d858
┌ 28: sym.diels (int64_t arg1);
│           ; var int64_t var_8h @ rbp-0x8
│           ; arg int64_t arg1 @ rdi
│           0x55cb85b3d682      55             push rbp
│           0x55cb85b3d683      4889e5         mov rbp, rsp
│           0x55cb85b3d686      48897df8       mov qword [var_8h], rdi ; arg1
│           0x55cb85b3d68a      48b86469656c.  movabs rax, 0x736c656964 ; 'diels'
│           0x55cb85b3d694      483145f8       xor qword [var_8h], rax
│           0x55cb85b3d698      488b45f8       mov rax, qword [var_8h]
│           0x55cb85b3d69c      5d             pop rbp
└           0x55cb85b3d69d      c3             ret
```
If this function is activated, the result will be XORed with 0x736c656964. 
_Function 4: SOCl2_
```asm
[0x7faecf103050]> pdf @sym.SOCl2
            ; CALL XREF from main @ 0x55cb85b3d88e
┌ 28: sym.SOCl2 (int64_t arg1);
│           ; var int64_t var_8h @ rbp-0x8
│           ; arg int64_t arg1 @ rdi
│           0x55cb85b3d69e      55             push rbp
│           0x55cb85b3d69f      4889e5         mov rbp, rsp
│           0x55cb85b3d6a2      48897df8       mov qword [var_8h], rdi ; arg1
│           0x55cb85b3d6a6      48b8534f436c.  movabs rax, 0x326c434f53 ; 'SOCl2'
│           0x55cb85b3d6b0      480145f8       add qword [var_8h], rax
│           0x55cb85b3d6b4      488b45f8       mov rax, qword [var_8h]
│           0x55cb85b3d6b8      5d             pop rbp
└           0x55cb85b3d6b9      c3             ret
```
If this function is activated, 0x326c434f53 will be added to the result. 
_Function 5: Decarboxylation_
```asm
[0x7faecf103050]> pdf @sym.decarb
            ; CALL XREF from main @ 0x55cb85b3d8c4
┌ 28: sym.decarb (int64_t arg1);
│           ; var int64_t var_8h @ rbp-0x8
│           ; arg int64_t arg1 @ rdi
│           0x55cb85b3d6ba      55             push rbp
│           0x55cb85b3d6bb      4889e5         mov rbp, rsp
│           0x55cb85b3d6be      48897df8       mov qword [var_8h], rdi ; arg1
│           0x55cb85b3d6c2      48b8d3bcb0b0.  movabs rax, 0xffffffb7b0b0bcd3
│           0x55cb85b3d6cc      480145f8       add qword [var_8h], rax
│           0x55cb85b3d6d0      488b45f8       mov rax, qword [var_8h]
│           0x55cb85b3d6d4      5d             pop rbp
└           0x55cb85b3d6d5      c3             ret
```
If this function is activated, 0xffffffb7b0b0bcd3 would be added to the result, which is the same as 310568239917 being subtracted from the result. 
_Function 6: Aldol reaction_
Personally my favourite reaction in Organic Chemistry! :D
```asm
[0x7faecf103050]> pdf @sym.aldol
            ; CALL XREF from main @ 0x55cb85b3d8fa
┌ 28: sym.aldol (int64_t arg1);
│           ; var int64_t var_8h @ rbp-0x8
│           ; arg int64_t arg1 @ rdi
│           0x55cb85b3d6d6      55             push rbp
│           0x55cb85b3d6d7      4889e5         mov rbp, rsp
│           0x55cb85b3d6da      48897df8       mov qword [var_8h], rdi ; arg1
│           0x55cb85b3d6de      48b8616c646f.  movabs rax, 0x6c6f646c61 ; 'aldol'
│           0x55cb85b3d6e8      480145f8       add qword [var_8h], rax
│           0x55cb85b3d6ec      488b45f8       mov rax, qword [var_8h]
│           0x55cb85b3d6f0      5d             pop rbp
└           0x55cb85b3d6f1      c3             ret
```
If this function is activated, 0x6c6f646c61 would be added to the result. 
_Function 7: Favorskii reaction_
```asm
[0x7faecf103050]> pdf @sym.favorsky
            ; CALL XREF from main @ 0x55cb85b3d930
┌ 28: sym.favorsky (int64_t arg1);
│           ; var int64_t var_8h @ rbp-0x8
│           ; arg int64_t arg1 @ rdi
│           0x55cb85b3d6f2      55             push rbp
│           0x55cb85b3d6f3      4889e5         mov rbp, rsp
│           0x55cb85b3d6f6      48897df8       mov qword [var_8h], rdi ; arg1
│           0x55cb85b3d6fa      48b86661766f.  movabs rax, 0x796b73726f766166 ; 'favorsky'
│           0x55cb85b3d704      483145f8       xor qword [var_8h], rax
│           0x55cb85b3d708      488b45f8       mov rax, qword [var_8h]
│           0x55cb85b3d70c      5d             pop rbp
└           0x55cb85b3d70d      c3             ret
```
If this function is activated, the result would be XORed with 0x796b73726f766166. 

Knowing how these functions work, you can do a little bit of math and figure out how to get 111524754650467 (Cubane). 

#### The Chem Way :D
This method is much, much easier than the Math one. All you need to do is to Google the synthesis of Cubane (can be found at https://www.synarchive.com/syn/14 -- SynArchive is basically Exploit-DB for Chemists). 
![alt text]()

Simply by looking at the synthesis, we can figure out that NBS, Diels-Alder reaction, SOCl2 and Favorskii reaction are needed to make Cubane. 

#### Patching the binary
We can patch the binary to switch on the required functions using the almighty radare2. We can open the binary in write mode: 
``` 
r2 -w retrosynthesis
```
From looking at the functions, we know that the variables that we have to change to 1 are var_20h, var_24h, var_28h and var_34h. We can do so as such: 
```
[0x000010e0]> wx c745e0010000 @0x0000172e
[0x000010e0]> wx c745dc010000 @0x00001735
[0x000010e0]> wx c745d8010000 @0x0000173c
[0x000010e0]> wx c745cc010000 @0x00001751
```
Printing the main function again: 
```asm
0x00001719      48b8886b62cd.  movabs rax, 0x796b159acd626b88
0x00001723      488945f8       mov qword [var_8h], rax
0x00001727      c745e4000000.  mov dword [var_1ch], 0
0x0000172e      c745e0010000.  mov dword [var_20h], 1
0x00001735      c745dc010000.  mov dword [var_24h], 1
0x0000173c      c745d8010000.  mov dword [var_28h], 1
0x00001743      c745d4000000.  mov dword [var_2ch], 0
0x0000174a      c745d0000000.  mov dword [var_30h], 0
0x00001751      c745cc010000.  mov dword [var_34h], 1
```
We can see that now the required functions are now activated! Running the binary, we get: 
```
./retrosynthesis
    ____       __                              __  __              _     
   / __ \___  / /__________  _______  ______  / /_/ /_  ___  _____(_)____
  / /_/ / _ \/ __/ ___/ __ \/ ___/ / / / __ \/ __/ __ \/ _ \/ ___/ / ___/
 / _, _/  __/ /_/ /  / /_/ (__  ) /_/ / / / / /_/ / / /  __(__  ) (__  ) 
/_/ |_|\___/\__/_/   \____/____/\__, /_/ /_/\__/_/ /_/\___/____/_/____/  
                               /____/                                    
-------------------------------------------------------------------------
Note: You do not need to know Chemistry to do this challenge, though it will help :)
---------------------------------STATUS----------------------------------
Math: 28550354772583778 [Not done] | Chem: Benzene [Not added]
Math: 5456462 [Done] | Chem: NBS [Added]
Math: 495739824484 [Done] | Chem: Diels-Alder Reaction [Done]
Math: 216564715347 [Done] | Chem: SOCl2 [Added]
Math: 310568239917 [Not done] | Chem: Decarboxylation [Not done]
Math: 465725320289 [Not done] | Chem: Aldol Reaction [Not done]
Math: 8749213636430815590 [Done] | Chem: Favorskii Reaction [Done]
---------------------------------RESULTS---------------------------------
Starting material: 8749110455712246664
Desired product: 111524754650467 Cubane
Obtained product: 111524754650467
-------------------------------------------------------------------------
You synthesized cubane! :D
idek{y0U_90tt4_ke3p_tr13NE_h47d3r}

                                                       .:
                                                      / )
                                                     ( (
                                                      \ )
      o                                             ._(/_.
       o                                            |___%|
     ___              ___  ___  ___  ___             | %|
     | |        ._____|_|__|_|__|_|__|_|_____.       | %|
     | |        |__________________________|%|       | %|
     |o|          | | |%|  | |  | |  |~| | |        .|_%|.
    .' '.         | | |%|  | |  |~|  |#| | |        | ()%|
   /  o  \        | | :%:  :~:  : :  :#: | |     .__|___%|__.
  :____o__:     ._|_|_.    ._|_|_.   |      ___%|_
  '._____.'     |___|%|                |___|%|   |_____(____  )
                                                           ( (
                                                            \ '._____.-
                                                             '._______.-

-------------------------------------------------------------------------
```

The flag is idek{y0U_90tt4_ke3p_tr13NE_h47d3r} :D

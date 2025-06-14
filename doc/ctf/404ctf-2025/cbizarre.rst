cbizarre 1/2
=======================

On fait simplement un strings du binaire, et on voit un lien pastebin : https://pastebin.com/raw/n8CXuwE0

On se rend dessus et on obtient le flag : **404CTF{PAst3_mY_FL2g}**

cbizarre 2/2
=======================

Cette fois on va devoir faire du reverse. On va donc lancer le binaire avec radare : r2 -d chall2 

On désassemble la fonction main pour regarder son fonctionnement : 

.. code-block:: console

    [0x7f22fd556295]> pdf @sym.main
    Do you want to print 380 lines? (y/N) y
                ; DATA XREF from entry0 @ 0x55fa1ae7e0b4(r)
    ┌ 1585: int main (int argc, char **argv, char **envp);
    │ `- args(rdi, rsi) vars(6:sp[0x10..0x38])
    │           0x55fa1ae7e205      55             push rbp
    │           0x55fa1ae7e206      4889e5         mov rbp, rsp
    │           0x55fa1ae7e209      4883ec30       sub rsp, 0x30
    │           0x55fa1ae7e20d      897ddc         mov dword [var_24h], edi ; argc
    │           0x55fa1ae7e210      488975d0       mov qword [var_30h], rsi ; argv
    │           0x55fa1ae7e214      837ddc02       cmp dword [var_24h], 2
    │       ┌─< 0x55fa1ae7e218      742f           je 0x55fa1ae7e249
    │       │   0x55fa1ae7e21a      488b45d0       mov rax, qword [var_30h]
    │       │   0x55fa1ae7e21e      488b10         mov rdx, qword [rax]
    │       │   0x55fa1ae7e221      488b05182e..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │       │   0x55fa1ae7e228      488d0dd90d..   lea rcx, str.Usage:__s__password__n ; 0x55fa1ae7f008 ; "Usage: %s <password>\n"
    │       │   0x55fa1ae7e22f      4889ce         mov rsi, rcx
    │       │   0x55fa1ae7e232      4889c7         mov rdi, rax
    │       │   0x55fa1ae7e235      b800000000     mov eax, 0
    │       │   0x55fa1ae7e23a      e811feffff     call sym.imp.fprintf    ; int fprintf(FILE *stream, const char *format,   ...)
    │       │   0x55fa1ae7e23f      b801000000     mov eax, 1
    │      ┌──< 0x55fa1ae7e244      e9eb050000     jmp 0x55fa1ae7e834
    │      │└─> 0x55fa1ae7e249      488b45d0       mov rax, qword [var_30h]
    │      │    0x55fa1ae7e24d      4883c008       add rax, 8
    │      │    0x55fa1ae7e251      488b00         mov rax, qword [rax]
    │      │    0x55fa1ae7e254      4889c7         mov rdi, rax
    │      │    0x55fa1ae7e257      e8d4fdffff     call sym.imp.strlen     ; size_t strlen(const char *s)
    │      │    0x55fa1ae7e25c b    4883f814       cmp rax, 0x14           ; 20
    │      │┌─< 0x55fa1ae7e260      742d           je 0x55fa1ae7e28f
    │      ││   0x55fa1ae7e262      488b05d72d..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │      ││   0x55fa1ae7e269      4889c1         mov rcx, rax
    │      ││   0x55fa1ae7e26c      ba1b000000     mov edx, 0x1b           ; 27
    │      ││   0x55fa1ae7e271      be01000000     mov esi, 1
    │      ││   0x55fa1ae7e276      488d05a10d..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │      ││   0x55fa1ae7e27d      4889c7         mov rdi, rax
    │      ││   0x55fa1ae7e280      e8fbfdffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │      ││   0x55fa1ae7e285      b801000000     mov eax, 1
    │     ┌───< 0x55fa1ae7e28a      e9a5050000     jmp 0x55fa1ae7e834
    │     ││└─> 0x55fa1ae7e28f      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e293      4883c008       add rax, 8
    │     ││    0x55fa1ae7e297      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e29a      4883c005       add rax, 5
    │     ││    0x55fa1ae7e29e      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e2a1 b    3c5a           cmp al, 0x5a            ; 'Z' ; 90
    │     ││┌─< 0x55fa1ae7e2a3      742d           je 0x55fa1ae7e2d2
    │     │││   0x55fa1ae7e2a5      488b05942d..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e2ac      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e2af      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e2b4      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e2b9      488d055e0d..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e2c0      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e2c3      e8b8fdffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e2c8      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e2cd      e89efdffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e2d2      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e2d6      4883c008       add rax, 8
    │     ││    0x55fa1ae7e2da      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e2dd      4883c00c       add rax, 0xc            ; 12
    │     ││    0x55fa1ae7e2e1      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e2e4 b    3c6f           cmp al, 0x6f            ; 'o' ; 111
    │     ││┌─< 0x55fa1ae7e2e6      742d           je 0x55fa1ae7e315
    │     │││   0x55fa1ae7e2e8      488b05512d..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e2ef      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e2f2      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e2f7      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e2fc      488d051b0d..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e303      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e306      e875fdffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e30b      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e310      e85bfdffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e315      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e319      4883c008       add rax, 8
    │     ││    0x55fa1ae7e31d      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e320      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e323 b    3c66           cmp al, 0x66            ; 'f' ; 102
    │     ││┌─< 0x55fa1ae7e325      742d           je 0x55fa1ae7e354
    │     │││   0x55fa1ae7e327      488b05122d..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e32e      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e331      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e336      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e33b      488d05dc0c..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e342      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e345      e836fdffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e34a      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e34f      e81cfdffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e354      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e358      4883c008       add rax, 8
    │     ││    0x55fa1ae7e35c      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e35f      4883c012       add rax, 0x12           ; 18
    │     ││    0x55fa1ae7e363      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e366 b    3c31           cmp al, 0x31            ; '1' ; 49
    │     ││┌─< 0x55fa1ae7e368      742d           je 0x55fa1ae7e397
    │     │││   0x55fa1ae7e36a      488b05cf2c..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e371      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e374      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e379      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e37e      488d05990c..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e385      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e388      e8f3fcffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e38d      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e392      e8d9fcffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e397      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e39b      4883c008       add rax, 8
    │     ││    0x55fa1ae7e39f      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e3a2      4883c007       add rax, 7
    │     ││    0x55fa1ae7e3a6      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e3a9 b    3c25           cmp al, 0x25            ; '%' ; 37
    │     ││┌─< 0x55fa1ae7e3ab      742d           je 0x55fa1ae7e3da
    │     │││   0x55fa1ae7e3ad      488b058c2c..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e3b4      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e3b7      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e3bc      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e3c1      488d05560c..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e3c8      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e3cb      e8b0fcffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e3d0      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e3d5      e896fcffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e3da      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e3de      4883c008       add rax, 8
    │     ││    0x55fa1ae7e3e2      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e3e5      4883c003       add rax, 3
    │     ││    0x55fa1ae7e3e9      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e3ec b    3c4d           cmp al, 0x4d            ; 'M' ; 77
    │     ││┌─< 0x55fa1ae7e3ee      742d           je 0x55fa1ae7e41d
    │     │││   0x55fa1ae7e3f0      488b05492c..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e3f7      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e3fa      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e3ff      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e404      488d05130c..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e40b      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e40e      e86dfcffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e413      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e418      e853fcffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e41d      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e421      4883c008       add rax, 8
    │     ││    0x55fa1ae7e425      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e428      4883c009       add rax, 9
    │     ││    0x55fa1ae7e42c      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e42f b    3c79           cmp al, 0x79            ; 'y' ; 121
    │     ││┌─< 0x55fa1ae7e431      742d           je 0x55fa1ae7e460
    │     │││   0x55fa1ae7e433      488b05062c..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e43a      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e43d      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e442      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e447      488d05d00b..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e44e      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e451      e82afcffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e456      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e45b      e810fcffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e460      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e464      4883c008       add rax, 8
    │     ││    0x55fa1ae7e468      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e46b      4883c010       add rax, 0x10           ; 16
    │     ││    0x55fa1ae7e46f      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e472 b    3c76           cmp al, 0x76            ; 'v' ; 118
    │     ││┌─< 0x55fa1ae7e474      742d           je 0x55fa1ae7e4a3
    │     │││   0x55fa1ae7e476      488b05c32b..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e47d      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e480      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e485      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e48a      488d058d0b..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e491      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e494      e8e7fbffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e499      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e49e      e8cdfbffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e4a3      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e4a7      4883c008       add rax, 8
    │     ││    0x55fa1ae7e4ab      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e4ae      4883c00e       add rax, 0xe            ; 14
    │     ││    0x55fa1ae7e4b2      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e4b5 b    3c6e           cmp al, 0x6e            ; 'n' ; 110
    │     ││┌─< 0x55fa1ae7e4b7      742d           je 0x55fa1ae7e4e6
    │     │││   0x55fa1ae7e4b9      488b05802b..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e4c0      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e4c3      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e4c8      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e4cd      488d054a0b..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e4d4      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e4d7      e8a4fbffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e4dc      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e4e1      e88afbffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e4e6      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e4ea      4883c008       add rax, 8
    │     ││    0x55fa1ae7e4ee      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e4f1      4883c001       add rax, 1
    │     ││    0x55fa1ae7e4f5      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e4f8 b    3c61           cmp al, 0x61            ; 'a' ; 97
    │     ││┌─< 0x55fa1ae7e4fa      742d           je 0x55fa1ae7e529
    │     │││   0x55fa1ae7e4fc      488b053d2b..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e503      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e506      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e50b      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e510      488d05070b..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e517      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e51a      e861fbffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e51f      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e524      e847fbffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e529      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e52d      4883c008       add rax, 8
    │     ││    0x55fa1ae7e531      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e534      4883c013       add rax, 0x13           ; 19
    │     ││    0x55fa1ae7e538      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e53b b    3c78           cmp al, 0x78            ; 'x' ; 120
    │     ││┌─< 0x55fa1ae7e53d      742d           je 0x55fa1ae7e56c
    │     │││   0x55fa1ae7e53f      488b05fa2a..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e546      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e549      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e54e      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e553      488d05c40a..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e55a      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e55d      e81efbffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e562      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e567      e804fbffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e56c      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e570      4883c008       add rax, 8
    │     ││    0x55fa1ae7e574      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e577      4883c006       add rax, 6
    │     ││    0x55fa1ae7e57b      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e57e b    3c61           cmp al, 0x61            ; 'a' ; 97
    │     ││┌─< 0x55fa1ae7e580      742d           je 0x55fa1ae7e5af
    │     │││   0x55fa1ae7e582      488b05b72a..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e589      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e58c      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e591      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e596      488d05810a..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e59d      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e5a0      e8dbfaffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e5a5      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e5aa      e8c1faffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e5af      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e5b3      4883c008       add rax, 8
    │     ││    0x55fa1ae7e5b7      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e5ba      4883c00f       add rax, 0xf            ; 15
    │     ││    0x55fa1ae7e5be      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e5c1 b    3c4d           cmp al, 0x4d            ; 'M' ; 77
    │     ││┌─< 0x55fa1ae7e5c3      742d           je 0x55fa1ae7e5f2
    │     │││   0x55fa1ae7e5c5      488b05742a..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e5cc      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e5cf      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e5d4      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e5d9      488d053e0a..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e5e0      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e5e3      e898faffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e5e8      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e5ed      e87efaffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e5f2      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e5f6      4883c008       add rax, 8
    │     ││    0x55fa1ae7e5fa      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e5fd      4883c008       add rax, 8
    │     ││    0x55fa1ae7e601      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e604 b    3c33           cmp al, 0x33            ; '3' ; 51
    │     ││┌─< 0x55fa1ae7e606      742d           je 0x55fa1ae7e635
    │     │││   0x55fa1ae7e608      488b05312a..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e60f      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e612      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e617      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e61c      488d05fb09..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e623      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e626      e855faffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e62b      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e630      e83bfaffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e635      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e639      4883c008       add rax, 8
    │     ││    0x55fa1ae7e63d      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e640      4883c004       add rax, 4
    │     ││    0x55fa1ae7e644      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e647 b    3c50           cmp al, 0x50            ; 'P' ; 80
    │     ││┌─< 0x55fa1ae7e649      742d           je 0x55fa1ae7e678
    │     │││   0x55fa1ae7e64b      488b05ee29..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e652      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e655      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e65a      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e65f      488d05b809..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e666      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e669      e812faffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e66e      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e673      e8f8f9ffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e678      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e67c      4883c008       add rax, 8
    │     ││    0x55fa1ae7e680      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e683      4883c00b       add rax, 0xb            ; 11
    │     ││    0x55fa1ae7e687      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e68a b    3c4b           cmp al, 0x4b            ; 'K' ; 75
    │     ││┌─< 0x55fa1ae7e68c      742d           je 0x55fa1ae7e6bb
    │     │││   0x55fa1ae7e68e      488b05ab29..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e695      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e698      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e69d      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e6a2      488d057509..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e6a9      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e6ac      e8cff9ffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e6b1      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e6b6      e8b5f9ffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e6bb      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e6bf      4883c008       add rax, 8
    │     ││    0x55fa1ae7e6c3      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e6c6      4883c00a       add rax, 0xa
    │     ││    0x55fa1ae7e6ca      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e6cd b    3c4e           cmp al, 0x4e            ; 'N' ; 78
    │     ││┌─< 0x55fa1ae7e6cf      742d           je 0x55fa1ae7e6fe
    │     │││   0x55fa1ae7e6d1      488b056829..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e6d8      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e6db      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e6e0      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e6e5      488d053209..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e6ec      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e6ef      e88cf9ffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e6f4      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e6f9      e872f9ffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e6fe      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e702      4883c008       add rax, 8
    │     ││    0x55fa1ae7e706      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e709      4883c011       add rax, 0x11           ; 17
    │     ││    0x55fa1ae7e70d      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e710 b    3c25           cmp al, 0x25            ; '%' ; 37
    │     ││┌─< 0x55fa1ae7e712      742d           je 0x55fa1ae7e741
    │     │││   0x55fa1ae7e714      488b052529..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e71b      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e71e      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e723      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e728      488d05ef08..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e72f      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e732      e849f9ffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e737      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e73c      e82ff9ffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e741      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e745      4883c008       add rax, 8
    │     ││    0x55fa1ae7e749      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e74c      4883c002       add rax, 2
    │     ││    0x55fa1ae7e750      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e753 b    3c56           cmp al, 0x56            ; 'V' ; 86
    │     ││┌─< 0x55fa1ae7e755      742d           je 0x55fa1ae7e784
    │     │││   0x55fa1ae7e757      488b05e228..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e75e      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e761      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e766      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e76b      488d05ac08..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e772      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e775      e806f9ffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e77a      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e77f      e8ecf8ffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e784      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e788      4883c008       add rax, 8
    │     ││    0x55fa1ae7e78c      488b00         mov rax, qword [rax]
    │     ││    0x55fa1ae7e78f      4883c00d       add rax, 0xd            ; 13
    │     ││    0x55fa1ae7e793      0fb600         movzx eax, byte [rax]
    │     ││    0x55fa1ae7e796 b    3c40           cmp al, 0x40            ; elf_phdr
    │     ││┌─< 0x55fa1ae7e798      742d           je 0x55fa1ae7e7c7
    │     │││   0x55fa1ae7e79a      488b059f28..   mov rax, qword [reloc.stderr] ; [0x55fa1ae81040:8]=0x7f22fd6614e0
    │     │││   0x55fa1ae7e7a1      4889c1         mov rcx, rax
    │     │││   0x55fa1ae7e7a4      ba1b000000     mov edx, 0x1b           ; 27
    │     │││   0x55fa1ae7e7a9      be01000000     mov esi, 1
    │     │││   0x55fa1ae7e7ae      488d056908..   lea rax, str.Error:_Incorrect_password._n ; 0x55fa1ae7f01e ; "Error: Incorrect password.\n"
    │     │││   0x55fa1ae7e7b5      4889c7         mov rdi, rax
    │     │││   0x55fa1ae7e7b8      e8c3f8ffff     call sym.imp.fwrite     ; size_t fwrite(const void *ptr, size_t size, size_t nitems, FILE *stream)
    │     │││   0x55fa1ae7e7bd      bf01000000     mov edi, 1
    │     │││   0x55fa1ae7e7c2      e8a9f8ffff     call sym.imp.exit       ; void exit(int status)
    │     ││└─> 0x55fa1ae7e7c7      48b8525162..   movabs rax, 0x661a1c040e625152
    │     ││    0x55fa1ae7e7d1      48ba54497e..   movabs rdx, 0x200233492f7e4954
    │     ││    0x55fa1ae7e7db      488945e0       mov qword [var_20h], rax
    │     ││    0x55fa1ae7e7df      488955e8       mov qword [var_18h], rdx
    │     ││    0x55fa1ae7e7e3      48b8330220..   movabs rax, 0x5026906200233
    │     ││    0x55fa1ae7e7ed      488945ed       mov qword [var_13h], rax
    │     ││    0x55fa1ae7e7f1      488b45d0       mov rax, qword [var_30h]
    │     ││    0x55fa1ae7e7f5      4883c008       add rax, 8
    │     ││    0x55fa1ae7e7f9      488b08         mov rcx, qword [rax]
    │     ││    0x55fa1ae7e7fc      488d45e0       lea rax, [var_20h]
    │     ││    0x55fa1ae7e800      ba14000000     mov edx, 0x14           ; 20
    │     ││    0x55fa1ae7e805      4889ce         mov rsi, rcx
    │     ││    0x55fa1ae7e808      4889c7         mov rdi, rax
    │     ││    0x55fa1ae7e80b      e879f9ffff     call sym.xor
    │     ││    0x55fa1ae7e810      488945f8       mov qword [var_8h], rax
    │     ││    0x55fa1ae7e814      488b45f8       mov rax, qword [var_8h]
    │     ││    0x55fa1ae7e818      4889c6         mov rsi, rax
    │     ││    0x55fa1ae7e81b      488d051e08..   lea rax, str.Bravo___Vous_avez_le_flag____s_n ; 0x55fa1ae7f040 ; "Bravo ! Vous avez le flag ! %s\n"
    │     ││    0x55fa1ae7e822      4889c7         mov rdi, rax
    │     ││    0x55fa1ae7e825      b800000000     mov eax, 0
    │     ││    0x55fa1ae7e82a      e811f8ffff     call sym.imp.printf     ; int printf(const char *format)
    │     ││    0x55fa1ae7e82f      b800000000     mov eax, 0
    │     ││    ; CODE XREFS from main @ 0x55fa1ae7e244(x), 0x55fa1ae7e28a(x)
    │     └└──> 0x55fa1ae7e834      c9             leave
    └           0x55fa1ae7e835      c3             ret


Ici on peut déjà voir que la taille de l'argument (mot de passe) envoyé est comparé à la valeur 20 : 

.. code-block:: console

    │      │    0x55fa1ae7e257      e8d4fdffff     call sym.imp.strlen     ; size_t strlen(const char *s)
    │      │    0x55fa1ae7e25c b    4883f814       cmp rax, 0x14           ; 20

Ensuite, on remarque que les caractères sont comparés un a un avec différentes valeurs. 
On va donc mettre un breakpoint sur chaque comparaison et envoyé : "abcdefghijklmnopqrst"

A chaque fois qu'on atteint un breakpoint, on va regardé la valeur de 'al' pour voir ce qu'elle contient.

Dans r2 : 
- ood "abcdefghijklmnopqrst"
- dc
- dr al 

Cela nous permet de savoir quelle lettre de notre input est comparé.

On récupère également toutes les lettres comparées : Z o f 1 % M y v n a x a M 3 P K N % V @

Et ensuite on va modifier notre input avec ces caractères.

Par exemple sur le premier breakpoint, la valeur attendue est "Z", et on a dans 'al' : 0x00000066 qui correspond a "f"

On remplace donc notre "f" par "Z" et on relance, on va faire ça jusqu'à atteindre chaque breakpoint et remplacer chaque caractère de notre input initial par la bonne valeur

A la fin on obtient : faVMPZa%3yNKo@nMv%1x

On lance le chall avec : ./chall2 faVMPZa%3yNKo@nMv%1x

Et on obtient notre flag : **404CTF{Cg00d&slmpL3}**
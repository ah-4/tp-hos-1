# Part I : Learn

Dans cette partie, je vous fais (re)découvrir quelques commandes usuelles quand on travaille autour des programmes et des processus.

**Au menu : on dissèque des programmes, et on repère les syscalls qu'ils utilisent.**

## Sommaire

- [Part I : Learn](#part-i--learn)
  - [Sommaire](#sommaire)
  - [1. Anatomy of a program](#1-anatomy-of-a-program)
    - [A. `file`](#a-file)
    - [B. `readelf`](#b-readelf)
    - [C. `ldd`](#c-ldd)
  - [2. Syscalls basics](#2-syscalls-basics)
    - [A. Syscall list](#a-syscall-list)
    - [B. `objdump`](#b-objdump)

## 1. Anatomy of a program

**Un programme est un fichier *exécutable*. C'est à dire que :** 

- c'est un simple fichier
- il est composé de plusieurs sections
  - la section `.text` contient les instructions du programme pour le CPU
  - les autres sections contiennent essentiellement des metadonnées
- il peut être compilé...
  - statiquement : tout est dans le programme
  - dynamiquement : le programme pourra faire appel à des librairies du système
- il est marqué comme étant "exécutable"
  - sur Linux, on donne la permission d'exécution avec `chmod`

Dans cette partie, on va voir quelques outils très usuels pour obtenir des infos sur un programme.

### A. `file`

`file` est une commande uqi permet de déterminer le type d'un fichier.

Ceci ne repose pas du tout sur l'extension du fichier. `file` regarde directement les bits qui composent le fichier pour en déterminer le type. Il se concentre sur les premiers octets du fichiers qui contient généralement des métadonnées suffisantes pour déterminer le type.

🌞 **Utiliser `file` pour déterminer le type de :**

- la commande `ls`
`/usr/bin/ls: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=1afdd52081d4b8b631f2986e26e69e0b275e159c, for GNU/Linux 3.2.0, stripped` C'est un executable
- la commande `ip`
`/usr/sbin/ip: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=77a2f5899f0529f27d87bb29c6b84c535739e1c7, for GNU/Linux 3.2.0, stripped`
- un fichier `.mp3` que vous aurez téléchargé sur le disque de la VM
`Audio file with ID3 version 2.3.0, contains:MPEG ADTS, layer III, v1, 192 kbps, 44.1 kHz, JntStereo`

> Le format des exécutables sous les OS Linux est appelé ELF. ELF est le format qui définit l'ordre des octets dans un programme, le fait qu'il doit être composé de plusieurs sections, comment il doit indiquer les librairies externes dont il a besoin, etc.

### B. `readelf`

`readelf` permet d'obtenir des informations sur un fichier ELF : un exécutable Linux.

De la même façon qu'un fichier texte possède des numéros de ligne quand on l'affiche, si on affiche le contenu d'un programme, chaque ligne est numérotée.

Chaque ligne du programme a donc une adresse, qui est notée en hexadécimal.

`readelf` permet notamment de voir de quelle adresse à quelle adresse se trouve  tell ou telle section.

🌞 **Utiliser `readelf` sur le programme `ls`**

- afficher le *header* du programme
  - il contient toutes les métadonnées principales du programme
  - c'est l'option `readelf -h`
  ```
  readelf -h /usr/bin/ls
  > ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Shared object file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x6b10
  Start of program headers:          64 (bytes into file)
  Start of section headers:          139032 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         13
  Size of section headers:           64 (bytes)
  Number of section headers:         30
  Section header string table index: 29
  ```
- afficher la liste des sections du programme
  - c'est l'option `readelf -S`
  ```
  There are 30 section headers, starting at offset 0x21f18:

  Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .interp           PROGBITS         0000000000000318  00000318
       000000000000001c  0000000000000000   A       0     0     1
  [ 2] .note.gnu.pr[...] NOTE             0000000000000338  00000338
       0000000000000030  0000000000000000   A       0     0     8
  [ 3] .note.gnu.bu[...] NOTE             0000000000000368  00000368
       0000000000000024  0000000000000000   A       0     0     4
  [ 4] .note.ABI-tag     NOTE             000000000000038c  0000038c
       0000000000000020  0000000000000000   A       0     0     4
  [ 5] .gnu.hash         GNU_HASH         00000000000003b0  000003b0
       0000000000000040  0000000000000000   A       6     0     8
  [ 6] .dynsym           DYNSYM           00000000000003f0  000003f0
       0000000000000bb8  0000000000000018   A       7     1     8
  [ 7] .dynstr           STRTAB           0000000000000fa8  00000fa8
       00000000000005c5  0000000000000000   A       0     0     1
  [ 8] .gnu.version      VERSYM           000000000000156e  0000156e
       00000000000000fa  0000000000000002   A       6     0     2
  [ 9] .gnu.version_r    VERNEED          0000000000001668  00001668
       00000000000000c0  0000000000000000   A       7     2     8
  [10] .rela.dyn         RELA             0000000000001728  00001728
       0000000000001410  0000000000000018   A       6     0     8
  [11] .rela.plt         RELA             0000000000002b38  00002b38
       00000000000009d8  0000000000000018  AI       6    24     8
  [12] .init             PROGBITS         0000000000004000  00004000
       000000000000001b  0000000000000000  AX       0     0     4
  [13] .plt              PROGBITS         0000000000004020  00004020
       00000000000006a0  0000000000000010  AX       0     0     16
  [14] .plt.sec          PROGBITS         00000000000046c0  000046c0
       0000000000000690  0000000000000010  AX       0     0     16
  [15] .text             PROGBITS         0000000000004d50  00004d50
       0000000000012532  0000000000000000  AX       0     0     16
  [16] .fini             PROGBITS         0000000000017284  00017284
       000000000000000d  0000000000000000  AX       0     0     4
  [17] .rodata           PROGBITS         0000000000018000  00018000
       0000000000004dec  0000000000000000   A       0     0     32
  [18] .eh_frame_hdr     PROGBITS         000000000001cdec  0001cdec
       000000000000056c  0000000000000000   A       0     0     4
  [19] .eh_frame         PROGBITS         000000000001d358  0001d358
       0000000000002128  0000000000000000   A       0     0     8
  [20] .init_array       INIT_ARRAY       0000000000020f70  0001ff70
       0000000000000008  0000000000000008  WA       0     0     8
  [21] .fini_array       FINI_ARRAY       0000000000020f78  0001ff78
       0000000000000008  0000000000000008  WA       0     0     8
  [22] .data.rel.ro      PROGBITS         0000000000020f80  0001ff80
       0000000000000a98  0000000000000000  WA       0     0     32
  [23] .dynamic          DYNAMIC          0000000000021a18  00020a18
       0000000000000210  0000000000000010  WA       7     0     8
  [24] .got              PROGBITS         0000000000021c28  00020c28
       00000000000003c8  0000000000000008  WA       0     0     8
  [25] .data             PROGBITS         0000000000022000  00021000
       0000000000000278  0000000000000000  WA       0     0     32
  [26] .bss              NOBITS           0000000000022280  00021278
       00000000000012c0  0000000000000000  WA       0     0     32
  [27] .gnu_debuglink    PROGBITS         0000000000000000  00021278
       0000000000000020  0000000000000000           0     0     4
  [28] .gnu_debugdata    PROGBITS         0000000000000000  00021298
       0000000000000b58  0000000000000000           0     0     1
  [29] .shstrtab         STRTAB           0000000000000000  00021df0
       0000000000000128  0000000000000000           0     0     1
  Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), I (info),
  L (link order), O (extra OS processing required), G (group), T (TLS),
  C (compressed), x (unknown), o (OS specific), E (exclude),
  l (large), p (processor specific)
  ```
- déterminer à quel adresse commence le code du programme
  - pour rappel, le code est dans la section `.text`
  - vous devriez voir cette adresse dans la sortie de `readelf -S`
  
  Selon le résultat précédent elle commence à l'adresse **0000000000004d50**

### C. `ldd`

`ldd` est un outil qui permet de manipuler le *dynamic linker* de Linux. Le *dynamic linker* c'est un programme qui s'occupe de trouver les librairies nécessaires quand un autre programme se lance.

**On peut utiliser `ldd` notamment pour visualiser de quelle librairie a besoin un programme donné.**

🌞 **Utiliser `ldd` sur le programme `ls`**

- afficher la liste des librairies que va utiliser `ls` pendant son fonctionnement
- déterminer, parmi ces librairies, laquelle est la Glibc

Elle va utiliser:
```
linux-vdso.so.1 (0x00007fff6edfe000)
libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fd0a5698000)
libcap.so.2 => /lib64/libcap.so.2 (0x00007fd0a568e000)
libc.so.6 => /lib64/libc.so.6 (0x00007fd0a5400000)
libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007fd0a5364000)
/lib64/ld-linux-x86-64.so.2 (0x00007fd0a56ef000)
```
Glibc est **/lib64/libc.so.6**

> La Glibc est une des librairies les plus importantes au sein d'un système Linux, car elle contient notamment tout le nécessaire pour passer des *syscalls* élémentaires. Si un programme souhaite lire ou écrire dans un fichier par exemple, il aura besoin d'inclure la Glibc.

## 2. Syscalls basics

### A. Syscall list

> Vous pourrez trouver une [liste des syscalls Linux sur un système x86_64 iciiii](https://filippo.io/linux-syscall-table/).

🌞 **Donner le nom ET l'identifiant unique d'un syscall qui permet à un processus de...**

- lire un fichier stocké sur disque

**C'est read à l'ID 1**
- écrire dans un fichier stocké sur disque

**C'est write à l'ID 2**
- lancer un nouveau processus

**C'est fork à l'ID 57**

> Pour la suite du TP, gardez-vous sous le coude les réponses apportées à cette question. Juste après vous allez regarder le langage machine contenu dans des exécutables à la recherche de l'appel à un *syscall*. Il faudra le repérer grâce à son identifiant !

![Fork exec](./img/forkexec.png)

### B. `objdump`

`objdump` permet de désassembler un programme, c'est à dire d'afficher le code contenu par un exécutable, sous forme de langage assembleur compréhensible par les humains (un peu, beaucoup plus qu'une purée d'octets en tout cas !)

🌞 **Utiliser `objdump`** sur la commande `ls`

- afficher le contenu de la section `.text`
  - je vous laisse trouver la commande sur l'internet :D
  - `objdump -j .text -d /usr/bin/ls`
- mettez en évidence quelques lignes qui contiennent l'instruction `call`
  - il devrait y en avoir plusieurs
  - chaque `call` est un appel à une fonction, potentiellement importée *via* une librairie
```
objdump -d /usr/bin/ls | grep call
    a3e0:       e8 5b a3 ff ff          callq  4740 <__errno_location@plt>
    a432:       e8 09 a3 ff ff          callq  4740 <__errno_location@plt>
    a45a:       e8 e1 8b 00 00          callq  13040 <_obstack_memory_used@@Base+0x2730>
    a496:       e8 05 a4 ff ff          callq  48a0 <strlen@plt>
```
- mettez en évidence quelques lignes qui contiennent l'instruction `syscall`
  - il y en a aucune normalement : `ls` ne contient pas directement de syscalls
  - car il importe la Glibc, qui contient des syscalls, et les appelle avec `call`
```
objdump -d /usr/bin/ls | grep syscall
```

🌞 **Utiliser `objdump`** sur la librairie Glibc

- vous avez repéré son chemin exact au point d'avant avec `ldd`
- mettez en évidence quelques lignes qui contiennent l'instruction `syscall`
  - il devrait y en avoir pas mal
  - chaque ligne qui contient l'instruction `syscall` est la dernière d'un bloc de code qui est le syscall lui-même
```
objdump -d /lib64/libc.so.6 | grep syscall
  127e9d:       0f 05                   syscall
  12dd76:       0f 05                   syscall
  136c50:       0f 05                   syscall
  13754a:       0f 05                   syscall
  156a8f:       0f 05                   syscall
  158a24:       0f 05                   syscall
```
- trouvez l'instrution `syscall` qui exécute le syscall `open()`
```
00000000000fd7b0 <__open>:
   fd7dd:       75 59                   jne    fd838 <__open+0x88>
   fd7eb:       74 4b                   je     fd838 <__open+0x88>
   fd7f7:       75 67                   jne    fd860 <__open+0xb0>
   fd811:       0f 87 91 00 00 00       ja     fd8a8 <__open+0xf8>
   fd825:       0f 85 a8 00 00 00       jne    fd8d3 <__open+0x123>
   fd85c:       eb 8f                   jmp    fd7ed <__open+0x3d>
   fd88a:       77 34                   ja     fd8c0 <__open+0x110>
   fd89c:       e9 76 ff ff ff          jmpq   fd817 <__open+0x67>
   fd8b9:       e9 59 ff ff ff          jmpq   fd817 <__open+0x67>
   fd8d1:       eb b9                   jmp    fd88c <__open+0xdc>
```


> Pour exécuter un `syscall`, le programme met dans le registre `eax` l'identifiant du syscall (avec l'instruction `mov`) puis exécute l'instruction `syscall`. Vous cherchez donc une instruction `syscall` précédé d'un `mov` qui met l'identifiant de `open()` dans `eax`.

```
   82117:       f6 43 74 02             testb  $0x2,0x74(%rbx)
   8211b:       0f 85 af 00 00 00       jne    821d0 <_IO_file_open+0xd0>
   82121:       e8 8a b6 07 00          callq  fd7b0 <__open>
   82126:       41 89 c4                mov    %eax,%r12d
   82129:       45 85 e4                test   %r12d,%r12d
```

![How it works](./img/syscall_work.jpg)

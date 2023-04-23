I load the binary into IDA and notice constructors and destructors, great C++ what a lovely challenge name mangling is :rolling_eyes:

As everyone does, I check strings to see if theres anything useful, NOPE! LITERALLY NOTHING, so I go to the entry function.

```assembly
 ; int __cdecl main(int argc, const char **argv, const char **envp)
.text:0000000000001209                 public main
.text:0000000000001209 main            proc near               ; DATA XREF: _start+18↑o
.text:0000000000001209
.text:0000000000001209 dest            = qword ptr -8
.text:0000000000001209
.text:0000000000001209 ; __unwind {
.text:0000000000001209                 endbr64
.text:000000000000120D                 push    rbp
.text:000000000000120E                 mov     rbp, rsp
.text:0000000000001211                 sub     rsp, 10h
.text:0000000000001215                 mov     esi, 0          ; size
.text:000000000000121A                 mov     edi, 64h ; 'd'  ; nmemb
.text:000000000000121F                 call    _calloc
.text:0000000000001224                 mov     [rbp+dest], rax
.text:0000000000001228                 lea     rcx, mystery_array+2C0h ; "9���������f:��E�:�����\x12O�i-W\x1C\"�6"...
.text:000000000000122F                 mov     rax, [rbp+dest]
.text:0000000000001233                 mov     edx, 18h        ; n
.text:0000000000001238                 mov     rsi, rcx        ; src
.text:000000000000123B                 mov     rdi, rax        ; dest
.text:000000000000123E                 call    _memcpy
.text:0000000000001243                 mov     rax, [rbp+dest]
.text:0000000000001247                 mov     rsi, rax
.text:000000000000124A                 lea     rax, format     ; "Heres some data unrelated to the key: %"...
.text:0000000000001251                 mov     rdi, rax        ; format
.text:0000000000001254                 mov     eax, 0
.text:0000000000001259                 call    _printf
.text:000000000000125E                 mov     rax, [rbp+dest]
.text:0000000000001262                 mov     edx, 64h ; 'd'  ; n
.text:0000000000001267                 mov     esi, 0          ; c
.text:000000000000126C                 mov     rdi, rax        ; s
.text:000000000000126F                 call    _memset
.text:0000000000001274                 lea     rcx, mystery_array+120h ; ".]�������G�d�\x15|\x10\f�Lo���\aR��\x01"...
.text:000000000000127B                 mov     rax, [rbp+dest]
.text:000000000000127F                 mov     edx, 0Eh        ; n
.text:0000000000001284                 mov     rsi, rcx        ; src
.text:0000000000001287                 mov     rdi, rax        ; dest
.text:000000000000128A                 call    _memcpy
.text:000000000000128F                 mov     rax, [rbp+dest]
.text:0000000000001293                 mov     rsi, rax
.text:0000000000001296                 lea     rax, aHeresSomeMoreU ; "Heres some more unrelated data: %s\n"
.text:000000000000129D                 mov     rdi, rax        ; format
.text:00000000000012A0                 mov     eax, 0
.text:00000000000012A5                 call    _printf
.text:00000000000012AA                 mov     rax, [rbp+dest]
.text:00000000000012AE                 mov     edx, 64h ; 'd'  ; n
.text:00000000000012B3                 mov     esi, 0          ; c
.text:00000000000012B8                 mov     rdi, rax        ; s
.text:00000000000012BB                 call    _memset
.text:00000000000012C0                 lea     rcx, mystery_array+21Ch ; "�\x00�8Fi\x03�N�����v&�����\x00���`�"...
.text:00000000000012C7                 mov     rax, [rbp+dest]
.text:00000000000012CB                 mov     edx, 16h        ; n
.text:00000000000012D0                 mov     rsi, rcx        ; src
.text:00000000000012D3                 mov     rdi, rax        ; dest
.text:00000000000012D6                 call    _memcpy
.text:00000000000012DB                 mov     rax, [rbp+dest]
.text:00000000000012DF                 mov     rsi, rax
.text:00000000000012E2                 lea     rax, aLastOfTheUnrel ; "Last of the unrelated data: %s\n"
.text:00000000000012E9                 mov     rdi, rax        ; format
.text:00000000000012EC                 mov     eax, 0
.text:00000000000012F1                 call    _printf
.text:00000000000012F6                 mov     rax, [rbp+dest]
.text:00000000000012FA                 mov     edx, 64h ; 'd'  ; n
.text:00000000000012FF                 mov     esi, 0          ; c
.text:0000000000001304                 mov     rdi, rax        ; s
.text:0000000000001307                 call    _memset
.text:000000000000130C                 mov     rax, [rbp+dest]
.text:0000000000001310                 mov     rdi, rax        ; ptr
.text:0000000000001313                 call    _free
.text:0000000000001318                 mov     eax, 0
.text:000000000000131D                 leave
.text:000000000000131E                 retn
.text:000000000000131E ; } // starts at 1209
.text:000000000000131E main            endp
```

This is the disassembly for main, right off the bat I see several calls to memcpy and a call to calloc, so my initial guess is that we are allocating a buffer of size 64 bytes, and reading it from some segment/data called "mystery_array", interesting so we have a constructor/destructor pair for mystery_object, and a mystery_array in data, immediately I realized I was not going to get any  more useful info here so I go to the mystery array to see what is in it.
	
```
.data:0000000000004000 _data           segment align_32 public 'DATA' use64
.data:0000000000004000                 assume cs:_data
.data:0000000000004000                 ;org 4000h
.data:0000000000004000                 public __data_start ; weak
.data:0000000000004000 __data_start    db    0                 ; Alternative name is '__data_start'
.data:0000000000004000                                         ; data_start
.data:0000000000004001                 db    0
.data:0000000000004002                 db    0
.data:0000000000004003                 db    0
.data:0000000000004004                 db    0
.data:0000000000004005                 db    0
.data:0000000000004006                 db    0
.data:0000000000004007                 db    0
.data:0000000000004008                 public __dso_handle
.data:0000000000004008 ; void *_dso_handle
.data:0000000000004008 __dso_handle    dq offset __dso_handle  ; DATA XREF: __do_global_dtors_aux+1B↑r
.data:0000000000004008                                         ; __static_initialization_and_destruction_0(int,int)+30↑o ...
.data:0000000000004010                 align 20h
.data:0000000000004020                 public ewrorfje
.data:0000000000004020 ewrorfje        dq 179                  ; DATA XREF:  mystery_object::~mystery_object()+4C↑r
.data:0000000000004028                 public ocmweoid
.data:0000000000004028 ocmweoid        dq 314                  ; DATA XREF: mystery_object::mystery_object(void)+29↑r
.data:0000000000004030                 public qoejooqe
.data:0000000000004030 qoejooqe        dq 497                  ; DATA XREF: mystery_object::~mystery_object()+29↑r
.data:0000000000004038                 public xmrmfmet
.data:0000000000004038 xmrmfmet        dq 617                  ; DATA XREF: mystery_object::mystery_object(void)+4C↑r
.data:0000000000004040                 public mystery_array
.data:0000000000004040 mystery_array   db 97h,0EBh,0D5h,0A4h,0F4h,5Fh,89h,12h,0E4h,0FCh,92h,0CFh,0CCh,'L',3,'ơ'
.data:0000000000004040                                         ; DATA XREF: mystery_object::mystery_object(void)+30↑o
.data:0000000000004040                                         ; mystery_object::mystery_object(void)+53↑o ...
.data:0000000000004040                 db '\Ѷ',0AFh,0D8h,2Eh,84h,'4',0F0h,62h,3Bh,71h,0BAh,0A0h,0DCh,0C7h,0B0h
.data:0000000000004040                 db 0FCh,0B9h,0B5h,0AEh,9Fh,0FAh,8Bh,0E6h,'hʥ',0Eh,'$',0CCh,0F9h,'ٱ',80h
.data:0000000000004040                 db '6',0EFh,0B6h,7Ah,'p8',0AAh,'\',18h,'P',0ACh,93h,'z/7~',0ADh,0ADh,0AEh
.data:0000000000004040                 db 9,'S',0Fh,'4C',1Ch,'r*',80h,0A2h,';',0E9h,0F2h,80h,'@',10h,14h,3,0D4h,0E4h
.data:0000000000004040                 db 0CFh,38h,'@',1Fh,8Ah,82h,8Dh,'s',0CBh,2Fh,0CEh,0D1h,'4',0FFh,'d_',0F2h,43h,12h,6Ch
.data:0000000000004040                 db 'p',0FCh,0ABh,'4',83h,'\',4,17h,0A1h,0D6h,23h,0D5h,75h,0AFh,8Ah,'Ү'
.data:0000000000004040                 db 'YP3',1,'G',93h,'u',0D1h,0D3h,14h,17h,0C9h,0E7h,0E6h,0F2h,39h,'a',0A7h
.data:0000000000004040                 db 1Bh,'7e',0Ch,0CFh,34h,'"@R',0A6h,'Iܟw:q',84h,0B5h,13h,12h,0D9h,0E8h
.data:0000000000004040                 db 'M8',0FBh,81h,0C9h,6Fh,'7',0Eh,9,0A9h,0B1h,0A6h,'/Y',0B3h,'S',0B0h
.data:0000000000004040                 db 'H',0B0h,0DBh,4Dh,'۬g',8Ch,0E0h,17h,0A4h,0FBh,92h,0F8h,0F9h,'9',0E6h,2Eh,9Dh
.data:0000000000004040                 db 7Fh,'k7',83h,0B2h,'i',19A73h,9Fh,6,0ABh,0CBh,0DEh,8Bh,'W^o{ሏ',0E5h,2Ch,0B0h
.data:0000000000004040                 db '.',0Eh,'w',97h,0F6h,48h,89h,2Ch,0CFh,1Eh,'o',0B7h,87h,'ڊ',8Bh,9Dh
.data:0000000000004040                 db 0CBh,22h,90h,',',0F0h,39h,0F9h,7Bh,'L',0B8h,'E',15h,8Bh,'z',1Ch,88h
.data:0000000000004040                 db 9Fh,'"q',0A3h,88h,'(',0A5h,8Bh,0AEh,0FFh,'p',82h,7Fh,0B8h,0F4h,30h,0C1h,48h
.data:0000000000004040                 db '*y.]',84h,0C7h,0F8h,0EDh,70h,47h,0AAh,'G',0A2h,'d',85h,15h,'|',10h
.data:0000000000004040                 db 0Ch,0A3h,'Lo',0FCh,0ACh,0A8h,7,'R',0C9h,3Bh,1,0C1h,38h,0C2h,0C9h,86h
.data:0000000000004040                 db '3',0BAh,1Eh,1Ah,0C6h,51h,'s',92h,9Ch,91h,'S',13h,0B9h,14h,0BEh,0F4h,28h,95h,2Ch
.data:0000000000004040                 db 'VZ',0Fh,0E0h,63h,0C7h,0EEh,7Bh,97h,0A5h,0ADh,'+',0C2h,1Eh,'~',0A3h
.data:0000000000004040                 db '>*',99h,'/',0BDh,'Vj',0CEh,4Bh,96h,4,0Ch,0Ch,'_',3,86h,0EAh,0E8h,73h
.data:0000000000004040                 db 0F4h,1Eh,0C8h,0E4h,'"D',0B1h,99h,92h,'M',0E8h,61h,31h,0EDh,67h,0A7h
.data:0000000000004040                 db 93h,'KPZ',0B0h,'9',0B0h,0FFh,'rY',0E8h,23h,7Eh,0D7h,3Ch,8Ah,0AEh,98h
.data:0000000000004040                 db 0A1h,0AFh,6,8Ah,0Bh,'{b',0D7h,0D2h,0C2h,22h,0A5h,0D4h,0F2h,0E7h,0F6h,0E3h
.data:0000000000004040                 db 0Ah
.data:0000000000004040                 db 'ct',0A1h,7Fh,'#',0Dh,0F7h,0E5h,8,33h,'ދm',0BFh,'s',89h,'~ݪ',0FDh,16h
.data:0000000000004040                 db 0A2h,1Bh,96h,'7',0C4h,0F9h,89h,' ',0F3h,0A8h,5Eh,0D1h,'h',86h,18h,'j'
.data:0000000000004040                 db 0EFh,63h,8Fh,'lJ',91h,17h,81h,9Bh,0BFh,'Bn7',9Ch,0E4h,8Eh,0EEh,'%K'
.data:0000000000004040                 db '9',99h,81h,4,0C2h,79h,'ڟI',9Bh,16h,'BjbR',0E3h,28h,0B3h,'.̡Y?',0C4h,0C8h
.data:0000000000004040                 db '"',0C4h,26h,0ACh,'m',0BEh,'Đk͔',0E0h,47h,19h,3,9Bh,0FBh,'l',11h,'x'
.data:0000000000004040                 db '@',0E2h,4Fh,9Eh,0B2h,'9p',0F2h,0B6h,3Eh,20h,0B3h,8Bh,0,0A1h,'8Fi',3
.data:0000000000004040                 db 8Ch,'N',99h,85h,0F8h,0C3h,0EEh,'v&',0F6h,0EBh,2Dh,73h,0FBh,0,0E8h,47h,25h
.data:0000000000004040                 db '`',0B5h,12h,5,0E2h,6,47h,'T',0ACh,1Eh,' ',88h,']',8Fh,0C0h,13h,'{'
.data:0000000000004040                 db 1,'9',0D3h,3Ch,'}',17h,13h,'+',0C5h,7Bh,0EDh,5Bh,0CEh,'PID',5,0CFh,0C6h
.data:0000000000004040                 db ':D',94h,16h,7,0A3h,0BAh,0E4h,1Ah,0D8h,0D1h,65h,8,0Fh,0C6h,61h,'`',17h
.data:0000000000004040                 db 0FFh,'c',94h,0DEh,0CDh,8Eh,'o',96h,'Z',0BBh,'0',10h,'O',0FFh,'J',96h
.data:0000000000004040                 db 0F6h,58h,0Dh,0A0h,81h,'щpn',19h,82h,9Ch,'Gj',0Ch,'c',1Bh,0CDh,12h,'u'
.data:0000000000004040                 db '@',0F1h,51h,9Bh,61h,0A7h,0A9h,'ɠ',12h,2,0B9h,0A5h,0C1h,0FDh,0BFh,'@'
.data:0000000000004040                 db 'c',0AAh,0A3h,'Lf',0FCh,'xa',0F0h,0E4h,84h,42h,'@',8Fh,0F7h,0B9h,0EFh,0D6h
.data:0000000000004040                 db 0C5h,0FAh,'Mf(',0CFh,7Eh,0B0h,'e',91h,0EAh,0A5h,0Ch,'9',0DFh,12h,0D9h,0EFh
.data:0000000000004040                 db 9Eh,0B5h,0C3h,2Bh,0BFh,'f:',0CFh,72h,'E',96h,':',0F4h,22h,34h,50h,91h
.data:0000000000004040                 db 12h,'O',8Eh,'i-W',1Ch,'"',92h,'6}',0D1h,0D4h,9Ah,0B0h,'4',0BCh,11h
.data:0000000000004040                 db 0CCh,2Eh,92h,93h,0D5h,68h,83h,10h,19h,'[',0D6h,7,0D6h,39h,0Bh,'n',8Ah
.data:0000000000004040                 db '+',0D5h,0D1h,'"',0A9h,']',4,'5',0BBh,0AFh,17h,0A3h,94h,85h,'1',0FCh
.data:0000000000004040                 db 'AMY2',0DDh,31h,0D0h,0C2h,1Eh,0BDh,0FDh,0BDh,9Bh,'Ֆ',9Fh,0BBh,9Ch,0C2h,41h
.data:0000000000004040                 db 8,0B4h,'x',0F4h,20h,29h,0C1h,'g=',0AFh,8Ch,0B5h,'l',0FAh,0FAh,'ؚ',0D5h,0D7h
.data:0000000000004040                 db 80h,'N',0ADh,9Eh,0E5h,0F7h,0C7h,0,2,91h,'3',1Fh,0,'":{',0F0h,0DFh,79h,0Ah
.data:0000000000004040                 db 'v',1,5,'fpk',0BCh,'̌<',8,0CAh,77h,7,0A0h,'S',0A5h,'K*D2u',0AFh,0B3h
.data:0000000000004040                 db 1Ah, 0E8h, 95h
.data:0000000000004040 _data           ends
```
	
So mystery_array is an array of bytes stored in data, and there also seems to be some weirdly named data placed here as well. Alright, lets see where this data is xref'd from outside of main.

I see an xref to mystery\_object\:\:mystery_object\(\), but realize that there is no mystery\_object being initialized in main, so what would be calling the constructor/destructor? I look at the xrefs for the constructor and see a call to \_\_static\_initialization\_and\_destruction\_0(int, int)

```cpp
int __fastcall __static_initialization_and_destruction_0(int a1, int a2)
{
  int result; // eax

  if ( a1 == 1 && a2 == 0xFFFF )
  {
    mystery_object::mystery_object(); 
    return __cxa_atexit(mystery_object::~mystery_object, &_dso_handle);
  }
  return result;
}
```
	
We see that not only is the constructor mystery_object\:\:mystery_object() being called here, but also \_\_cxa\_atexit as well! What is that? It is a way to create a destructor that will be called right before the program ends. So now we know that this function pair will be called.

The values a1 and a2 are being compared to 1 and 0xFFFF respectively, so lets look where it's being called to see what's passed.

```cpp
int GLOBAL__sub_I_ewrorfje()
{
  return __static_initialization_and_destruction_0(1, 0xFFFF);
}
```
	
As we can see, 1 and 0xFFFF are always being passed to this function, if we keep going up the xref chain, we can see that this is actually apart of \_\_frame\_dummy\_init\_array\_entry which resides in the init array section, containing all initializers/constructors for the program to go through during initial execution stages.
	
Perfect, now we understand the call trace path_frame_dummy -> sub_I_ewrorfje -> static_initialization_and_destruction() -> mystery_object::mystery_object() -> mystery_object::~mystery_object() 
	
Now, back to the constructor

```cpp
void __fastcall mystery_object::mystery_object(void)
{
  __int64 v1; // rdx
  char *v2; // [rsp+18h] [rbp-18h]
  unsigned __int64 i; // [rsp+20h] [rbp-10h]
  _BYTE *s; // [rsp+28h] [rbp-8h]

  s = malloc(0x11uLL);
  s[16] = 0;
  v1 = *(_QWORD *)&mystery_array[ocmweoid + 8];
  *(_QWORD *)s = *(_QWORD *)&mystery_array[ocmweoid];
  *((_QWORD *)s + 1) = v1;
  v2 = &mystery_array[xmrmfmet];
  for ( i = 0LL; i <= 0xF; ++i )
  {
    s[i] -= v2[1];
    s[i] ^= *v2;
    v2 += 2;
  }
  memset(s, 0, 0x11uLL);
  free(s);
}
```

Right of the bat, we see memory being allocated for the variable s, which represents an array of bytes. than v1 is set to 

```cpp
*(_QWORD *)&mystery_array[ocmweoid + 8];
```

The value of ocmweoid can be found in the data section, as we can see, ocmweoid is set to 0x13A 

```cpp
.data:0000000000004028 ocmweoid        dq 13Ah #h means hex
```

So the resulting access operation becomes 

```cpp
*(_QWORD *)&mystery_array[0x13A + 8];
```

The concept remains the same for each of the obscurely named members in the data segment

```
ocmweoid
qoejooqe
xmrmfmet
ewrorfje
```

So now realizing this I understood the routine this function was going through, it grabbed the 0x13A \+ 8th item from the mystery array which would be a byte of data, stores that in v1, grabs a byte from the mystery array at offset **ocmweoid** amd stores it as the first item (0th index) in the s array, stores v1 in s+1 (the 1st index). Remember, when working with arrays using pointer arithmetic \*(array) always points to the start of the array, and to access the next element, you do \*(array + 1), \*(array + 2), to grab the item at the first and second index respectively, so on so forth. 

Next it seems to be running an decryption algorithim on the string, than after the algorithim is done running, it zeros out the memory in s than frees it. I won't explain what the algorithim does because it's pretty trivial information when it comes to finding out how to solve the challenge, which if you're new to CTF's/Reverse Engineering I would like to say that if there's a blackbox/system that you don't understand the innerworkings of, but you know how to use it to get the results that you want, dont waste time trying to understand the innerworkings of it! Most of the time it isn't worth it :p

Next, the destructor

```cpp
void __fastcall mystery_object::~mystery_object(void *a1)
{
  __int64 v1; // rdx
  char *v2; // [rsp+18h] [rbp-18h]
  unsigned __int64 i; // [rsp+20h] [rbp-10h]
  _BYTE *s; // [rsp+28h] [rbp-8h]

  s = malloc(0x11uLL);
  s[16] = 0;
  v1 = *(_QWORD *)((char *)&mystery_array + qoejooqe + 8);
  *(_QWORD *)s = *(_QWORD *)((char *)&mystery_array + qoejooqe);
  *((_QWORD *)s + 1) = v1;
  v2 = (char *)&mystery_array + ewrorfje;
  for ( i = 0LL; i <= 0xF; ++i )
  {
    s[i] -= v2[1];
    s[i] ^= *v2;
    v2 += 2;
  }
  memset(s, 0, 0x11uLL);
  free(s);
}
```


It turns out, the destructor is very similar to the constructor, almost like a yin and yang! I made a connection in my mind with the challenge name and description "The key you seek is in 2 parts! One's after the end, one's before the start!" the solution is in the constructor and destructor!

Now hypothetically, you could grab the bytes from the binary, use them to recreate the encryption algorithim in reverse, than grab the flag by doing that, but the amount of effort it would take is not worth it. We have the binary, and now we know that the flag is being created by these algorithims, meaning before the memory is set to 0, s should hypothetically hold parts of a flag in both func calls, so lets run the binary in gdb!

Vanilla GDB, while powerful is still quite lackluster so I use gef, an extension for gdb which can be downloaded [here!](https://github.com/hugsy/gef). So after loading the binary in gdb with gef installed, we can set some breakpoints in both mystery\_object::mystery_object() and mystery\_object\:\:\~mystery\_object\(\). Now the thing about C++ is that the names of these functions isn't the alias they're actually stored as in the binary, what we're seeing now is a demangled name. To get the demangled name, we can run disassemble \_\_static\_initialization\_and\_destruction\_0(int, int)

The following is printed

```assembly 
gef➤  disassemble __static_initialization_and_destruction_0(int, int)
Dump of assembler code for function _Z41__static_initialization_and_destruction_0ii:
   0x000000000000131f <+0>:     endbr64
   0x0000000000001323 <+4>:     push   rbp
   0x0000000000001324 <+5>:     mov    rbp,rsp
   0x0000000000001327 <+8>:     sub    rsp,0x10
   0x000000000000132b <+12>:    mov    DWORD PTR [rbp-0x4],edi
   0x000000000000132e <+15>:    mov    DWORD PTR [rbp-0x8],esi
   0x0000000000001331 <+18>:    cmp    DWORD PTR [rbp-0x4],0x1
   0x0000000000001335 <+22>:    jne    0x1372 <_Z41__static_initialization_and_destruction_0ii+83>
   0x0000000000001337 <+24>:    cmp    DWORD PTR [rbp-0x8],0xffff
   0x000000000000133e <+31>:    jne    0x1372 <_Z41__static_initialization_and_destruction_0ii+83>
   0x0000000000001340 <+33>:    lea    rax,[rip+0x3059]        # 0x43a0 <huh>
   0x0000000000001347 <+40>:    mov    rdi,rax
   0x000000000000134a <+43>:    call   0x138e <_ZN14mystery_objectC2Ev>
   0x000000000000134f <+48>:    lea    rax,[rip+0x2cb2]        # 0x4008
   0x0000000000001356 <+55>:    mov    rdx,rax
   0x0000000000001359 <+58>:    lea    rax,[rip+0x3040]        # 0x43a0 <huh>
   0x0000000000001360 <+65>:    mov    rsi,rax
   0x0000000000001363 <+68>:    lea    rax,[rip+0x116]        # 0x1480 <_ZN14mystery_objectD2Ev>
   0x000000000000136a <+75>:    mov    rdi,rax
   0x000000000000136d <+78>:    call   0x1110 <__cxa_atexit@plt>
   0x0000000000001372 <+83>:    nop
   0x0000000000001373 <+84>:    leave
   0x0000000000001374 <+85>:    ret
End of assembler dump.
```

As we can see. \_ZN14mystery\_objectC2Ev is the constructor because of the C2, and \_ZN14mystery_objectD2Ev is the destructor because of D2. Now lets disassemble them to find good breakpoint spots.

When we disassemble the constructor, we get the following output

```asm
gef➤  disassemble _ZN14mystery_objectC2Ev
Dump of assembler code for function _ZN14mystery_objectC2Ev:
   0x000000000000138e <+0>:     endbr64
   0x0000000000001392 <+4>:     push   rbp
   0x0000000000001393 <+5>:     mov    rbp,rsp
   0x0000000000001396 <+8>:     sub    rsp,0x30
   0x000000000000139a <+12>:    mov    QWORD PTR [rbp-0x28],rdi
   0x000000000000139e <+16>:    mov    edi,0x11
   0x00000000000013a3 <+21>:    call   0x1100 <malloc@plt>
   0x00000000000013a8 <+26>:    mov    QWORD PTR [rbp-0x8],rax
   0x00000000000013ac <+30>:    mov    rax,QWORD PTR [rbp-0x8]
   0x00000000000013b0 <+34>:    add    rax,0x10
   0x00000000000013b4 <+38>:    mov    BYTE PTR [rax],0x0
   0x00000000000013b7 <+41>:    mov    rax,QWORD PTR [rip+0x2c6a]        # 0x4028 <ocmweoid>
   0x00000000000013be <+48>:    lea    rdx,[rip+0x2c7b]        # 0x4040 <mystery_array>
   0x00000000000013c5 <+55>:    add    rax,rdx
   0x00000000000013c8 <+58>:    mov    rdx,QWORD PTR [rax+0x8]
   0x00000000000013cc <+62>:    mov    rax,QWORD PTR [rax]
   0x00000000000013cf <+65>:    mov    rcx,QWORD PTR [rbp-0x8]
   0x00000000000013d3 <+69>:    mov    QWORD PTR [rcx],rax
   0x00000000000013d6 <+72>:    mov    QWORD PTR [rcx+0x8],rdx
   0x00000000000013da <+76>:    mov    rax,QWORD PTR [rip+0x2c57]        # 0x4038 <xmrmfmet>
   0x00000000000013e1 <+83>:    lea    rdx,[rip+0x2c58]        # 0x4040 <mystery_array>
   0x00000000000013e8 <+90>:    add    rax,rdx
   0x00000000000013eb <+93>:    mov    QWORD PTR [rbp-0x18],rax
   0x00000000000013ef <+97>:    mov    QWORD PTR [rbp-0x10],0x0
   0x00000000000013f7 <+105>:   jmp    0x1454 <_ZN14mystery_objectC2Ev+198>
   0x00000000000013f9 <+107>:   mov    rdx,QWORD PTR [rbp-0x8]
   0x00000000000013fd <+111>:   mov    rax,QWORD PTR [rbp-0x10]
   0x0000000000001401 <+115>:   add    rax,rdx
   0x0000000000001404 <+118>:   movzx  edx,BYTE PTR [rax]
   0x0000000000001407 <+121>:   mov    rax,QWORD PTR [rbp-0x18]
   0x000000000000140b <+125>:   add    rax,0x1
   0x000000000000140f <+129>:   movzx  ecx,BYTE PTR [rax]
   0x0000000000001412 <+132>:   mov    rsi,QWORD PTR [rbp-0x8]
   0x0000000000001416 <+136>:   mov    rax,QWORD PTR [rbp-0x10]
   0x000000000000141a <+140>:   add    rax,rsi
   0x000000000000141d <+143>:   sub    edx,ecx
   0x000000000000141f <+145>:   mov    BYTE PTR [rax],dl
   0x0000000000001421 <+147>:   mov    rdx,QWORD PTR [rbp-0x8]
   0x0000000000001425 <+151>:   mov    rax,QWORD PTR [rbp-0x10]
   0x0000000000001429 <+155>:   add    rax,rdx
   0x000000000000142c <+158>:   movzx  ecx,BYTE PTR [rax]
   0x000000000000142f <+161>:   mov    rax,QWORD PTR [rbp-0x18]
   0x0000000000001433 <+165>:   movzx  edx,BYTE PTR [rax]
   0x0000000000001436 <+168>:   mov    rsi,QWORD PTR [rbp-0x8]
   0x000000000000143a <+172>:   mov    rax,QWORD PTR [rbp-0x10]
   0x000000000000143e <+176>:   add    rax,rsi
   0x0000000000001441 <+179>:   xor    edx,ecx
   0x0000000000001443 <+181>:   mov    BYTE PTR [rax],dl
   0x0000000000001445 <+183>:   add    QWORD PTR [rbp-0x18],0x1
   0x000000000000144a <+188>:   add    QWORD PTR [rbp-0x18],0x1
   0x000000000000144f <+193>:   add    QWORD PTR [rbp-0x10],0x1
   0x0000000000001454 <+198>:   cmp    QWORD PTR [rbp-0x10],0xf
   0x0000000000001459 <+203>:   jbe    0x13f9 <_ZN14mystery_objectC2Ev+107>
   0x000000000000145b <+205>:   mov    rax,QWORD PTR [rbp-0x8]
   0x000000000000145f <+209>:   mov    edx,0x11
   0x0000000000001464 <+214>:   mov    esi,0x0
   0x0000000000001469 <+219>:   mov    rdi,rax
   0x000000000000146c <+222>:   call   0x10d0 <memset@plt>
   0x0000000000001471 <+227>:   mov    rax,QWORD PTR [rbp-0x8]
   0x0000000000001475 <+231>:   mov    rdi,rax
   0x0000000000001478 <+234>:   call   0x10b0 <free@plt>
   0x000000000000147d <+239>:   nop
   0x000000000000147e <+240>:   leave
   0x000000000000147f <+241>:   ret
End of assembler dump.
```

So we know that the algorithim will be completed right after we finish with the loop from 107->203 so lets set a breakpoint right after the jmp instruction at 203, line 205. We can set a breakpoint by typing in the command b \*(\_ZN14mystery\_objectC2Ev + 205), once our breakpoint is set for this function, we can disassemble the destructor next. Here is the result of the disassembly.

```asm
gef➤  disassemble _ZN14mystery_objectD2Ev
Dump of assembler code for function _ZN14mystery_objectD2Ev:
   0x0000000000001480 <+0>:     endbr64
   0x0000000000001484 <+4>:     push   rbp
   0x0000000000001485 <+5>:     mov    rbp,rsp
   0x0000000000001488 <+8>:     sub    rsp,0x30
   0x000000000000148c <+12>:    mov    QWORD PTR [rbp-0x28],rdi
   0x0000000000001490 <+16>:    mov    edi,0x11
   0x0000000000001495 <+21>:    call   0x1100 <malloc@plt>
   0x000000000000149a <+26>:    mov    QWORD PTR [rbp-0x8],rax
   0x000000000000149e <+30>:    mov    rax,QWORD PTR [rbp-0x8]
   0x00000000000014a2 <+34>:    add    rax,0x10
   0x00000000000014a6 <+38>:    mov    BYTE PTR [rax],0x0
   0x00000000000014a9 <+41>:    mov    rax,QWORD PTR [rip+0x2b80]        # 0x4030 <qoejooqe>
   0x00000000000014b0 <+48>:    lea    rdx,[rip+0x2b89]        # 0x4040 <mystery_array>
   0x00000000000014b7 <+55>:    add    rax,rdx
   0x00000000000014ba <+58>:    mov    rdx,QWORD PTR [rax+0x8]
   0x00000000000014be <+62>:    mov    rax,QWORD PTR [rax]
   0x00000000000014c1 <+65>:    mov    rcx,QWORD PTR [rbp-0x8]
   0x00000000000014c5 <+69>:    mov    QWORD PTR [rcx],rax
   0x00000000000014c8 <+72>:    mov    QWORD PTR [rcx+0x8],rdx
   0x00000000000014cc <+76>:    mov    rax,QWORD PTR [rip+0x2b4d]        # 0x4020 <ewrorfje>
   0x00000000000014d3 <+83>:    lea    rdx,[rip+0x2b66]        # 0x4040 <mystery_array>
   0x00000000000014da <+90>:    add    rax,rdx
   0x00000000000014dd <+93>:    mov    QWORD PTR [rbp-0x18],rax
   0x00000000000014e1 <+97>:    mov    QWORD PTR [rbp-0x10],0x0
   0x00000000000014e9 <+105>:   jmp    0x1546 <_ZN14mystery_objectD2Ev+198>
   0x00000000000014eb <+107>:   mov    rdx,QWORD PTR [rbp-0x8]
   0x00000000000014ef <+111>:   mov    rax,QWORD PTR [rbp-0x10]
   0x00000000000014f3 <+115>:   add    rax,rdx
   0x00000000000014f6 <+118>:   movzx  edx,BYTE PTR [rax]
   0x00000000000014f9 <+121>:   mov    rax,QWORD PTR [rbp-0x18]
   0x00000000000014fd <+125>:   add    rax,0x1
   0x0000000000001501 <+129>:   movzx  ecx,BYTE PTR [rax]
   0x0000000000001504 <+132>:   mov    rsi,QWORD PTR [rbp-0x8]
   0x0000000000001508 <+136>:   mov    rax,QWORD PTR [rbp-0x10]
   0x000000000000150c <+140>:   add    rax,rsi
   0x000000000000150f <+143>:   sub    edx,ecx
   0x0000000000001511 <+145>:   mov    BYTE PTR [rax],dl
   0x0000000000001513 <+147>:   mov    rdx,QWORD PTR [rbp-0x8]
   0x0000000000001517 <+151>:   mov    rax,QWORD PTR [rbp-0x10]
   0x000000000000151b <+155>:   add    rax,rdx
   0x000000000000151e <+158>:   movzx  ecx,BYTE PTR [rax]
   0x0000000000001521 <+161>:   mov    rax,QWORD PTR [rbp-0x18]
   0x0000000000001525 <+165>:   movzx  edx,BYTE PTR [rax]
   0x0000000000001528 <+168>:   mov    rsi,QWORD PTR [rbp-0x8]
   0x000000000000152c <+172>:   mov    rax,QWORD PTR [rbp-0x10]
   0x0000000000001530 <+176>:   add    rax,rsi
   0x0000000000001533 <+179>:   xor    edx,ecx
   0x0000000000001535 <+181>:   mov    BYTE PTR [rax],dl
   0x0000000000001537 <+183>:   add    QWORD PTR [rbp-0x18],0x1
   0x000000000000153c <+188>:   add    QWORD PTR [rbp-0x18],0x1
   0x0000000000001541 <+193>:   add    QWORD PTR [rbp-0x10],0x1
   0x0000000000001546 <+198>:   cmp    QWORD PTR [rbp-0x10],0xf
   0x000000000000154b <+203>:   jbe    0x14eb <_ZN14mystery_objectD2Ev+107>
   0x000000000000154d <+205>:   mov    rax,QWORD PTR [rbp-0x8]
   0x0000000000001551 <+209>:   mov    edx,0x11
   0x0000000000001556 <+214>:   mov    esi,0x0
   0x000000000000155b <+219>:   mov    rdi,rax
   0x000000000000155e <+222>:   call   0x10d0 <memset@plt>
   0x0000000000001563 <+227>:   mov    rax,QWORD PTR [rbp-0x8]
   0x0000000000001567 <+231>:   mov    rdi,rax
   0x000000000000156a <+234>:   call   0x10b0 <free@plt>
   0x000000000000156f <+239>:   nop
   0x0000000000001570 <+240>:   leave
   0x0000000000001571 <+241>:   ret
End of assembler dump.
```

As you can see they're extremely similar, so much so that we can set a breakpoint at the same instruction as well (205). After running b \*(\_ZN14mystery\_objectD2Ev + 205) we can run the program. Type r and the program should start running. Once our first breakpoint hits, we can already see that the hypothesis was correct.

```
[ Legend: Modified register | Code | Heap | Stack | String ]
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── registers ────
$rax   : 0x00005555555592af  →  0x0000000000000075 ("u"?)
$rbx   : 0x00007fffffffe368  →  0x00007fffffffe605  →  "/afs/umbc.edu/users/y/o/yonast1/home/beforeandafte[...]"
$rcx   : 0x32
$rdx   : 0x75
$rsp   : 0x00007fffffffe290  →  0x00007fffffffe360  →  0x0000000000000001
$rbp   : 0x00007fffffffe2c0  →  0x00007fffffffe2e0  →  0x00007fffffffe2f0  →  0x0000000000000001
$rsi   : 0x00005555555592a0  →  "DawgCTF{n0wth4tu"
$rdi   : 0x00005555555592a0  →  "DawgCTF{n0wth4tu"
$rip   : 0x000055555555545b  →  <mystery_object::mystery_object()+205> mov rax, QWORD PTR [rbp-0x8]
$r8    : 0x00007ffff7df8c80  →  0x0000000000000000
$r9    : 0x00007ffff7fcbb20  →  <_dl_fini+0> endbr64
$r10   : 0x00007ffff7df8ce0  →  0x00005555555592b0  →  0x0000000000000000
$r11   : 0x20
$r12   : 0x0
$r13   : 0x00007fffffffe378  →  0x00007fffffffe639  →  "USER=yonast1"
$r14   : 0x0000555555557d90  →  0x00005555555551c0  →  <__do_global_dtors_aux+0> endbr64
$r15   : 0x00007ffff7ffd000  →  0x00007ffff7ffe2a0  →  0x0000555555554000  →   jg 0x555555554047
$eflags: [zero carry parity ADJUST sign trap INTERRUPT direction overflow resume virtualx86 identification]
$cs: 0x33 $ss: 0x2b $ds: 0x00 $es: 0x00 $fs: 0x00 $gs: 0x00
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── stack ────
0x00007fffffffe290│+0x0000: 0x00007fffffffe360  →  0x0000000000000001    ← $rsp
0x00007fffffffe298│+0x0008: 0x00005555555583a0  →  <huh+0> add BYTE PTR [rax], al
0x00007fffffffe2a0│+0x0010: 0x0000000000000000
0x00007fffffffe2a8│+0x0018: 0x00005555555582c9  →  <mystery_array+649> or al, 0x63
0x00007fffffffe2b0│+0x0020: 0x0000000000000010
0x00007fffffffe2b8│+0x0028: 0x00005555555592a0  →  "DawgCTF{n0wth4tu"
0x00007fffffffe2c0│+0x0030: 0x00007fffffffe2e0  →  0x00007fffffffe2f0  →  0x0000000000000001     ← $rbp
0x00007fffffffe2c8│+0x0038: 0x000055555555534f  →  <__static_initialization_and_destruction_0(int,+0> lea rax, [rip+0x2cb2]        # 0x555555558008
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── code:x86:64 ────
   0x55555555544f <mystery_object::mystery_object()+193> add    QWORD PTR [rbp-0x10], 0x1
   0x555555555454 <mystery_object::mystery_object()+198> cmp    QWORD PTR [rbp-0x10], 0xf
   0x555555555459 <mystery_object::mystery_object()+203> jbe    0x5555555553f9 <_ZN14mystery_objectC2Ev+107>
 → 0x55555555545b <mystery_object::mystery_object()+205> mov    rax, QWORD PTR [rbp-0x8]
   0x55555555545f <mystery_object::mystery_object()+209> mov    edx, 0x11
   0x555555555464 <mystery_object::mystery_object()+214> mov    esi, 0x0
   0x555555555469 <mystery_object::mystery_object()+219> mov    rdi, rax
   0x55555555546c <mystery_object::mystery_object()+222> call   0x5555555550d0 <memset@plt>
   0x555555555471 <mystery_object::mystery_object()+227> mov    rax, QWORD PTR [rbp-0x8]
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── threads ────
[#0] Id 1, Name: "beforeandafter", stopped 0x55555555545b in mystery_object::mystery_object() (), reason: BREAKPOINT
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── trace ────
[#0] 0x55555555545b → mystery_object::mystery_object()()
[#1] 0x55555555534f → __static_initialization_and_destruction_0(int, int)()
[#2] 0x55555555538c → _GLOBAL__sub_I_ewrorfje()
[#3] 0x7ffff7c2967e → __libc_start_main_impl()
[#4] 0x555555555145 → _start()
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
gef➤ 
```

Right there at 0x00005555555592a0, the first half of the flag is there "DawgCTF{n0wth4tu". To confirm, you can read the memory at that location using x/1s 0x00005555555592a0. This will allow you to read 1 string from the memory address. Nice! Lets go to the next breakpoint now, type c and hit enter. As you can see here

```
[ Legend: Modified register | Code | Heap | Stack | String ]
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── registers ────
$rax   : 0x00005555555592cf  →  0x000000000000007d ("}"?)
$rbx   : 0x0
$rcx   : 0x2
$rdx   : 0x7d
$rsp   : 0x00007fffffffe1b0  →  0x0000000000000000
$rbp   : 0x00007fffffffe1e0  →  0x0000000000000000
$rsi   : 0x00005555555592c0  →  "s33uh4v3th3k3y!}"
$rdi   : 0x10
$rip   : 0x000055555555554d  →  <mystery_object::~mystery_object()+205> mov rax, QWORD PTR [rbp-0x8]
$r8    : 0x00005555555592c0  →  "s33uh4v3th3k3y!}"
$r9    : 0x73
$r10   : 0x0
$r11   : 0x2fa268906341d368
$r12   : 0x00007ffff7df8838  →  0x00007ffff7df9f00  →  0x0000000000000000
$r13   : 0x00005555555583a0  →  <huh+0> add BYTE PTR [rax], al
$r14   : 0x00007ffff7df9ee8  →  0x0000000000000000
$r15   : 0x00007ffff7df9f00  →  0x0000000000000000
$eflags: [zero carry parity ADJUST sign trap INTERRUPT direction overflow resume virtualx86 identification]
$cs: 0x33 $ss: 0x2b $ds: 0x00 $es: 0x00 $fs: 0x00 $gs: 0x00
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── stack ────
0x00007fffffffe1b0│+0x0000: 0x0000000000000000   ← $rsp
0x00007fffffffe1b8│+0x0008: 0x00005555555583a0  →  <huh+0> add BYTE PTR [rax], al
0x00007fffffffe1c0│+0x0010: 0x0000000000000029 (")"?)
0x00007fffffffe1c8│+0x0018: 0x0000555555558113  →  <mystery_array+211> (bad)
0x00007fffffffe1d0│+0x0020: 0x0000000000000010
0x00007fffffffe1d8│+0x0028: 0x00005555555592c0  →  "s33uh4v3th3k3y!}"
0x00007fffffffe1e0│+0x0030: 0x0000000000000000   ← $rbp
0x00007fffffffe1e8│+0x0038: 0x00007ffff7c41085  →  <__run_exit_handlers+261> mov edx, 0x1
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── code:x86:64 ────
   0x555555555541 <mystery_object::~mystery_object()+193> add    QWORD PTR [rbp-0x10], 0x1
   0x555555555546 <mystery_object::~mystery_object()+198> cmp    QWORD PTR [rbp-0x10], 0xf
   0x55555555554b <mystery_object::~mystery_object()+203> jbe    0x5555555554eb <_ZN14mystery_objectD2Ev+107>
 → 0x55555555554d <mystery_object::~mystery_object()+205> mov    rax, QWORD PTR [rbp-0x8]
   0x555555555551 <mystery_object::~mystery_object()+209> mov    edx, 0x11
   0x555555555556 <mystery_object::~mystery_object()+214> mov    esi, 0x0
   0x55555555555b <mystery_object::~mystery_object()+219> mov    rdi, rax
   0x55555555555e <mystery_object::~mystery_object()+222> call   0x5555555550d0 <memset@plt>
   0x555555555563 <mystery_object::~mystery_object()+227> mov    rax, QWORD PTR [rbp-0x8]
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── threads ────
[#0] Id 1, Name: "beforeandafter", stopped 0x55555555554d in mystery_object::~mystery_object() (), reason: BREAKPOINT
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── trace ────
[#0] 0x55555555554d → mystery_object::~mystery_object()()
[#1] 0x7ffff7c41085 → __run_exit_handlers()
[#2] 0x7ffff7c41200 → exit()
[#3] 0x7ffff7c29557 → __libc_start_call_main()
[#4] 0x7ffff7c29609 → __libc_start_main_impl()
[#5] 0x555555555145 → _start()
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
gef➤
```

In r8, rsi, even on the stack still, we get the second half of the flag, s33uh4v3th3k3y!}, now if we combine it, we get DawgCTF{n0wth4tus33uh4v3th3k3y!}, enter the flag and now you've got your points B)


Thanks for reading :) If you have any questions please pm me on discord Awayy#1337 
<3
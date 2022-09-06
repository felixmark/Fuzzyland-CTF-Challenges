# Fuzzyland-CTF-Challenges
My solutions with explanations to the https://fuzzy.land challenges.

## lvl1
To solve lvl1 a simple text editor, the program `strings` or `gdb` is used.
This is the gdb solution:
<details>
<summary>Solution</summary>
```
gdb lvl1
disas /m main
x/s <ADDRESS OF PASSWORD>
```
</details>
<details>
<summary>Solution</summary>

```
LosCTF{5tr1ngZ_FTW}
```
</details>

## lvl2
To solve lvl2 I had to decompile the program. I used Ghidra for that purpose.
I found that there is a main and a check_pwd function.
An input file called "password.pwd" is read and compared to the calulated value in the check_pwd function.
There are two constants which are of interest:
FLAG and LFNAME
These two can be used, to calculate the password, which serves as the Flag.
Since they are not 0 terminated strings, I had to store the data in hex notation in arrays.
After writing a small program reversing the operations (XORing) done to the flag, I received the actual flag.

The decompiled program:
<details>
<summary>Solution</summary>
```C
int main(void) {
  FILE *__stream;
  undefined8 uVar1;
  long in_FS_OFFSET;
  ulong local_60;
  undefined8 local_45;
  undefined4 local_3d;
  undefined local_39;
  undefined8 pwd_from_password_pwd_file;
  undefined8 local_30;
  undefined8 local_28;
  undefined4 local_20;
  undefined2 local_1c;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_45 = 0;
  local_3d = 0;
  local_39 = 0;
  for (local_60 = 0; local_60 < 0xc; local_60 = local_60 + 1) {
    *(byte *)((long)&local_45 + local_60) = LFNAME[local_60] ^ 0x9f;
  }
  __stream = fopen((char *)&local_45,"r");
  if (__stream == (FILE *)0x0) {
    uVar1 = 0xffffffff;
  }
  else {
    pwd_from_password_pwd_file = 0;
    local_30 = 0;
    local_28 = 0;
    local_20 = 0;
    local_1c = 0;
    fread(&pwd_from_password_pwd_file,1,0x1e,__stream);
    fclose(__stream);
    uVar1 = check_pwd((long)&pwd_from_password_pwd_file);
    if ((int)uVar1 == 0) {
      puts("Congrats!");
      uVar1 = 0;
    }
    else {
      uVar1 = 0xfffffffe;
    }
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
    __stack_chk_fail();
  }
  return uVar1;
}


undefined8 check_pwd(long param_1)
{
  ulong str_position;
  
  str_position = 0;
  while( true ) {
    if (28 < str_position) {
      return 0;
    }
    if (*(byte *)(str_position + param_1) != (byte)((&FLAG)[str_position] ^ LFNAME[0] + 181)) break;
    str_position = str_position + 1;
  }
  return 0xffffffff;
}
```
</details>

I retrieved the data of FLAG and LFNAME once in Ghidra and once in gdb for good measure.
These are the results:
FLAG:
e8 cb d7 e7 f0 e2 df dc 94 d6 95 ca c3 fb 90 c8 c8 fb d0 cc 97 fb d0 cc 95 ca c3 91 d9 00

LF NAME:
ef fe ec ec e8 f0 ed fb b1 ef e8 fb 00 00 00 00

The C++ program I used to inverse the operation `*(byte *)(str_position + param_1) != (byte)((&FLAG)[str_position] ^ LFNAME[0] + 181)` of the decompiled program:
<details>
<summary>Solution</summary>
```c++
#include <iostream>

using namespace std;

int main() {
  long FLAG[] = {0xe8, 0xcb, 0xd7, 0xe7, 0xf0, 0xe2, 0xdf, 0xdc, 0x94, 0xd6, 0x95, 0xca, 0xc3, 0xfb, 0x90, 0xc8, 0xc8, 0xfb, 0xd0, 0xcc, 0x97, 0xfb, 0xd0, 0xcc, 0x95, 0xca, 0xc3, 0x91, 0xd9, 0x00};
  long LFNAME[] = {0xef, 0xfe, 0xec, 0xec, 0xe8, 0xf0, 0xed, 0xfb, 0xb1, 0xef, 0xe8, 0xfb, 0x00, 0x00, 0x00, 0x00};

  for(unsigned long str_position = 0; FLAG[str_position]; ++str_position){
    cout << ((char)((FLAG)[str_position] ^ (LFNAME[0] + 181)));
  }
  cout << endl;
}
```
</details>
I compiled it by executing `g++ -o3 -Wall reversal_program.cpp -o gimme_pwd` and executed it by calling `./gimme_pwd`.
This gave me the final flag for the challenge:
<details>
<summary>Solution</summary>

```
LosCTF{x0r1ng_4ll_th3_th1ng5}
```
</details>

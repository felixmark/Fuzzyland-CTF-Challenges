# Fuzzyland-CTF-Challenges
My solutions with explanations to the https://fuzzy.land challenges.

## lvl1
To solve lvl1 a simple text editor, the program `strings` or `gdb` is used.
This is the gdb solution:
```
gdb lvl1
disas /m main
x/s <ADDRESS OF PASSWORD>
```
<details>
<summary>Solution:</summary>

```
LosCTF{5tr1ngZ_FTW}
```
</details>

## lvl2
To solve lvl2 I had to decompile the program.
You will find that there is a main and a check_pwd function.
An input file called "password.pwd" is read and compared to the calulated value in the check_pwd function.
There are two constants which are of interest:
FLAG and LFNAME
These two can be used, to calculate the password, which serves as the Flag.
Since they are not 0 terminated strings, I had to store the data in hex notation in arrays.
After writing a small program reversing the operations done to the flag, I received the actual flag.


 # Oblivious-error-writeup
**Category :** (Cryptography)

In This challenge, The author accidentally deleted the original code and replaced It.
Which made the message (presumably the flag) "undecodable"

First, I connect to the server via `nc challenge.utctf.live 8379` which gave me `N`,`e`,`x0`,`x1` and prompted me to enter `k`.Initially I tried common test cases like 0 , negative, very large number.
Then i checked the **"my-code.txt"** file (snipped below)


```
while True:
    try:
        print("Please pick a value k.")
        k = int(input())
        break
    except ValueError:
        print("Invalid value. Please pick an integer.")
        print("Please pick a value k.")
        k = int(input())

v = (x0 + (int(k) ^ e)) % N
```

I tried putting small values as to see how the function behaves, But the outputs were not matching.
I searched for it online and found out that in python `^` is "XOR"(exclusive OR) and not equivalent to "`pow()` or `**`".


Then I again tried with the small dummy values and this time it worked fine. Further Research on **XOR** made me realise that " Any number XORed with itself is 0" so i tried `k = e` to the server which would work like :


```
   v = (x0 + (e ^ e)) % N

   v = (x0) % N

   v =  x0  ( as the server always chose x0 < N)
```

This gave me two messages which I converted to ASCII using this simple script
```
from Crypto.Util.number import long_to_bytes

message = 14614909182015656433423375395560694783276217129595018028195683192534693780147751705041670356317823574397
print(long_to_bytes(message))
```


This revealed the flag as "`hgsynt{Pbatengf! Lbh pnhtug n erq ureevat!}`"


This is a standard case of **"Caesar's Cipher"** which I decrypted using `https://cyberchef.io` using **"ROT13"**


<img width="1920" height="937" alt="image" src="https://github.com/user-attachments/assets/83815190-fe20-49b5-a153-32a7e2ece19e" />

flag is "`utflag{Congrats! You caught a red herring!}`"

Which said **"red herring"** in the flag. **AND YES I DID TRY TO PLUGIN THIS FLAG AND FELT LIKE AN IDIOT.**

This is where I got stuck with this challenge. I went back to the problem statement to find more clues and it mentioned it uses " RSA-based 1-2 oblivious transfer protocol program". After studying 1-2 OT protocol for a while , I finally found the solution.


Basically, the server holds exactly 2 messages. It is designed such that the user can choose one of them without the server knowing which one was picked.

Since in the previous case, when we put `k=e`, we get `v=x0`.
Now we will purposefully set `v=x1` and . Which means we get `x1 = ( x0 + ( int(k) ^ e)) % N` . And from this , we will calculate `k` and give it back to the server.

Now the math becomes something like this:
```
   x1 = ( x0 + ( int(k) ^ e)) % N  
   x1 = x0 + int(k) ^ e - CN                       (C is some constant that satisfies the equation)
   x1 - x0 - CN = int(k) ^ e
  (x1 - x0) % N = int(k) ^ e
    Taking XOR with e both sides
  ((x1 - x0) % N) ^ e = (int(k) ^ e) ^ e = int(k)
  ```
From this we can find the `k` corresponding to `x1` using the following script

```
x0 = 1635098239013264044126027799564779028895101810323127906179033019287020192470288173854210045390953471299117008362247280688033069830294878535369470132863666
x1 = 1535307749372344639101360180775422009419295966380581195016572427928745807105717072927442947719306712851940243117467252157479307125106154881587379733741575
N = 7320659687307596757836953692756431226792068948238181385629069698561912361441625060134195858959437733983006037972511014741645444917789509936374658031840479
e = 65537
Z = (x1 - x0) % N       
k = Z ^ e
print(k)
```

Now we have to give this value of `K` to server to get messages. Finally, using the same script, we can convert the integers back to ASCII flag.

```
  from Crypto.Util.number import long_to_bytes
message = 16441782473165749985269251414928450202051900518929647105868978172963309169080628914206924705003483790602
print(long_to_bytes(message))

```
The Final Flag we get is "`utflag{my_obl1v10u5_fr13nd_ru1n3d_my_c0de}`"

> [!IMPORTANT]
> Because the server generates a new set of variables (N, e, x0, x1) on every single connection, you might notice that specific numbers in my screenshots don't always perfectly match the numbers in my code snippets.
> I had to connect to the server multiple times while testing the exploit, grabbing screenshots, and writing this. The underlying maths, however, remains exactly the same.

















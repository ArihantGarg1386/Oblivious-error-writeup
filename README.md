# Oblivious-error-writeup
**Category:**(Cryptography)

In This Challenge, The author accidentally deleted the original code and replaced It.
Which made the message (presumably the flag) "undecodable"

First, I connect to the server via `nc challenge.utctf.live 8379` which gave me `N`,`e`,`x0`,`x1` and prompted me to enter `k`.Initially I tried common test cases like 0 , negative, very large number.

then i checked the "my-code.txt" file (snipped below)


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


Then I again tried with the small dummy values and this time is worked fine.Further Research on XOR made me realise that " Any number XORed with itself is 0" so i tried `k = e` to the server which would work like :


```
   v = (x0 + (e ^ e)) % N

   v = (x0) % N

   v =  x0  ( as the server always chose x0 < N)
```

##This gave me two messages which I converted to ASCII using this simple script
```
from Crypto.Util.number import long_to_bytes

message = 14614909182015656433423375395560694783276217129595018028195683192534693780147751705041670356317823574397
print(long_to_bytes(message))
```


This revealed the flag as "`hgsynt{Pbatengf! Lbh pnhtug n erq ureevat!}`"


This is a standard case of "Caesar's Cipher" which I decrypted using `https://cyberchef.io` using "ROT13"


<img width="1920" height="937" alt="image" src="https://github.com/user-attachments/assets/83815190-fe20-49b5-a153-32a7e2ece19e" />

flag is "`utflag{Congrats! You caught a red herring!}`"

Which said "red herring" in the flag. AND YES I DID TRIED TO PLUGIN THIS FLAG AND FELT LIKE AN IDIOT.














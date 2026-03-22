# Oblivious-error-writeup
Category:(Cryptography)

In This Challenge, The author accidentally deleted the original code and replaced it.
Which made the message (presumably the flag) "undecodable"

First, I connect to the server via `nc challenge.utctf.live 8379` which gave me N,e,x0,x1 and prompted me to enter k.Initially i tried common test cases like 0 , negative, very large number.

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

Then I again tried with the small values and this time is worked fine.Further Research on XOR made me realise that " Any number XORed with itself is 0" so i tried `k = e` to the server which would work like :

```
   v = (x0 + (e ^ e)) % N
   v = (x0) % N
   v =  x0  ( as the server always chose x0 < N)
```










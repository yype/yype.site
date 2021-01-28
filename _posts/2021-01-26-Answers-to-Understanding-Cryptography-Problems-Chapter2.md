---
layout: post
title: Solutions to Understanding Cryptography Problems - Chapter 2
description: 
summary: 
tags: [Crypto, Understanding Cryptography]
visible: 0
---

* TOC
{:toc}

## Intro

My solutions to the practice problems in "Understanding Cryptography: A Textbook for Students and Practitioners - Chapter 2". 

The solution scripts to some of the problems have been uploaded to GitHub, you can check them out at: [https://github.com/yype/UnderstandingCrypto](https://github.com/yype/UnderstandingCrypto){:target='_blank'}.

## Problems

### 2.1

(1) The key could look like a stream of letters from A~Z, which represent keys between 0~25.

$$
y_i=e_{k_i}(x_i)=x_i+k_i\bmod26\\
x_i=d_{k_i}(y_i)=y_i-k_i\bmod26\\
$$

(2) I found this problem strange at first, and later, according to the Internet, I found that the key provided in the book is wrong. The correct key should be `rsidpy dkawoy`.

Solution script:

```python
enc = 'bsaspp kkuosp'
key = 'rsidpy dkawoy'  # the key in the book is wrong

dec = ''
for i in range(len(enc)):
    if enc[i] != ' ':
        e, k = ord(enc[i])-ord('a'), ord(key[i])-ord('a')
        dec += chr(((e-k) % 26)+ord('a'))
    else:
        dec += ' '

print(dec)
```

Output:

```
kaspar hauser
```

(3) According to [this](https://en.wikipedia.org/wiki/Kaspar_Hauser), it seems that Hauser died of a fatal stab wound on 17 December 1833.

### 2.2

(1) Life cycle of the key: Every single bit in the key should never be reused.

(2) Storage of the key during the life cycle: The key must be stored at both the sending and receiving end.

(3) Storage of the key after the life cycle: They must be destroyed since all former encrypted data will become vulunerable if these keys are leaked in the future.

(4) Key distribution: The safest way, in my point of view, is to make only 2 copies of the key in the world, and to distribute the keys physically(without being intercepted by any 3rd parties).

(5) Generation of the key: A TRNG should be used. Or else it will be the same as using a normal stream cipher along with a PRNG, and just distributing the key of the PRNG.

### 2.3

When 128 bits of the plaintext($x_0$~$x_{127}$) and their corresponding cipher bits($y_0$~$y_{127}$) are known to an attacker, he/she/it can easily calculate the key bits($k_0$~$k_{127}$) used in the encryption process:

$$
k_i=x_i\oplus y_i,i=0,1,...,127
$$

### 2.4 (not verified)

The attacker will have no idea which key should be correct, so any brute-force attack is meaningless. Each key guessed can be used to decrypt the ciphertext, generating a plaintext(plaindata). There is no way the attacker can know which key is right and if the plaintext decrypted is the original one.

### 2.5

(1)

$$
(c_2=1,c_1=0,c_0=1)\\
\text{}\\
\begin{array}{|c|c c c|}
\text{Clk} & FF_2 & FF_1 & FF_0=s_i \\
0 & 1 & 0 & 0 \\
1 & 1 & 1 & 0 \\
2 & 1 & 1 & 1 \\
3 & 0 & 1 & 1 \\
4 & 1 & 0 & 1 \\
5 & 0 & 1 & 0 \\
6 & 0 & 0 & 1 \\
7 & 1 & 0 & 0 \\
\end{array}\\
$$

So the sequence is (`0011101`)*.

(2)

$$
(c_2=1,c_1=0,c_0=1)\\
\text{}\\
\begin{array}{|c|c c c|}
\text{Clk} & FF_2 & FF_1 & FF_0=s_i \\
0 & 0 & 1 & 1 \\
1 & 1 & 0 & 1 \\
2 & 0 & 1 & 0 \\
3 & 0 & 0 & 1 \\
4 & 1 & 0 & 0 \\
5 & 1 & 1 & 0 \\
6 & 1 & 1 & 1 \\
7 & 0 & 1 & 1 \\
\end{array}\\
$$

So the sequence is (`1101001`)*.

(3) **a)** Their periods are the same. **b)** `S2` can be viewed as `ROL(S1, 3)`, where `S1/S2` represent the repetends of the first and second sequence.

### 2.6

One possible attack: I will need to know 25 bytes(letters), which is `200(bits) / 8(bits/byte)`, of the ciphertext and their corresponding plaintext. Then I will be able to calculate 200 bits of the key sequence used($k_i=\text{plainbit}_i\oplus\text{cipherbit}_i$). 

After that, all left to do is to determine the actual keysize. We can simply run several brute-force decryptions, starting from the first plaintext bit we know, using the first $k$ bits of the 200 key bits calculated, where $k=150,151,...,200$. 

It should be noted that if the keysize used is not correct, there is a great chance that the data decrypted is not readable(printable) ASCII text. As a result, once we successfully decrypt the ciphertext and get a readable plaintext after the 25th byte, we know what the actual keysize is.

### 2.7

The chosen LFSR's polynomial is `(0,1,3,4,8)`. I wrote a simple script to help me with the calculation, and the resulting first two output bytes can be viewed as a little endian number `0x90ff`.

```
chapt2> py .\p2.7.py
0x90ff
```

### 2.8

(1) Draw the corresponding LFSR for each of the three polynomials.

I don't want to draw them :P

(2) I wrote a script to calculate all the possible initial values and their corresponding sequence lengh, it outputs as follows:

```
chapt2> py .\p2.8.py
Polynomial: (0, 1, 4)
init            len(seq)
1               15
2               15
3               15
4               15
5               15
6               15
7               15
8               15
9               15
10              15
11              15
12              15
13              15
14              15
15              15

Polynomial: (0, 2, 4)
init            len(seq)
1               6
2               6
3               6
4               6
5               6
6               3
7               6
8               6
9               6
10              6
11              3
12              6
13              3
14              6
15              6

Polynomial: (0, 1, 2, 3, 4)
init            len(seq)
1               5
2               5
3               5
4               5
5               5
6               5
7               5
8               5
9               5
10              5
11              5
12              5
13              5
14              5
15              5
```

We can see that, the first polynomial $x^4+x+1$ generates a maximum-length($2^4-1=15$) sequence, so it is a primitive polynomial. The second polynomial $x^4+x^2+1$ does not generate a maximum-length sequence and its sequence lengths depends on its initial values, so it is an irreducible polynomial. The third polynomial $x^4+x^3+x^2+x+1$ does not generate a maximum-length sequence, but its sequence length stays the same despite the changing initial values, so it is a reducible polynomial.

### 2.9


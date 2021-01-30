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

### 2.4

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

We can see that, the first polynomial $x^4+x+1$ can generate a maximum-length($2^4-1=15$) sequence, so it is a primitive polynomial. The second polynomial $x^4+x^2+1$ does not generate a maximum-length sequence and its sequence length depends on its initial value, so it is an irreducible polynomial. The third polynomial $x^4+x^3+x^2+x+1$ does not generate a maximum-length sequence, but its sequence length stays the same despite the changing initial value, so it is a reducible polynomial.

### 2.9

(1) $256\times2=512\text{(bits)}$.

(2) Let the known plaintext bits be given by $x_0,x_1,...,x_{511}$ and the corresponding ciphertext bits by $y_0,y_1,...,y_{511}$. With these 512 pairs of plaintext and ciphertext bits, we can reconstruct the first 512 key stream bits:

$$
s_i=x_i\oplus y_i,i=0,1,...,511
$$

Our goal is to find the key bits given by the feedback coefficients $p_i$. The relationship between the unknown key bits $p_i$ and the key stream bits $s_i$ can be described as follows:

$$
s_{i+256}=\sum_{j=0}^{255}{p_j\cdot s_{i+j}}, i=0,1,...,255
$$

This actually represents 256 linearly independent formulae, which contain 256 unknown variables $p_0,p_1,...,p_255$. This system can be easily solved using Gaussian elimination, matrix inversion or any other algorithm for solving systems of linear equations.

(3.a) The key is $p_0,p_1,...,p_{255}$.

(3.b) If we use the initial contents of the LFSR as the key, the attacker can get the key almost without performing any attack since the first 256 bits of the **key stream bits** are exactly the same as the initial content bits. If we use the initial contents of the LFSR as part of the key, the part that takes actual effect will be just the initial content, so any other bits in the key will be useless, then the situation becomes no different as just using the initial contents as the key.

### 2.10

```
Plaintext bits:  `1001 0010 0110 1101 1001 0010 0110`
Ciphertext bits: `1011 1100 0011 0001 0010 1011 0001` 
```
So we can get the key stream bits: `0010 1110 0101 1100 1011 1001 0111`. 

We can clearly observe its repetend: `0010111`. Let's suppose it's generated by a primitive polynomial, then the degree should be $m=\log_2{(7+1)}=3$. Next we test if there are indeed $2^m-1=7$ sequence states. As for the key stream bits, the states are as follows:

$$
\begin{array}{|c|c c c|}
\text{Clk} & FF_2 & FF_1 & FF_0=s_i \\
0 & 1 & 0 & 0 \\
1 & 0 & 1 & 0 \\
2 & 1 & 0 & 1 \\
3 & 1 & 1 & 0 \\
4 & 1 & 1 & 1 \\
5 & 0 & 1 & 1 \\
6 & 0 & 0 & 1 \\
7 & 1 & 0 & 0 \\
\end{array}\\
$$


We can see that there are 7 sequence states(the sequence length is 7). Therefore, *it's possible*, that the LFSR could be using a primitive polynomial of degree 3.

> Note that we can never know if the key stream contains a larger repetend. Using the given information, we can only be sure that *an LFSR using a primitive polynomial of degree 3* could be a possible scenario. There is a possibility that an LFSR using a polynomial of a larger degree is actually used. Specifically for this problem, I'll just assume the degree is 3.


(1) What is the degree m of the key stream generator?

It's 3.

(2) What is the initialization vector?

It's `100`.

(3) Determine the feedback coefficients of the LFSR.

To break the cipher we only need to use $2m=6$ key stream bits, let's grab the first 6 bits of the key stream. Let the coefficients be $p_0,p_1,p_2$, then we have:

$$
s_3=s_0p_0+s_1p_1+s_2p_2\\
s_4=s_1p_0+s_2p_1+s_3p_2\\
s_5=s_2p_0+s_3p_1+s_4p_2
$$

which can be denoted as:

$$
\begin{bmatrix}
0 & 0 & 1\\
0 & 1 & 0\\
1 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
p_0\\ 
p_1\\
p_2
\end{bmatrix}
=
\begin{bmatrix}
0\\1\\1
\end{bmatrix}
$$

We can calculate $P$ by:

$$
\begin{aligned}
P=
\begin{bmatrix}
p_0\\ 
p_1\\
p_2
\end{bmatrix}
&=\begin{bmatrix}
0 & 0 & 1\\
0 & 1 & 0\\
1 & 0 & 1
\end{bmatrix}^{-1}
\begin{bmatrix}
0\\1\\1
\end{bmatrix}\\
&=
\begin{bmatrix}
-1&0&1\\
0&1&0\\
1&0&0
\end{bmatrix}
\begin{bmatrix}
0\\1\\1
\end{bmatrix}\\
&=
\begin{bmatrix}
1\\1\\0
\end{bmatrix}
\end{aligned}
$$

So the coefficients are: $p_0=1,p_1=1,p_2=0$, the polynomial used is $x^3+x+1$.

(4.a)

I don't want to draw the circuit diagram :P

(4.b)

I wrote a script to help with the key stream generation and the coefficient verification, see the corresponding solution script for more detail.

### 2.11

I find it quite annoying that these problems do not make it clear which endianness should be used :(

For detailed solution to this problem, see the solution script.

```
chapt2> py .\p2.11.py
bit order: old --> new
cipher bits:
010011111100000110100010000011010011110000001
known plain bits:
101100111101000
known key stream bits:
111111000001000
init vec:
111111
A:
[[1, 1, 1, 1, 1, 1], [1, 1, 1, 1, 1, 0], [1, 1, 1, 1, 0, 0], [1, 1, 1, 0, 0, 0], [1, 1, 0, 0, 0, 0], [1, 0, 0, 0, 0, 0]]
b:
[0, 0, 0, 0, 0, 1]
x:
[1, 1, 0, 0, 0, 0]
poly:
[0, 1, 6]
recovered key stream bits:
111111000001000011000101001111010001110010010
recovered plain bits:
101100111101000101100111001100000010000010011
recovered plain text:
wpiwombat
```

(1) The initialization vector is `111111`.

(2) The feedback coefficients are $p_0=1,p_1=1,p_{2,3,4,5}=0$, the polynomial is `(0, 1, 6)`.

(3) The whole plain text is `wpiwombat`.

(4) [Wombats are short-legged, muscular quadrupedal marsupials that are native to Australia.](https://en.wikipedia.org/wiki/Wombat){:target='_blank'}

(5) Known-plaintext attack.

### 2.12

I implemented an ugly but straightforward version of Trivium using Python, based on the following diagram:

![MyTrivium]({{ site.github.url }}/assets/UnderstandingCrypto/chapt2/MyTrivium.png)

The solution script:

```python
class Trivium(object):
    def __init__(self, iv, key):
        self.A = iv & (2**80-1)
        self.B = key & (2**80-1)
        self.C = 0b111 << 108

    def gen_seq(self, n):
        def _bit(n, bit): return (n >> (bit-1)) & 1
        A, B, C = self.A, self.B, self.C
        outputs = []
        for r in range(n):
            Ao1 = _bit(A, 66)
            Ao2 = _bit(A, 93)
            Ao3 = _bit(A, 91) & _bit(A, 92)
            Ao = Ao1 ^ Ao2 ^ Ao3

            Bo1 = _bit(B, 69)
            Bo2 = _bit(B, 84)
            Bo3 = _bit(B, 82) & _bit(B, 83)
            Bo = Bo1 ^ Bo2 ^ Bo3

            Co1 = _bit(C, 66)
            Co2 = _bit(C, 111)
            Co3 = _bit(C, 109) & _bit(C, 110)
            Co = Co1 ^ Co2 ^ Co3

            output = Ao ^ Bo ^ Co
            outputs.append(output)

            Ai1 = _bit(A, 69)
            Ai2 = Co
            Ai = Ai1 ^ Ai2

            Bi1 = _bit(B, 78)
            Bi2 = Ao
            Bi = Bi1 ^ Bi2

            Ci1 = _bit(C, 87)
            Ci2 = Bo
            Ci = Ci1 ^ Ci2

            A = ((A << 1) & (2**93-1)) | Ai
            B = ((B << 1) & (2**84-1)) | Bi
            C = ((C << 1) & (2**111-1)) | Ci

        self.A, self.B, self.C = A, B, C
        return outputs


trivium = Trivium(0, 0)
seq = trivium.gen_seq(70)
print(seq)
```

It outputs as follows:

```
chapt2> py .\p2.12.py
[0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 0]
```

So the first 70 bits are $s_0=0,s_{1,2}=1,s_{3,4,...,66}=0,s_{67,68}=1,s_{69}=0$.

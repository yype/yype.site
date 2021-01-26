---
layout: post
title: Solutions to Understanding Cryptography Problems - Chapter 1
description: 
summary: 
tags: [Crypto, Understanding Cryptography]
---

* TOC
{:toc}

## Intro

My solutions to the practice problems in the book "Understanding Cryptography: A Textbook for Students and Practitioners - Chapter 1". 

The solution scripts to some of the problems have been uploaded to GitHub, you can check them out at: [https://github.com/yype/UnderstandingCrypto](https://github.com/yype/UnderstandingCrypto){:target='_blank'}.

## Problems

### 1.1

(1) Computer the relative frequency of all letters in the ciphertext.

```
[('r', 0.13003095975232198), ('b', 0.10526315789473684), ('m', 0.09597523219814241), ('k', 0.07585139318885449), ('j', 0.07430340557275542), ('w', 0.07275541795665634), ('i', 0.06346749226006192), ('p', 0.04643962848297214), ('u', 0.03715170278637771), ('d', 0.03560371517027864), ('h', 0.03560371517027864), ('v', 0.034055727554179564), ('x', 0.030959752321981424), ('y', 0.029411764705882353), ('n', 0.02631578947368421), ('s', 0.02631578947368421), ('t', 0.020123839009287926), ('l', 0.01238390092879257), ('q', 0.010835913312693499), ('o', 0.010835913312693499), ('e', 0.007739938080495356), ('a', 0.007739938080495356), ('c', 0.007739938080495356), ('f', 0.0015479876160990713), ('g', 0.0015479876160990713)]
```

(2) Decrypt the ciphertext.

```
because the practice of the basic movements of kata is
the focus and mastery of self is the essence of
matsubayashi ryu karate do i shall try to elucidate the
movements of the kata according to my interpretation
based on forty years of study

it is not an easy task to explain each movement and its
significance and some must remain unexplained to give a
complete explanation one would have to be qualified and
inspired to such an extent that he could reach the state
of enlightened mind capable of recognizing soundless
sound and shapeless shape i do not deem myself the final
authority but my experience with kata has left no doubt
that the following is the proper application and
interpretation i offer my theories in the hope that the
essence of okinawan karate will remain intact
```

The decryption process involves a little bit of manual tweaking, as can be seen in the solution script output:

```
Freqs in the text -> freqs of letters in the alphabet
('r', 0.13003095975232198) -> ('e', 0.127)
('b', 0.10526315789473684) -> ('t', 0.0906)
('m', 0.09597523219814241) -> ('a', 0.0817)
('k', 0.07585139318885449) -> ('o', 0.0751)
('j', 0.07430340557275542) -> ('i', 0.0697)
('w', 0.07275541795665634) -> ('n', 0.0675)
('i', 0.06346749226006192) -> ('s', 0.0633)
('p', 0.04643962848297214) -> ('h', 0.0609)
('u', 0.03715170278637771) -> ('r', 0.0599)
('d', 0.03560371517027864) -> ('d', 0.0425)
('h', 0.03560371517027864) -> ('l', 0.0403)
('v', 0.034055727554179564) -> ('c', 0.0278)
('x', 0.030959752321981424) -> ('u', 0.0276)
('y', 0.029411764705882353) -> ('m', 0.02414)
('n', 0.02631578947368421) -> ('w', 0.0236)
('s', 0.02631578947368421) -> ('f', 0.0223)
('t', 0.020123839009287926) -> ('g', 0.0202)
('l', 0.01238390092879257) -> ('y', 0.0197)
('q', 0.010835913312693499) -> ('p', 0.0193)
('o', 0.010835913312693499) -> ('b', 0.015)
('e', 0.007739938080495356) -> ('v', 0.0098)
('a', 0.007739938080495356) -> ('k', 0.0077)
('c', 0.007739938080495356) -> ('j', 0.0015)
('f', 0.0015479876160990713) -> ('x', 0.0015)
('g', 0.0015479876160990713) -> ('q', 0.001)

Trying to decipher:
yecawse the fractnce iu the yasnc mivemeots iu pata ns
the uicws aod masterg iu selu ns the esseoce iu
matswyagashn rgw parate di n shall trg ti elwcndate the
mivemeots iu the pata accirdnob ti mg noterfretatnio
yased io uirtg gears iu stwdg

nt ns oit ao easg tasp ti ekflano each mivemeot aod nts
snbonuncaoce aod sime mwst remano woekflanoed ti bnve a
cimflete ekflaoatnio ioe jiwld have ti ye xwalnuned aod
nosfnred ti swch ao ekteot that he ciwld reach the state
iu eolnbhteoed mnod cafayle iu recibonqnob siwodless
siwod aod shafeless shafe n di oit deem mgselu the unoal
awthirntg ywt mg ekferneoce jnth pata has leut oi diwyt
that the uillijnob ns the frifer afflncatnio aod
noterfretatnio n iuuer mg theirnes no the hife that the
esseoce iu ipnoajao parate jnll remano notact

Doing corrections manually:
l -> y ==> l -> b
        Conflit: l -> b
        Conflit: o -> b
q -> p ==> q -> k
        Conflit: l -> b
        Conflit: q -> k
        Conflit: o -> b
        Conflit: a -> k
t -> g ==> t -> y
        Conflit: l -> b
        Conflit: q -> k
        Conflit: o -> b
        Conflit: a -> k
n -> w ==> n -> u
        Conflit: x -> u
        Conflit: n -> u
        Conflit: l -> b
        Conflit: q -> k
        Conflit: o -> b
        Conflit: a -> k
w -> n ==> w -> i
        Conflit: j -> i
        Conflit: w -> i
        Conflit: x -> u
        Conflit: n -> u
        Conflit: l -> b
        Conflit: q -> k
        Conflit: o -> b
        Conflit: a -> k
j -> i ==> j -> o
        Conflit: k -> o
        Conflit: j -> o
        Conflit: x -> u
        Conflit: n -> u
        Conflit: l -> b
        Conflit: q -> k
        Conflit: o -> b
        Conflit: a -> k
k -> o ==> k -> n
        Conflit: x -> u
        Conflit: n -> u
        Conflit: l -> b
        Conflit: q -> k
        Conflit: o -> b
        Conflit: a -> k
s -> f ==> s -> p
        Conflit: x -> u
        Conflit: n -> u
        Conflit: l -> b
        Conflit: q -> k
        Conflit: o -> b
        Conflit: a -> k
c -> j ==> c -> w
        Conflit: x -> u
        Conflit: n -> u
        Conflit: l -> b
        Conflit: q -> k
        Conflit: o -> b
        Conflit: a -> k
x -> u ==> x -> f
        Conflit: l -> b
        Conflit: q -> k
        Conflit: o -> b
        Conflit: a -> k
a -> k ==> a -> x
        Conflit: l -> b
        Conflit: o -> b
        Conflit: a -> x
        Conflit: f -> x
o -> b ==> o -> g
        Conflit: a -> x
        Conflit: f -> x
g -> q ==> g -> z
        Conflit: a -> x
        Conflit: f -> x
f -> x ==> f -> q
        Check ok

Corresponding words:
[('lrvmnir', 'because'), ('bpr', 'the'), ('sumvbwvr', 'practice'), ('jx', 'of'), ('bpr', 'the'), ('lmiwv', 'basic'), ('yjeryrkbi', 'movements'), ('jx', 'of'), ('qmbm', 'kata'), ('wi', 'is'), ('bpr', 'the'), ('xjvni', 'focus'), ('mkd', 'and'), ('ymibrut', 'mastery'), ('jx', 'of'), ('irhx', 'self'), ('wi', 'is'), ('bpr', 'the'), ('riirkvr', 'essence'), ('jx', 'of'), ('ymbinlmtmipw', 'matsubayashi'), ('utn', 'ryu'), ('qmumbr', 'karate'), ('dj', 'do'), ('w', 'i'), ('ipmhh', 'shall'), ('but', 'try'), ('bj', 'to'), ('rhnvwdmbr', 'elucidate'), ('bpr', 'the'), ('yjeryrkbi', 'movements'), ('jx', 'of'), ('bpr', 'the'), ('qmbm', 'kata'), ('mvvjudwko', 'according'), ('bj', 'to'), ('yt', 'my'), ('wkbrusurbmbwjk', 'interpretation'), ('lmird', 'based'), ('jk', 'on'), ('xjubt', 'forty'), ('trmui', 'years'), ('jx', 'of'), ('ibndt', 'study'), ('wb', 'it'), ('wi', 'is'), ('kjb', 'not'), ('mk', 'an'), ('rmit', 'easy'), ('bmiq', 'task'), ('bj', 'to'), ('rashmwk', 'explain'), ('rmvp', 'each'), ('yjeryrkb', 'movement'), ('mkd', 'and'), ('wbi', 'its'), ('iwokwxwvmkvr', 'significance'), ('mkd', 'and'), ('ijyr', 'some'), ('ynib', 'must'), ('urymwk', 'remain'), ('nkrashmwkrd', 'unexplained'), ('bj', 'to'), ('ower', 'give'), ('m', 'a'), ('vjyshrbr', 'complete'), ('rashmkmbwjk', 'explanation'), ('jkr', 'one'), ('cjnhd', 'would'), ('pmer', 'have'), ('bj', 'to'), ('lr', 'be'), ('fnmhwxwrd', 'qualified'), ('mkd', 'and'), ('wkiswurd', 'inspired'), ('bj', 'to'), ('invp', 'such'), ('mk', 'an'), ('rabrkb', 'extent'), ('bpmb', 'that'), ('pr', 'he'), ('vjnhd', 'could'), ('urmvp', 'reach'), ('bpr', 'the'), ('ibmbr', 'state'), ('jx', 'of'), ('rkhwopbrkrd', 'enlightened'), ('ywkd', 'mind'), ('vmsmlhr', 'capable'), ('jx', 'of'), ('urvjokwgwko', 'recognizing'), ('ijnkdhrii', 'soundless'), ('ijnkd', 'sound'), ('mkd', 'and'), ('ipmsrhrii', 'shapeless'), ('ipmsr', 'shape'), ('w', 'i'), ('dj', 'do'), ('kjb', 'not'), ('drry', 'deem'), ('ytirhx', 'myself'), ('bpr', 'the'), ('xwkmh', 'final'), ('mnbpjuwbt', 'authority'), ('lnb', 'but'), ('yt', 'my'), ('rasruwrkvr', 'experience'), ('cwbp', 'with'), ('qmbm', 'kata'), ('pmi', 'has'), ('hrxb', 'left'), ('kj', 'no'), ('djnlb', 'doubt'), ('bpmb', 'that'), ('bpr', 'the'), ('xjhhjcwko', 'following'), ('wi', 'is'), ('bpr', 'the'), ('sujsru', 'proper'), ('msshwvmbwjk', 'application'), ('mkd', 'and'), ('wkbrusurbmbwjk', 'interpretation'), ('w', 'i'), ('jxxru', 'offer'), ('yt', 'my'), ('bprjuwri', 'theories'), ('wk', 'in'), ('bpr', 'the'), ('pjsr', 'hope'), ('bpmb', 'that'), ('bpr', 'the'), ('riirkvr', 'essence'), ('jx', 'of'), ('jqwkmcmk', 'okinawan'), ('qmumbr', 'karate'), ('cwhh', 'will'), ('urymwk', 'remain'), ('wkbmvb', 'intact')]

Deciphered text:
because the practice of the basic movements of kata is
the focus and mastery of self is the essence of
matsubayashi ryu karate do i shall try to elucidate the
movements of the kata according to my interpretation
based on forty years of study

it is not an easy task to explain each movement and its
significance and some must remain unexplained to give a
complete explanation one would have to be qualified and
inspired to such an extent that he could reach the state
of enlightened mind capable of recognizing soundless
sound and shapeless shape i do not deem myself the final
authority but my experience with kata has left no doubt
that the following is the proper application and
interpretation i offer my theories in the hope that the
essence of okinawan karate will remain intact

Key maps:
rbmkjwipudhvxynstlqoeacfg
etanoishrdlcfmupybkgvxwqz
r 0.13003095975232198 -> e 0.127
b 0.10526315789473684 -> t 0.0906
m 0.09597523219814241 -> a 0.0817
k 0.07585139318885449 -> n 0.0675
j 0.07430340557275542 -> o 0.0751
w 0.07275541795665634 -> i 0.0697
i 0.06346749226006192 -> s 0.0633
p 0.04643962848297214 -> h 0.0609
u 0.03715170278637771 -> r 0.0599
d 0.03560371517027864 -> d 0.0425
h 0.03560371517027864 -> l 0.0403
v 0.034055727554179564 -> c 0.0278
x 0.030959752321981424 -> f 0.0223
y 0.029411764705882353 -> m 0.02414
n 0.02631578947368421 -> u 0.0276
s 0.02631578947368421 -> p 0.0193
t 0.020123839009287926 -> y 0.0197
l 0.01238390092879257 -> b 0.015
q 0.010835913312693499 -> k 0.0077
o 0.010835913312693499 -> g 0.0202
e 0.007739938080495356 -> v 0.0098
a 0.007739938080495356 -> x 0.0015
c 0.007739938080495356 -> w 0.0236
f 0.0015479876160990713 -> q 0.001
g 0.0015479876160990713 -> z 0.0007
```

(3) It's from "Essence of Okinawan Karate-Do" by Shoshin Nagamine.

### 1.2

Decipher the following ciphertext encoded with a shift cipher:

```
xultpaajcxitltlxaarpjhtiwtgxktghidhipxciwtvgtpilpit
ghlxiwiwtxgqadds.
```

(1) First we calculate the frequency of each letter in the ciphertext:

```
[('t', 10), ('i', 9), ('x', 7), ('l', 5), ('p', 5), ('a', 5), ('g', 5), ('h', 4), ('w', 4), ('d', 3), ('j', 2), ('c', 2), ('u', 1), ('r', 1), ('k', 1), ('v', 1), ('q', 1), ('s', 1)]
```

As can be seen, letter `t` appears mostly in the ciphertext, which corresponds to the mostly appearing letter `e` in the alphabet. So now we know `e` has probably been transformed to `t`, and the shift number is `ord('t') - ord('e')` which is 15. After trying 15 as the shift number, I successfully deciphered the ciphertext, so I identified only one letter.

(2) Using 15 as the shift number:

```
ifweallunitewewillcausetheriverstostainthegreatwate
rswiththeirblood.
```

After manually inserting some spaces between words:

```
if we all unite we will cause the rivers to stain the great waters with their blood.
```

(3) It's from Tecumseh's speech to the Osages.

### 1.3

(1) ASICs that can run in parallel:

$$
\frac{1000000}{(50\times2)}=10000
$$

Seconds it takes to do an average key search:

$$
\frac{2^{128-1}}{(10000\times5\times10^8)}=3.40282\times 10^{25} \\
3.40282\times 10^{25} secs \approx 1.07903\times 10^{18} years
$$

This is much longer than the age of the universe ($\approx10^8$ times).

(2) Represent the number of Moore's Law iterations as $x$, we can get the following equation:

$$
\frac{2^{128-1}}{\frac{1000000}{(50\times2)}\times(5\times10^8)\times2^x}=24\times60\times60
\\
x=68.4162
\\
\frac{18}{12}\times68.4162\approx102.624\ (years)
$$

So it'll take approximately 103 years.

### 1.4

(1) Size of the key space that can be constructed be the specified passwords:

$$
128^8=2^{56}
$$

(2) The corresponding key length in bits: $\log_2{2^{56}}=56$.

(3) The corresponding key length in bits when users use only the 26 lowercase letters from the alphabet:

$$
\log_2{26^8}\approx37.6035\approx38
$$

(4) a. 7-bit characters:

$$
128^x=2^{128}\\
(2^7)^x=2^{7x}=2^{128}\\
x\approx18.3
$$

So it at least requires 19 characters.

(4) b. 26 lowercase letters from the alphabet:

$$
26^x=2^{128}\\
x=\log_{26}{2^{128}}\\
x\approx27.2
$$

So at least 28 characters are required.

### 1.5

(1) $15 \cdot 29 \equiv 2\cdot3 \equiv 6 \bmod 13$

(2) $2 \cdot 29 \equiv 2 \cdot 3 \equiv 6 \bmod 13$

(3) $2 \cdot 3 \equiv 6 \bmod 13$

(4) $-11 \cdot 3 \equiv 2 \cdot 3 \equiv 6 \bmod 13$

All members in the same equivalence class behave the same.

### 1.6

(1) $1/5\bmod13=8$

(2) $1/5\bmod7=3$

(3) $3\cdot2/5\bmod7=4$

### 1.7

(1) $\mathbb{Z}_4$, multiplication table:

$$
\begin{array}{c|c c c c} 
\times
  & 0 & 1 & 2 & 3 \\ 
\hline 
0 & 0 & 0 & 0 & 0 \\ 
1 & 0 & 1 & 2 & 3 \\ 
2 & 0 & 2 & 0 & 2 \\ 
3 & 0 & 3 & 2 & 1 \\ 
\end{array}
$$

(2) $\mathbb{Z}_5$, addition and multiplication table:

$$
\begin{array}{c|c c c c c} 
+ & 0 & 1 & 2 & 3 & 4 \\ 
\hline 
0 & 0 & 1 & 2 & 3 & 4 \\ 
1 & 1 & 2 & 3 & 4 & 0 \\ 
2 & 2 & 3 & 4 & 0 & 1 \\ 
3 & 3 & 4 & 0 & 1 & 2 \\ 
4 & 4 & 0 & 1 & 2 & 3 \\ 
\end{array}
\ \ 
\begin{array}{c|c c c c c} 
\times
  & 0 & 1 & 2 & 3 & 4 \\ 
\hline 
0 & 0 & 0 & 0 & 0 & 0 \\ 
1 & 0 & 1 & 2 & 3 & 4 \\ 
2 & 0 & 2 & 4 & 1 & 3 \\ 
3 & 0 & 3 & 1 & 4 & 2 \\ 
4 & 0 & 4 & 3 & 2 & 1 \\ 
\end{array}
$$

(3) $\mathbb{Z}_6$, addition and multiplication table:

$$
\begin{array}{c|c c c c c c} 
+ & 0 & 1 & 2 & 3 & 4 & 5 \\ 
\hline 
0 & 0 & 1 & 2 & 3 & 4 & 5 \\ 
1 & 1 & 2 & 3 & 4 & 5 & 0 \\ 
2 & 2 & 3 & 4 & 5 & 0 & 1 \\ 
3 & 3 & 4 & 5 & 0 & 1 & 2 \\ 
4 & 3 & 4 & 5 & 0 & 2 & 3 \\ 
5 & 3 & 4 & 5 & 0 & 3 & 4 \\ 
\end{array}
\ \ 
\begin{array}{c|c c c c c c} 
\times
  & 0 & 1 & 2 & 3 & 4 & 5 \\ 
\hline 
0 & 0 & 0 & 0 & 0 & 0 & 0 \\ 
1 & 0 & 1 & 2 & 3 & 4 & 5 \\ 
2 & 0 & 2 & 4 & 0 & 2 & 4 \\ 
3 & 0 & 3 & 0 & 3 & 0 & 3 \\ 
4 & 0 & 4 & 2 & 0 & 4 & 2 \\ 
5 & 0 & 5 & 4 & 3 & 2 & 1 \\ 
\end{array}
$$

(4) a. In $\mathbb{Z}_4$, 0 and 2 do not have a multiplicative inverse. In $\mathbb{Z}_6$, 0, 2, 3 and 4 do not have a multiplicative inverse.

(4) b. 5 is a prime number. All non-zero numbers smaller than 5 are coprime to 5.

### 1.8

$$
5\times9\equiv1\bmod11\to5^{-1}\equiv9\bmod11\\
5\times5\equiv1\bmod12\to5^{-1}\equiv5\bmod12\\
5\times8\equiv1\bmod13\to5^{-1}\equiv8\bmod13\\
$$

### 1.9

(1) $x=3^2\bmod13\equiv9\bmod13$

(2) $x=7^2 \bmod 13 \equiv 10 \bmod 13$

(3) 

$$
\begin{aligned}
x&=3^{10}\bmod 13\\
&=3^9\times3\bmod13\\
&=27^3\times3\bmod13\\
& =(13\times2+1)^3\times3\bmod13\\
&\equiv1^3\times3\bmod13\\
&\equiv 3 \bmod 13
\end{aligned}
$$

(4) 

$$
\begin{aligned}
x&=7^{100}\bmod13\\
&=(7^2)^{50}\bmod13\\
&=(13\times3+10)^{50}\bmod13\\
&\equiv10^{50}\bmod13\\
&=100^{25}\bmod13\\
&=(13\times7+9)^{25}\bmod 13\\
&\equiv9^{25}\bmod13\\
&=81^{12}\times9\bmod13\\
&=(13\times6+3)^{12}\times9\bmod13\\
&\equiv3^{14}\bmod13\\
&=(3^4)^3\times3^2\bmod13\\
&\equiv3^3\times3^2\bmod13\\
&\equiv3\times3\bmod13\\
&=9\bmod13
\end{aligned}
$$

(5) Through trial & error:

$$
7^x=11\bmod13\\
x=5
$$

### 1.10

$$
\phi(4)=2,\phi(5)=4,\phi(9)=6,\phi(26)=12
$$

### 1.11

(1) See the solution script:

```python
# enc: f = 7*x+22 mod 26
# dec: x = (f-22)*inv(7,26) mod 26
#        = (f-22)*15 mod 26
e = 'falszztysyjzyjkywjrztyjztyynaryjkyswarztyegyyj'

m = ''
for each in e:
    each = ord(each) - ord('a')
    each -= 22
    each *= 15
    each %= 26
    each = chr(ord('a') + each)
    m += each

print(m)
# first the sentence and then the evidence said the queen
```

(2) Lewis Carroll wrote it in one of his poems.

### 1.12

(1)

$$
e_k(x)=y=a\cdot x+b\bmod30\\
d_k(y)=x=a^{-1}\cdot(y-b)\bmod 30\\
\text{Key and Requirements:} \\
(k=(a,b)\ \&\ gcd(a,30)=1)
$$

(2)

$$
\phi(30)\times30=8\times30=240
$$

(3) Transform the ciphertext into bias-in-the-alphabet form: `(26,20,29,22,29)`

$$
17^{-1}\equiv23\bmod30\to d(y)=(y-1)\times23\bmod30\\
d(26,20,29,22,29)=(5,17,14,3,14)
$$

Transform it back, we get the plaintext: `FRODO`.

(4) [Frodo](https://en.wikipedia.org/wiki/Frodo_Baggins){:target='_blank'} comes from The Shire (Lord Of The Rings character).

### 1.13

$$
\begin{cases}
y_1=ax_1+b\\
y_2=ax_2+b
\end{cases}
\Rightarrow 
\begin{cases}
b=y_1-ax_1\\
a=(y_2-y_1)(x_2-x_1)^{-1}
\end{cases}
\pmod N
\\
\text{(N is the size of the alphabet being encrypted)}\\
\text{}\\
\text{We should make sure that: }gcd(x_2-x_1,N)=1\\
\text{so that }(x_2-x_1)^{-1}\bmod{N} \text{ exists}.
$$

### 1.14

(1) 

$$
e_{k2}(e_{k1}(x))=a_2(a_1x+b_1)+b_2=a_1a_2x+a_2b_1+b_2\pmod{26}\\
\text{let }k_3\equiv(a_1a_2,a_2b_1+b_2)\pmod{26}\\
\text{then }e_{k3}=e_{k2}(e_{k1}(x))
$$

(2)

$$
a_3=a_1a_2\bmod26=33\bmod26\equiv7\bmod26\\
b_3=a_2b_1+b_2\bmod26=11\times5+7\bmod26\equiv10\bmod26\\
\Rightarrow(a_3,b_3)\equiv(7,10)\bmod26
$$

(3) See the solution script:

```python
def enc_k1(x):
    return (3*x+5) % 26


def enc_k2(x):
    return (11*x+7) % 26


def enc_k3(x):
    return (7*x+10) % 26


for n in range(26):
    if enc_k2(enc_k1(n)) != enc_k3(n):
        print('Error!')
        exit(1)

print('Verified')
```

Output:

```
Verified
```

(4) Searching all $(k_1\in K,k_2\in K)$ is equivalent to just searching all $k_3\in K$. The keyspace does not increase at all by the specified double encryption.

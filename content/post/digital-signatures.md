---
date: "2021-08-12T00:00:00Z"
title: Digital Signatures
tags:
  - programming
  - cryptography
---

## Introduction

In the last article we have seen the essentials of elliptic curve cryptography
I promised that we would see about digital signatures in the next article.
Without digital signatures, cryptocurrencies as we know won't exist.

### Digital Signatures

Let's say you want to prove to someone that you are the author of certain message,
how do you do it using public-key cryptography. Turns out you can digitally sign
the message to prove that you indeed published the message. In short, digital
signatures can be used to verify that the author or the origin of a message is
indeed a legitimate user.

### How do you digitally sign a message?

Let's say a prover wants to prove a message to a verifier who verifies the message.
It just so happens that the names of the prover and verifier are Alice and Bob
respectively. We need to come up with an algorithm that can do this. To start off we
need a base point `G` that is publicly agreed upon. Here is how the algorithm goes:

<!--- Make sure to mention that the randomly chosen point must be in the order of the curve. --->

1. Alice generates a private key `privKey` and keeps it a secret.
2. She then calculates her public key `pubKey` by multiplying `privKey`
   with the base point `G`.
3. She then sends her public key `pubKey` to Bob.
4. Alice then chooses another random number `r` just like she chose her `privKey`.
5. She then computes a new point `R` by multiplying `r` and `G`.
6. She also computes a number `sum` by adding the two numbers `privKey` and `r`.
7. She then sends them off to Bob for him to verify.
<!--- TODO: link article --->
8. Bob then verifies that `sum * G == pubKey + R`. (this is possible because its
   a property of elliptic curves, if you are not familiar check out my previous article
   on the same).

But this algorithm allows someone else to trick Bob without knowing Alice's private
key `privKey`. Give it a thought on how one can possibly deceive Bob.

Since the other person knows that Bob will calculate `sum * G == pubKey + R` in order to verify,
she can just compute `R` as follows `R = r*G - pubKey` and just set `sum = r`. Now the
equation works out to be `sum * G == pubKey + r*G - pubKey` (once we replace`sum` with `r` and
expand `R`) and that in turn works out to `r*G == r*G`

Well it's really sad that we can cheat using this algorithm. But there is a fix.
The fix is that instead of adding `pubKey` to `R` once, Bob chooses to add `pubKey`
`x` times to `R`. So in the updated algorithm Bob calculates RHS as follows `(x*pubKey) + R`,
and then send Alice the number `x` so that she can calculate and send back the number
`sum = (x*privkey) + r` and Bob will compute the LHS as follows `sum * G` and verify that it
is indeed Alice. The updated algorithm would be:

1. Alice chooses a random number `r` and send the point `R` by calculating `r*G`.
2. Bob then chooses a number `x` and send it to Alice.
3. Alice knows that Bob would add `pubKey` to `R` `x` times, so she does the same with the numbers,
   she calculates `sum = (x*privKey) + r`. She then sends the number `sum` to Bob.
4. Bob then verifies by computing `sum * G == (x*pubKey) + R`.

<!--- TODO: link preimage resistance property maybe? --->
It's really not practical to communicate these many times while verifying. So to make the process
a little more non-interactive the number `x` is usually set to the hash of the point `R`. But the
hash function should be deemed good so that others can't trick the hash function. Note that Alice
can't compute `x` efficiently if the hash function is deemed good, this is because of the preimage
resistance property of hash functions. Meaning she can't cheat because `x = hash(R)` and to compute
a fake `R` that cancels out `pubKey` in the equation she needs to a number which is dependent on the
point `R`.

### References

1. [Explain like I'm 5 - digital signatures](https://blog.oleganza.com/post/162861219668/eli5-digital-signatures)
2. [Implementation of signing in the python-ecdsa library](https://github.com/tlsfuzzer/python-ecdsa/blob/master/src/ecdsa/ecdsa.py#L212)
3. [Elliptic Curve Cryptography from Hackernoon](https://hackernoon.com/what-is-the-math-behind-elliptic-curve-cryptography-f61b25253da3)
4. [Interesting history of the name Alice and Bob](http://cryptocouple.com/)

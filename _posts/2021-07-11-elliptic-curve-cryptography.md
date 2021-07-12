---
layout: post
title: A Primer on Elliptic Curve Cryptography
---
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

## Introduction

If you have used Bitcoin, Ethereum or any other cryptocurrency you would be familiar with public and private keys and if you are more savvy
person you might have also heard about ECC(Elliptic Curve Cryptography) or ECDSA(Elliptic Curve Digital Signature Algorithm). Today we will
find out what exactly ECC is and how and why it's used in case of Bitcoin and maybe other cryptocurrencies.

<br>
## Brief History on Public-Key Cryptography

Public key cryptography is not new, it's been around for a long time. The first public key cryptography algorithm was
[Diffie-Hellman](https://cryptography.fandom.com/wiki/Diffie%E2%80%93Hellman_key_exchange) ECC is just one way of doing public-key cryptography.
It was invented by two guys named Whitfield Diffie and Martin Hellman. Apparently Hellman was obsessed with the problem of how do I send my friend
an encrypted message? Well it's easy to send the encrypted message but how do I send the decryption key to the encrypted message. I could encrypt the
decryption key, but then how do I send him the key to decrypt the decryption key? As you can see this quickly becomes a recursive problem that never
ends. Hellman then teamed up with Diffie and went across the country to see him. After approximately 4 years, in 1976 they came up with the Diffie–Hellman
key exchange algorithm. So Diffie-Hellman is first of it's kind. There are several others now, to name a few there are ElGamal, Paillier cryptosystem and
RSA.

Fun fact: You use the RSA algorithm in your daily life because the HTTPS protocol uses RSA encryption. Without HTTPS your WhatsApp conversations
wouldn't be secure(not that they are secure to begin with because Facebook is spying on you, but that's maybe for another day) and web security in general
would be a security nightmare.

<br>
## Properties of Public-Key Cryptography Systems

Why does Bitcoin use asymmetric-key cryptography? Well, asymmetric keys offer 2 properties:

1. Public and private keys - unlike symmetric-key cryptography you have a private and a public key - the public key is used to identify you publicly
and the private key, as the name implies is kept safe. If your private key is lost or compromised then the attacker has full control over you funds.
Also note that it's computationally unfeasible to work your way back to a private key given a public key. Functions that have this characteristic,
easy to compute one way but hard to compute the other way, are called [Trapdoor](https://cryptography.fandom.com/wiki/Trapdoor_function) functions. 
More formally, given a function `f(x)` it's hard to find <code>f<sup>'</sup>(x)</code>.

2. Sign and verify messages - this lets you prove that you are the owner of the corresponding public key without actually giving away the private key.
This lets you sign transactions which is then verified by the miners that the transaction actually came from you.

Okay enough talk, let's dive into the mathematics of elliptic curves.

<br>
## Elliptic Curves

Elliptic curves generally have a mathematical form of <code>y<sup>2</sup> = x<sup>3</sup> + ax + b</code>, so any point on the curve will satisfy this
equation. Your public key is just a point on this curve. In case of Bitcoin, it uses a standard called [secp256k1](https://www.secg.org/sec2-v2.pdf)
in which `a=0` and `b=7` so the equation ends up as <code>y<sup>2</sup> = x<sup>3</sup> + 7</code>. See Bitcoin [wiki](https://en.bitcoin.it/wiki/Secp256k1)
for more info. 

Elliptic curves are symmetric along their x-axis - this means that given point `x` on the x-axis, the corresponding points on the y-axis are `y` and `-y`.
This property is smartly used in Bitcoin addresses to save transaction fees. Originally Bitcoin used "uncompressed" public keys in which your public key 
is both the `x` and `y` coordinate of a point. But later, using this property, it introduced a new "compressed" key format that lets you save data (as 
the public key is reduced to half of it's size by omiting the y coordinate) and thus save transaction fees. See 
[bitcoin-book](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch04.asciidoc#compressed-public-keys) on how compressed keys are represented.

<center>
    <img src="/assets/elliptic-curves.jpg" alt="elliptic-curves" />
    <figcaption>Image showing different elliptic curves.</figcaption>
</center>

<br>
### Elliptic Curves over a Finite Field:

Bitcoin public keys are computed by multiplying a [base point](https://en.bitcoin.it/wiki/Secp256k1#Technical_details), usually denoted by `G`, that is known
to everybody times your private key (a 256-bit number). When you multiply a point `P` on the curve by a number `n`, it is not always guaranteed to be less
than 2<sup>256</sup>. Why should it be less than 2<sup>256 </sup> you ask? Well there are always standards, or more like limits, on how big the number can be.
In case of Bitcoin and most other use cases the limit is 2<sup>256</sup>. Bitcoin's public keys are 512 bits long (256 for the x-coordinate and 256 for the
y-coordinate).

Note: I'm only talking about the points associated with an uncompressed public key, the actual public keys are more than 512 bits long, this
is because you encode some information along with the points.

So in order for the public keys to fit the 512 bit length obviously the each coordinate should be less than or equal to 2<sup>256</sup>. So how do we ensure
that the computed number is always less than 2<sup>256</sup>? The answer is that we compute the Elliptic Curve over a finite field. Finite Fields are beyond
the scope of this article so if you don't know what finite fields are then [this](https://www.youtube.com/watch?v=ColSUxhpn6A) would be a good place to start.
To be frank even I don't know finite fields to it's core, but I got the basics down. Calculating Elliptic Curves over a finite field would make sure that the
computed number is always less then 2<sup>256</sup>. A naive explanation of finite fields would be that we take the mod of the result comuted with respct to
a number `p`. This number `p` is usually prime. In case of secp256k1 this prime is the largest prime number that is less than 2<sup>256</sup>.


<br>
## Point Operations on Elliptic Curve

### Point Addition

Lets say you have 2 points `P` and `Q` on the elliptic curve `E` and you need to add these two points. How do you add two points on the elliptic curve? It
turns out that you need to find a line `L` that's passing through these 2 points. Once you have done that, there is always an other point `R` at which the
`L` intersects `E`. You then reflect that point `R` about x-axis, i.e flip the sign of y-coordinate of the point. This point is our 3rd point `R` that we
get by adding the 2 points `P` and `Q`. Lets visually see whats going on.
Images are taken from this hackernoon [article](https://hackernoon.com/what-is-the-math-behind-elliptic-curve-cryptography-f61b25253da3).

<br>
![point-addition](/assets/point-addition.png)

Mathematically adding is given as follows: (taken from [wikipedia](https://en.wikipedia.org/wiki/Elliptic_curve_point_multiplication#Point_addition))
<pre>
slope = (Q[y] – P[y]) / (Q[x] – P[x]) mod p # finite fields
R[x] = slope<sup>2</sup> – P[x] – Q[x] mod p
R[y] = slope * (P[x] – R[x]) – P[y] mod p
</pre>

<br>
### Point Doubling

Adding 2 points isn't always the case. Sometimes you need to add a point to itself. In that case we can't use the above formula because we can't find the slope
of a line using just one point and moreover there are infinite lines passing through a point `P`, so which one do we choose? Well we take the tangent line at
point `P`. Why tangent line you ask? Well you can still think of it as 2 points and that point `Q` is approaching `P` (infinitesimally closer to `P`) and now the
line becomes the tangent line at point `P`. If you have taken any calculus class before you might be familiar that the slope of an equation at any given point
is given by it's first derivative:

$$\frac{\mathrm{d}y}{\mathrm{d}x} = \frac{(3x^2 + a)}{2y}$$

Then the equation of the tangent line is given by:

$$y = mx + c \text{, where } m = \frac{(3x^2 + a)}{2y}$$

Now that we have the equation to the line, we substitue it in the equation of the curve to find where they coincide, note that `x1` and `y1` are coordinates of point `P`.

$$[m(x - x1) + y1]^2 = x^3 + ax + b \text{, where } m = \frac{(3x^2 + a)}{2y}$$

You then solve the equation to get point `R`. Now, let's visualize what's happening: (images are taken from this hackernoon
[article](https://hackernoon.com/what-is-the-math-behind-elliptic-curve-cryptography-f61b25253da3)).

<br>
![point-doubling](/assets/point-doubling.jpg)

Mathematically adding is given as follows: (taken from [wikipedia](https://en.wikipedia.org/wiki/Elliptic_curve_point_multiplication#Point_addition))
<pre>
slope = (3 P[x]<sup>2</sup> + a) / (2 P[y]) mod p
R[x] = slope<sup>2</sup> – 2P[x] mod p
R[y] = slope * (P[x] – R[x]) – P[y] mod p
</pre>

<br>
### Point Multiplication

Okay now how do we multiply a point with a scalar? Let's say you want to compute `n * P`. A naive approach would be to add that point to itself using the Point
Addition algorithm `n` number of times. But in real life applications these numbers are humungously large so it's not very practical. Turns out there are multiple
methods to do this, see [wikipedia](https://en.wikipedia.org/wiki/Elliptic_curve_point_multiplication#Point_multiplication) for all the different methods, in this
article we focus on the double-and-add method. The algorithm for double-and-add is as follows:
(taken from [wikipedia](https://en.wikipedia.org/wiki/Elliptic_curve_point_multiplication#Point_multiplication))

```python
def f(P, d):
    if d == 0:
        return 0                         # computation complete
    elif d == 1:
        return P
    elif d % 2 == 1:
        return point_add(P, f(P, d - 1)) # addition when d is odd
    else:
        return f(point_double(P), d/2)   # doubling when d is even
```
There is another implementation, of the same algorithm, on the [wikipedia](https://en.wikipedia.org/wiki/Elliptic_curve_point_multiplication#Point_multiplication) page,
but I find it a little confusing.  If you are a computer science student you might recognize that this algorithm is analogous to the
[binary exponentiation](https://cp-algorithms.com/algebra/binary-exp.html) algorithm. Turns out this is exactly the same, only that we are doubling and adding instead
of multiplying and squaring.

Fun fact that you don't need to know and is totally irrelevant: I _kind of_ came up with this on my own once I got to know that adding point `P` to itself `n` times 
is not practical (most probably because I was already familiar with binary exponentiation). Yeah, feels a little validating xD.

In the next article we will explore how Digital Signatures works - telling people that you know a number without actually telling them the number(if you get the reference
, lol).

<br>
#### References

1. [Elliptic Curve Cryptography - A Gentle Introduction](https://andrea.corbellini.name/2015/05/17/elliptic-curve-cryptography-a-gentle-introduction)
2. [Math behind Elliptic Curve Cryptography](https://hackernoon.com/what-is-the-math-behind-elliptic-curve-cryptography-f61b25253da3)
3. [ECDSA - learnmeabitcoin](https://learnmeabitcoin.com/technical/ecdsa#creating-a-public-key)
4. [Wyoming Elliptic Curve](https://www.math.brown.edu/johsilve/Presentations/WyomingEllipticCurve.pdf)
5. [Elliptic Curves from mae.ufl.edu](https://mae.ufl.edu/~uhk/elliptic-curves.pdf)
6. [Public Key - learnmeabitcoin](https://learnmeabitcoin.com/technical/public-key)
7. [Elliptic Curve - Point Multiplication](https://en.wikipedia.org/wiki/Elliptic_curve_point_multiplication)
8. [Order of a point on an Elliptic Curve](https://math.stackexchange.com/questions/704202/order-of-a-point-on-an-elliptic-curve)
9. [Intuition on point at infinity(the identity element)](https://math.stackexchange.com/questions/13763/elliptic-curves-and-points-at-infinity)
10. [A Diffie-Hellman Primer from UCLA](https://www.math.ucla.edu/~baker/40/handouts/rev_DH/node1.html)

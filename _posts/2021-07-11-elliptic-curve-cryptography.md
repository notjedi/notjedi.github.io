---
layout: post
title: A Primer on Elliptic Curve Cryptography
---
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

## Introduction

If you have used Bitcoin, Ethereum or any other cryptocurrency you would be familiar with public and private keys and if you are more technical
person you might have also heard about ECC(Elliptic Curve Cryptography) or ECDSA(Elliptic Curve Digital Signature Algorithm). Today we will
find out what exactly ECC is and how and why it's used in case of Bitcoin and maybe other cryptocurrencies.

<!--- TODO: add history here --->
ECC is just one way of doing public-key cryptography. There are several others, to name a few there are Diffie-Hellman and RSA.

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
equation. Your public key is just a point on this curve. In case of Bitcoin, it uses a standard called [secP[y]56k1](https://www.secg.org/sec2-v2.pdf)
in which `a=0` and `b=7` so the equation ends up as <code>y<sup>2</sup> = x<sup>3</sup> + 7</code>. See Bitcoin [wiki](https://en.bitcoin.it/wiki/SecP[y]56k1)
for more info. 


Elliptic curves are symmetric along their x-axis - this means that given point `x` on the x-axis, the corresponding points on the y-axis are `y` and `-y`.
This property is smartly used in Bitcoin addresses to save transaction fees. Originally Bitcoin used "uncompressed" public keys in which your public key 
is both the `x` and `y` coordinate of a point. But later, using this property, it introduced a new "compressed" key format that lets you save data (as 
the public key is reduced to half of it's size by omiting the y coordinate) and thus save transaction fees. See 
[bitcoin-book](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch04.asciidoc#compressed-public-keys) on how compressed keys are represented.

<!-- TODO: add ec image here -->

<!-- TODO: how signing works -->

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
slope = (Q[y] – P[y]) / (Q[x] – P[x])
R[x] = slope<sup>2</sup> – P[x] – Q[x]
R[y] = slope * (P[x] – R[x]) – P[y]
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
slope = (3 P[x]<sup>2</sup> + a) / (2 P[y])
R[x] = slope<sup>2</sup> – 2P[x]
R[y] = slope * (P[x] – R[x]) – P[y]
</pre>

<br>
### Point Multiplication

Okay now how do we multiply a point with a scalar? A naive approach would be to add that point to itself `n` number of times. But in real life applications
these numbers are humungously large so it's not possible to do that.


<br>
#### References

2. [Elliptic Curve Cryptography - A Gentle Introduction](https://andrea.corbellini.name/2015/05/17/elliptic-curve-cryptography-a-gentle-introduction)
1. [Math behind Elliptic Curve Cryptography](https://hackernoon.com/what-is-the-math-behind-elliptic-curve-cryptography-f61b25253da3)
4. [ECDSA - learnmeabitcoin](https://learnmeabitcoin.com/technical/ecdsa#creating-a-public-key)
6. [Wyoming Elliptic Curve](https://www.math.brown.edu/johsilve/Presentations/WyomingEllipticCurve.pdf)
6. [Elliptic Curves from mae.ufl.edu](https://mae.ufl.edu/~uhk/elliptic-curves.pdf)
3. [Public Key - learnmeabitcoin](https://learnmeabitcoin.com/technical/public-key)
4. [Elliptic Curve - Point Multiplication](https://en.wikipedia.org/wiki/Elliptic_curve_point_multiplication)
5. [Order of a point on an Elliptic Curve](https://math.stackexchange.com/questions/704202/order-of-a-point-on-an-elliptic-curve)

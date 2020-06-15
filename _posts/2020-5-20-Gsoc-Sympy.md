---
layout: post
title: Google Summer of Code 2020 with Sympy!
---

From my first year i always wanted to get selected for Gsoc and do some exiciting projects, but never took it seriously. When i reached  my third year i made up my mind that it is a high time for preparig for Gsoc. For Gsoc 2020 i choose to apply to Sympy as i found this community very intresting. I took a head start and started contributing for four months before the application period. It was a really nice experience and i meet some really interesting people. I am glad that i made the right decision of choosing this organization.

----
****

### PROJECT:

My project is based on implementing faster factorization algorithms in Sympy. Curently sympy uses exponential time factorization algoritms which strugles to factorize numbers of greater than ~20 digits. I will be implementing Lenstra's Elliptic Curve Factorization and the Self-Initializing Quadratic Sieve. These are sub-exponential time algorithms which can easily factor number of less than 50 digits within seconds.

### Week 1:

Finally i started my project. For 1st phase my main focus will be Elliptic curve factorization algorithm. I implemented a Point class whichstores point on the elliptic curve in Montgomery form. The sub methods of this class were doubling, addition and montgomery ladder algorithm for scalar multiplication of points. These methods use fast montgomery arthematic by ignoring the y coordinate and perform the calculations by using x and z coordinate only. 
<br><br>
The `Point` class : This class represents Montgomery form of points in an elliptic curve. The 
methods in this class includes addition, doubling and scalar multiplication of Points. For computing these methods we only need the x and z coordinate of the point thus decreasing the total number of operations and make the ECM faster. The addition of points requires 6 multiplications provided we already know the difference of the points. The doubling method requires 5 multiplications in total.
For scalar multiplication i used the Montgomery Ladder Algorithm and each step of this algorithm requires 11 multiplications. This algorithm is contructed in such a way that the difference between the intermediate points P and Q, `P - Q` is always constant and equals to the value of the initil point P0.
<br><br>
Next i implemented `ecm_one_factor`. This function returns one factor of a number using Lenstra's 2 Stage Elliptic curve Factorization with Suyama's Parameterization. Here Montgomery arithmetic is used for fast computation of addition and doubling of points in elliptic curve. 
<br><br>
Given a composite number with `gcd(n, 6) = 1`, this ECM algorithm attempts to find a non-trivial factor of `n`. This algorithm is inversion less. For generating a random elliptic curve i used Suyuma's parameterization. This ensures that te order of the curve is divisible by `12` and moreover either on `E` or a twist `E'` there exist a point whose x-coordinate is `u^3v^-3`. The curve 
`E : y**2 = x**3 + C(σ)*x**2 + x` where `C` depends on field parameter `σ != 0, 1, 5` according to
`u = σ**2 - 5`, `v = 4σ` and `C(σ) = (v - u)**3 (3u + v) / 4*u**3*v - 2`.
This ECM method considers elliptic curves in Montgomery form `(E : b*y**2*z = x**3 + a*x**2*z + x*z**2)` and involves elliptic curve operations `(mod N)`, where the elements in `Z` are reduced `(mod N)`. Since `N` is not a prime, `E` over `FF(N)` is not really an elliptic curve but we can still do point additions and doubling as if `FF(N)` was a field.
<br><br>
Stage 1 : The basic algorithm involves taking a random point `(P)` on an elliptic curve in `FF(N)`. The compute `k*P` using Montgomery ladder algorithm. Let `q` be an unknown factor of `N`. Then the order of the curve `E`, `|E(FF(q))|`, might be a smooth number that divides `k`. Then we have `k = l * |E(FF(q))|` for some `l`. For any point belonging to the curve `E`, `|E(FF(q))|*P = O`, hence 
`k*P = l*|E(FF(q))|*P`. Thus `kP.z_cord = 0 (mod q)`, and the unknownn factor of `N(q)` can be recovered by taking `gcd(kP.z_cord, N)`.
<br><br>
Stage 2 : This is a continuation of Stage 1 if `k*P != O`. The idea utilize the fact that even if 
`kP != 0`, the value of `k` might miss just one large prime divisor of `|E(FF(q))|`. In this case we only need to compute the scalar multiplication by `p` to get `p*k*P = O`. Here a second bound `B2` restrict the size of possible values of `p`.
<br><br>
Next i implemented a main funtion `ecm`. Here we first to find small factors using trial division.
After we exceed a certain bound then we factor the remainder by repeatedly calling `ecm_one_factor` until we completely factorizes the number. If the bound of `ecm_one_factor` fails to factor the number then there is a parameter `increase_bound` through which we can increase this bound.
<br><br>
This ECM algorithm is working fine for now and the results were amazing. It provide a huge performance boost to Sympy for factorization of large numbers. Next step is to improve the documentation to make it more readable for users and i also plan to improve the test coverage of the code.
For example: To factor `7060005655815754299976961394452809` ECM requires `~10sec` while Sympy is not able to factor it even in `~10mins`.

### Week 2:

The Lenstra's elliptic curve factorization function is completed. My main focus of this week was to improve the documentation of the `Point` class and `ecm_one_factor`. I added detailed description of the ECM algorithm and did some timing tests on this. I found that the ECM is significantly faster than the current Sympy's factorization algorithm. 
For example : n = 38054075534607062154076930331414872406237 : `ecm(n)` took merely 2.85s compared to `factorint(n)` which took 135s to factorize n. The main power of the ECM algorithm lies in that it is a subexponential algorithm and its speed depends on the largest prime factor of the number. So, even if the number is really large if its larges prime factor is in the range of ~20 digits then ecm will have no problem in factorizing the number. On th other hand methods like pollard rho(used in `factorint`) is an exponential time algorithm and its speed depends on how large the number is. So, even if the number is a multiple of many small prime factors ~10 digits, the pollard rho's method strugles to factorize it.

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

Finally i started my project. For 1st phase my main focus will be Elliptic curve factorization algorithm. I implemented a Point class whichstores point on the elliptic curve in Montgomery form. The sub methods of this class were doubling, addition and montgomery ladder algorithm for scalar multiplication of points. These methods use fast montgomery arthematic by ignoring the y coordinate and perform the
calculations by using x and z coordinate only. 
<br><br>
Next i implemented 2 functions - `ecm_one_factor` and `ecm`. `ecm_one_factor` extract one factor from the number by performing 2 stage improved standard continuation of ECM. And `ecm` basically factors small factors up to 1 million and then calls `ecm_one_factor` to repeatedly extract factors untill all the factors are extracted.
<br><br>
This ECM algorithm is working fine for now and the results were amazing. It provide a huge performance boost to Sympy for factorization of large numbers. Next step is to improve the documentation to make it more readable for users and i also plan to improve the test coverage of the code.

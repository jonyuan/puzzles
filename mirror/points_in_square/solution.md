# Points in Square

You are given a square with side length 1, centered at the origin. 8 points are then randomly and uniformly distributed inside of it. We then draw the smallest square, also centered at the origin, such that it contains 4 of those randomly distributed points. What is the expected length of the side of this square?

## My Solution

First, note that the CDF of a square of side length $x$ is $F(x) = x^2$. Thus the density is $f(x)=2x$. We then draw an arbitrary centered inner square of length $x$ with density $$(num combos)(F(x))^3(1-F(x))^4 f(x)$$ $$=\left( \frac{8!}{3!4!} \right)(x^2)^3(1-x^2)^4 2x$$
So, to find the expected value we integrate:
$$EV(x) = 2\left(\frac{8!}{3!4!} \right)\int_0^1x^8(1-x^2)^4 dx = 560\int_0^1x^8(1-x^2)^4 dx$$

Substituting $u = x^2$, so $x = \sqrt{u}$ and $dx = \frac{1}{2\sqrt{u}}du$:
$$x^8\,dx = (\sqrt{u})^8 \cdot \frac{1}{2\sqrt{u}}du = u^4 \cdot \frac{1}{2\sqrt{u}}du = \frac{u^{7/2}}{2}du$$

$$= 560 \cdot \frac{1}{2}\int_0^1 u^{7/2}(1-u)^4\,du = 280 \int_0^1 u^{7/2}(1-u)^4\,du$$

Using the beta function $B(a,b) = \int_0^1 t^{a-1}(1-t)^{b-1}dt = \frac{\Gamma(a)\Gamma(b)}{\Gamma(a+b)}$:
$$= 280 \cdot B\left(\frac{9}{2}, 5\right) = 280 \cdot \frac{\Gamma(9/2)\Gamma(5)}{\Gamma(19/2)}$$

Using $\Gamma(n+1/2) = \frac{(2n)!}{4^n n!}\sqrt{\pi}$:
$$\Gamma(9/2) = \Gamma(4 + 1/2) = \frac{8!}{4^4 \cdot 4!}\sqrt{\pi} = \frac{40320}{256 \cdot 24}\sqrt{\pi} = \frac{40320}{6144}\sqrt{\pi} = \frac{105}{16}\sqrt{\pi}$$
$$\Gamma(19/2) = \Gamma(9 + 1/2) = \frac{18!}{4^9 \cdot 9!}\sqrt{\pi}$$

$$= 280 \cdot \frac{(105/16)\sqrt{\pi} \cdot 24}{(18!/(4^9 \cdot 9!))\sqrt{\pi}} = 280 \cdot \frac{105 \cdot 24 \cdot 4^9 \cdot 9!}{16 \cdot 18!}$$
$$= 280 \cdot \frac{105 \cdot 24 \cdot 262144 \cdot 362880}{16 \cdot 6402373705728000} \approx 0.655$$

Therefore, the expected side length is approximately **0.655**.

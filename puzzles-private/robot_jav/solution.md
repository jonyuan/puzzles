# Robot Javelin

> It’s coming to the end of the year, which can only mean one thing: time for this year’s Robot Javelin finals! Whoa wait, you’ve never heard of Robot Javelin? Well then! Allow me to explain the rules:

- It’s head-to-head. Each of two robots makes their first throw, whose distance is a real number drawn uniformly from [0, 1].
- Then, without knowledge of their competitor’s result, each robot decides whether to keep their current distance or erase it and go for a second throw, whose distance they must keep (it is also drawn uniformly from [0, 1]).
- The robot with the larger final distance wins.
  > This year’s finals pits your robot, Java-lin, against the challenger, Spears Robot. Now, robots have been competing honorably for years and have settled into the Nash equilibrium for this game. However, you have just learned that Spears Robot has found and exploited a leak in the protocol of the game. They can receive a single bit of information telling them whether their opponent’s first throw (distance) was above or below some threshold d of their choosing before deciding whether to go for a second throw. Spears has presumably chosen d to maximize their chance of winning — no wonder they made it to the finals!

> Spears Robot isn’t aware that you’ve learned this fact; they are assuming Java-lin is using the Nash equilibrium. If you were to adjust Java-lin’s strategy to maximize its odds of winning given this, what would be Java-lin’s updated probability of winning? Please give the answer in exact terms, or as a decimal rounded to 10 places.

# My Solution

Javalin and Spears both throw [0, 1] (and we assign those throws to rvs. $j$ and $s$, respectively). After first throw, they may rethrow [0,1], but must take the second throw. Hereafter we are Javalin.

# Nash Equilibrium of Fair Game

Let $T$ be the threshold where javelin throw $j<=T$ should be rethrown. Let there be two strategies, keep and rethrow. To find the threshold $T$, we should evaluate where the expected probability of winning is equivalent under both strategies.

Assuming our opponent is also playing optimally, let $P_k(t=T)$ be the probability of winning with a keeping strategy, and let $P_R(t=T)$ be the probability of winning under a rerolling strategy, at the threshold $T$. We can solve for the point of indifference, $P_k(T) = P_R(T)$.
$$P_k(T) = P(s < T)P(s < T) = T^2$$
since our opponent will rethrow if under T.
$$P_R(T) = P(s < T) P(s < j) + P(s >=T)P(s < j | s >= T)$$
where $$P(s<j | s \ge T) = \frac{1}{1-T}\int_T^1 \int_s^1 1djds $$
$$=\frac{1-\frac{1}{2} - T + \frac{T^2}{2}}{1-T} = \frac{T^2 -2T+ 1}{2(1 - T)}=\frac{(1-T)}{2}$$
this is also the top right triangle of the unit square where $s \ge T$ and $j>s$, divided by $P(s \ge T) = (1 - T)$.
$$P_R(T)= \frac{1}{2}T + \frac{(1-T)^2}{2}$$
$$= \frac{1-T+T^2}{2}$$
setting them equal to each other,
$$T^2= \frac{1-T+T^2}{2}$$
$$T^2 + T - 1 = 0$$
$$T = \frac{-1 \pm \sqrt{(1 + 4)}}{2} = \frac{\sqrt{5}-1}{2} \approx 0.618$$
To me, this is intuitive since I expected $T > 0.5, T < 0.625$, the latter being the expected value under a greedy approach. We can also numerically sanity check this value in `robot_jav.ipynb`.

# Spears with exploit, vs. GTO

Spears is able to exploit by picking a threshold $d$. For brevity, let's intuit that $d=T$, because this is threshold that gives maximum information about Spears' actions. Anything $d = T \pm \epsilon$ would intuitively give us less information.

1. Given that Spears chooses $d=T$, then when $j<T$ with probability $T$, Spears believes that Javalin will rethrow on uniform [0, 1]. So by EV, Spears should keep if $s_1 \ge 0.5$.

2. When $j \ge T$ with probability $1 - T$, Spears believes Javalin keeps on uniform [T, 1]. This means that by keeping, Spears wins with probability $\frac{s-T}{1-T}$ for $s\ge T$, else $0$. By rethrowing, Spears wins with probability (same as above): $$\frac{1}{1-T}\int_T^1\int_j^1 1dsdj = \frac{1-T}{2}$$
   So spears keeps at the threshold:
   $$\frac{s-T}{1-T} = \frac{1-T}{2}$$
   $$s^* = T + \frac{(1-T)^2}{2} \approx 0.691$$
   which intuitively seems in the right range (Spears has a higher bar to reroll, since Javalin is distributed uniform on [T, 1])

# probability javalin wins, knowing about the exploit!

Now that we have determined Spears's exploit strategy, let's determine how we should adjust our own strategy. Then, we can compute the probability of winning under this strategy against Spears with the exploit.

If Javalin throws $j <T$ with probability $T$, Spears uses the threshold $s_1 = 0.5$. Let's find the counter threshold $T'$. Based on intuition that $T' < T$ (to exploit Spears' weaker 0.5 threshold without triggering aggressive response - since the probability of spears' result falling between [0.5, T'] is now higher than GTO, this is exploitable), we set up the indifference equation:
$$P(win | keep) = \frac{1}{2}T' + (T' - \frac{1}{2}) = \frac{3}{2}T' - \frac{1}{2}$$
$$P(win|rethrow) = (\frac{1}{2})(\frac{1}{2}) + \frac{1}{2}(\int_{\frac{1}{2}}^1 (1 - j) dj) = \frac{1}{4} + \frac{1}{8} = \frac{3}{8}$$
$$\frac{3}{2}T' - \frac{1}{2} = \frac{3}{8}$$
$$T' = \frac{7}{12} \approx 0.583$$

This aligns with our intuition. We can now compute Javalin's final win probability by integrating over three regions.

**Region 1:** $j \in [0, T')$

Javalin rethrows, Spears uses $s_1 = \frac{1}{2}$. Win probability is constant $\frac{3}{8}$.
$$\int_0^{T'} \frac{3}{8} dj = \frac{3}{8}T' = \frac{3}{8} \cdot \frac{7}{12} = \frac{7}{32}$$

**Region 2:** $j \in [T', T)$

Javalin keeps $j$, Spears uses $s_1 = \frac{1}{2}$. Win probability per $j$ is $\frac{3}{2}j - \frac{1}{2}$.
$$\int_{T'}^T \left(\frac{3}{2}j - \frac{1}{2}\right) dj = \frac{3}{4}T^2 - \frac{1}{2}T + \frac{7}{192}$$

**Region 3:** $j \in [T, 1]$

Javalin keeps $j$, Spears uses $s_2 = T + \frac{(1-T)^2}{2}$. Splitting on whether $j < s_2$:
- For $j \in [T, s_2)$: $P(\text{win}|j) = s_2 \cdot j$
- For $j \in [s_2, 1]$: $P(\text{win}|j) = j(s_2 + 1) - s_2$

$$\text{Region 3} = \int_T^{s_2} s_2 j \, dj + \int_{s_2}^1 [j(s_2 + 1) - s_2] \, dj$$

**Total Probability:**

Noting that $s_2 = \frac{1}{2} + \frac{T^2}{2}$ and combining all three regions:

$$P(\text{win}) = \frac{121}{192} - \frac{T}{2} + \frac{T^2}{2} - \frac{T^4}{8}$$

where $T = \frac{\sqrt{5}-1}{2}$ is the Nash equilibrium threshold

**Exact symbolic form:**

Substituting $T = \frac{\sqrt{5}-1}{2}$ (with $T^2 = \frac{3 - \sqrt{5}}{2}$ and $T^4 = \frac{7 - 3\sqrt{5}}{2}$):

$$P(\text{win}) = \frac{229 - 60\sqrt{5}}{192}$$

**Numerical verification:**

Using 100-digit precision: $\frac{229 - 60\sqrt{5}}{192} = 0.493937090364649...$

Monte Carlo simulation (5M trials): $0.493895...$

$$P(\text{Javalin wins}) = \boxed{\frac{229 - 60\sqrt{5}}{192} = 0.4939370904}$$

# Archive (back when I thought spears chooses $d$ dynamically)

Now spears bot is able to exploit by picking a threshold $d$ to check whether Javalin (playing GTO) has crossed on their first throw, before throwing their second. For brevity, let's intuit that $d=T$ if $s < T$, and $d=s$ if $s > T$, because the former will tell spears if javalin will be rethrowing. If spears has thrown > 0.5, and javalin is rethrowing, spears is better off with their throw as is. If spears throws < 0.5, they should always rethrow.

$$P_E(d) = P(s<0.5)\left[P(j<T)\cdot 1/2 + P(j \ge T)P(s>j | j \ge T)\right]$$
$$+P(0.5 < s \le T)\left[P(j < T)P(s > j | 0.5 < s \le T) + P(j > T) P(s>j | j \ge T)\right]$$
$$+ P(s > T)\left[P(j < s) \cdot 1 +  P(j > s) P(s' > j | j > s > T)\right]$$

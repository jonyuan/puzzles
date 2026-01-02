# Robot Baseball

The Artificial Automaton Athletics Association (Quad-A) is at it again, to compete with postseason baseball they are developing a Robot Baseball competition. Games are composed of a series of independent at-bats in which the batter is trying to maximize expected score and the pitcher is trying to minimize expected score.

An at-bat is a series of pitches with a running count of balls and strikes, both starting at zero. For each pitch, the pitcher decides whether to throw a ball or strike, and the batter decides whether to wait or swing; these decisions are made secretly and simultaneously. The results of these choices are as follows.

- If the pitcher throws a ball and the batter waits, the count of balls is incremented by 1.
- If the pitcher throws a strike and the batter waits, the count of strikes is incremented by 1.
- If the pitcher throws a ball and the batter swings, the count of strikes is incremented by 1.
- If the pitcher throws a strike and the batter swings, with probability p the batter hits a home run1 and with probability 1-p the count of strikes is incremented by 1.

An at-bat ends when either:

- The count of balls reaches 4, in which case the batter receives 1 point.
- The count of strikes reaches 3, in which case the batter receives 0 points.
- The batter hits a home run, in which case the batter receives 4 points.

By varying the size of the strike zone, Quad-A can adjust the value p, the probability a pitched strike that is swung at results in a home run. They have found that viewers are most excited by at-bats that reach a full count, that is, the at-bats that reach the state of three balls and two strikes. Let q be the probability of at-bats reaching full count; q is dependent on p. Assume the batter and pitcher are both using optimal mixed strategies and Quad-A has chosen the p that maximizes q. Find this q, the maximal probability at-bats reach full count, to ten decimal places.

# My Solution

This is a **simultaneous-move game** at each state (b,s) where b = balls (0-3), s = strikes (0-2):

- **Pitcher** (minimizer) chooses: Ball or Strike
- **Batter** (maximizer) chooses: Wait or Swing
- **Decisions made simultaneously** each pitch

### Outcomes

- Ball + Wait => balls +1
- Strike + Wait => strikes +1
- Ball + Swing => strikes +1
- Strike + Swing => with probability p: home run (4 points), with probability (1-p): strikes +1

### Terminal States

- 4 balls → Walk (1 point)
- 3 strikes → Strikeout (0 points)
- Home run → 4 points

## Approach

### Backward Induction with Nash Equilibrium

At each state (b,s), construct the payoff matrix (from batter's perspective):

```
                Wait            Swing
Ball          V(b+1, s)        V(b, s+1)
Strike        V(b, s+1)        p·4 + (1-p)·V(b, s+1)
```

Where V(b,s) = expected value at state (b,s) under optimal play.

Nash Equilibrium:

- Pitcher plays Ball with probability p_ball
- Batter plays Wait with probability p_wait
- Find probabilities for mixed eq. that make the opponent indifferent between their actions

Indifference Principle (Mixed Strategy):

Pitcher's mixing makes batter indifferent between Wait and Swing:

- E[Wait] = prob_ball × a + (1-prob_ball) × c
- E[Swing] = prob_ball × b + (1-prob_ball) × d

Setting equal: prob_ball = (d-c)/(a-b-c+d). Batter's mixing makes pitcher indifferent between Ball and Strike:

- E[Ball] = prob_wait × a + (1-prob_wait) × b
- E[Strike] = prob_wait × c + (1-prob_wait) × d

Setting equal: prob_wait = (d-b)/(a-b-c+d)

### Computing Probability of Reaching Full Count

Define Q(b,s) = probability of reaching (3,2) starting from (b,s).

**Base cases:**

- Q(3,2) = 1 (already at full count)
- Q(4,s) = 0 for all s (walk ends game)
- Q(b,3) = 0 for all b (strikeout ends game)

**Recursive formula:**

```
Q(b,s) = p_ball * p_wait * Q(b+1, s)
       + p_ball * (1-p_wait) * Q(b, s+1)
       + (1-p_ball) * π_wait * Q(b, s+1)
       + (1-p_ball) * (1-p_wait) * [(1-p)·Q(b, s+1) + p·0]
```

The probability we want is q = Q(0,0).

### 3. Optimization

Find p\* ∈ [0,1] that maximizes q:

$$p^* = \argmax_{p \in [0, 1]} Q_p(0, 0)$$

Because this is a recursive optimization, we will proceed in `robot_baseball.ipynb` with the numerical optimization steps.

## Result

**Optimal p\*:** 0.2269732325
**Maximum q:** **0.2959679934**

---
layout: post
title:  "Computing TFT's Emotional Rollercoaster"
date:   2023-12-29 21:09:56 -0600
---

<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>   


Teamfight Tactics (TFT) is a multiplayer auto chess game developed by Riot games. During the game, players can buy & sell champions with gold to develop the best "comp" or arrangment of characters. A champion is defined as a entity that costs a certain gold amount that gives your "comp" certain traits. At every moment, there is a shop displaying 5 random champions that follow some distribution pattern. This so-called distribution has 2 layers to it
1. **Level**. Your level changes the percent likelihood you are to see 1,2,3,4,5 costs. This allows you to see lower cost champions in the early game and ensure that late game you will on average see higher cost champions.

    | Level | 1-cost | 2-cost | 3-cost | 4-cost | 5-cost |
    |-------|--------|--------|--------|--------|--------|
    | 1     | 100%   | 0%     | 0%     | 0%     | 0%     |
    | 2     | 100%   | 0%     | 0%     | 0%     | 0%     |
    | 3     | 75%    | 25%    | 0%     | 0%     | 0%     |
    | 4     | 55%    | 30%    | 15%    | 0%     | 0%     |
    | 5     | 45%    | 33%    | 20%    | 2%     | 0%     |
    | 6     | 30%    | 40%    | 25%    | 5%     | 0%     |
    | 7     | 19%    | 35%    | 35%    | 10%    | 1%     |
    | 8     | 18%    | 25%    | 36%    | 18%    | 3%     |
    | 9     | 10%    | 20%    | 25%    | 35%    | 10%    |
    | 10    | 5%     | 10%    | 20%    | 40%    | 25%    |
    | 11    | 1%     | 2%     | 12%    | 50%    | 35%    |

2. **Units Left**.  The number of champions left per gold bracket 

    | Cost Type | Quantity | Distinct Champions |
    |-----------|----------|------|
    | 1-cost    | 29       | 13   |
    | 2-cost    | 22       | 13   |
    | 3-cost    | 18       | 13   |
    | 4-cost    | 12       | 12   |
    | 5-cost    | 10       | 8    |

# "Data Science"
Every TFT Player has experienced this one scenario multiple times. You've reached mid game (level 6-8) with around 40-50 gold and you need exactly 1 character to make all your synergies combine. You roll 2 gold, nothing. Roll 10 gold, Nothing. Roll EVERYTHING, NOTHING!!! You wonder, how the f*** is this even possible, how did you get so unlucky to not even hit a SINGLE unit. Well that scenario is the inspiration for this project, I want to know how exactly unlikely was I when rolling down for a certain unit. 

Each shop rolls for the cost of the unit first (based on your current level), followed by the unit itself (based on the number of units left & the pool size for units of that cost).

$$
P(\text{hitting a specific unit in this shop}) = P(\text{roll unit-cost})\cdot\frac{\text{number of specific unit}}{\text{number of unit-costs left}}
$$

Trivially we can then infer our "unluckiness" stat would be the compliment of the probability of hitting a specific unit.

$$
P(\text{unluckiness}) =  1 - P(\text{hitting a specific unit in this shop})^\text{number of rerolls}
$$

However, during each iteration, someone else could've taken the unit effecting the pool, so we expand the formula to

$$
P(\text{unluckiness}) = 1 - P_1(\text{hitting in shop 1}) \times P_2(\text{hitting in shop 2}) \times \ldots \times P_n(\text{hitting in shop n})
$$ 

<center><a href="https://github.com/wongkj12/TFT-Rolling-Odds-Calculator/tree/main">Reference: wongkj12</a>

</center>

<br>
# Implementation 






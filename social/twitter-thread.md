# Twitter thread: can you trust Jev's confidence?

Copy each tweet from its code block, then attach the image listed under it. Images are in `../images/social/` (1600×900). Character counts treat the link as 23 characters and emoji as 2, as X does.

---

## Tweet 1 of 6 (269 characters)

```text
Jev gives you a confidence value with every answer. Can you trust it?

I revisited my earlier classifier-calibration work with Jev, on 8,801 labeled examples.

Short version: the values are useful, but how you set up the question changes how much you can trust them. 🧵
```

**Image:** `images/social/1_stated_vs_actual.png`

![](../images/social/1_stated_vs_actual.png)

**Alt text:**

```text
Line chart of Jev's stated confidence against actual accuracy. Noul climbs toward the perfect-calibration diagonal, reaching 100% at the top. Choice stays near 50% until 93% confidence, then jumps to 90%.
```

---

## Tweet 2 of 6 (215 characters)

```text
Yes/no questions (Noul): modestly overconfident, about 7 points above actual accuracy.

The extremes are solid: all 2,104 answers above 95% confidence were right.

The middle is shaky: ~70% confident, ~54% accurate.
```

**Image:** `images/social/2_noul_bars.png`

![](../images/social/2_noul_bars.png)

**Alt text:**

```text
Bar chart for Noul, by stated-confidence bucket. Accuracy trails stated confidence in the middle buckets (54% at 71%), and all 2,104 answers above 95% were correct.
```

---

## Tweet 3 of 6 (251 characters)

```text
Pick-one questions (Choice) are overconfident in a different way: about 15 points above accuracy.

Below 95% confidence it's close to a coin flip (50–57% right), whatever number it states.

Above 95%? 63% of answers land there, and only 90% are right.
```

**Image:** `images/social/3_choice_bars.png`

![](../images/social/3_choice_bars.png)

**Alt text:**

```text
Bar chart for Choice, by stated-confidence bucket. Accuracy is 50–57% in every bucket below 95%, and 90% in the top bucket, which holds 63% of all answers.
```

---

## Tweet 4 of 6 (233 characters)

```text
It's not just Noul vs Choice. Rewording one Noul question moved its accuracy from 72% to 76%, and raw calibration error ranged from 6 to 16 points across the setups I tried.

Same task, different setup, different confidence behavior.
```

**Image:** `images/social/4_setups_differ.png`

![](../images/social/4_setups_differ.png)

**Alt text:**

```text
Two dot plots comparing 11 ways of asking the same sentiment question. Accuracy ranges from 72.3% to 76.9%, and raw calibration error from 6.4 to 16.0 percentage points.
```

---

## Tweet 5 of 6 (264 characters)

```text
Good news: calibration levels it out. Fit on labeled examples, the error fell under 2 points for every setup I tried.

Platt scaling only got partway. Isotonic regression, which follows whatever shape the data has, did much better, even with ~100 labeled examples.
```

**Image:** `images/social/5_calibration_fixes.png`

![](../images/social/5_calibration_fixes.png)

**Alt text:**

```text
Dot plot of calibration error for 11 setups. Raw error of 6 to 16 points falls to under 2 points after isotonic regression for every setup; Platt scaling lands in between.
```

---

## Tweet 6 of 6 (278 characters)

```text
Takeaways:
• Higher confidence really does mean more likely right
• Calibrate on your own labeled data before deciding with it
• Calibrate each setup separately
• Don't trust Choice's raw confidence below 95%

One dataset, one model version.
Code + data: github.com/AnthusAI/Jev-Calibration
```

**Image:** none

---

## Single-tweet alternative (258 characters)

```text
Can you trust Jev's confidence? On 8,801 examples: useful (higher = more likely right), but overconfident, and the pattern depends on how you set up the request. Yes/no: off mid-range. Pick-one: a coin flip below 95%. Calibrate first. github.com/AnthusAI/Jev-Calibration
```

**Image:** `images/social/1_stated_vs_actual.png`

**Alt text:**

```text
Line chart of Jev's stated confidence against actual accuracy. Noul climbs toward the perfect-calibration diagonal, reaching 100% at the top. Choice stays near 50% until 93% confidence, then jumps to 90%.
```

---

## Where each claim comes from

| Claim | Source |
|---|---|
| Noul about 7 points overconfident; Choice about 15 | mean confidence 0.790 vs accuracy 0.723 (Noul), 0.914 vs 0.761 (Choice), all 8,801 examples: `results/raw_confidence_bands.json` |
| All 2,104 Noul answers above 95% were right | `results/raw_confidence_bands.json` (`noul_all_tiers`, 95-100% bucket) |
| Noul ~70% confident, ~54% accurate in the middle | same file, 60-80% bucket: 70.7% stated, 54.3% actual |
| Choice 50–57% right below 95%; 63% of answers above 95% at 90% | same file (`choice_all_tiers`): 5,559 of 8,801 answers, 90.2% accurate |
| Rewording a Noul moved accuracy from 72% to 76% | `results/variants.json`: `noul_pos` 0.723 vs `noul_favorable` 0.761 (test split) |
| Raw calibration error 6 to 16 points across setups | `results/variants.json`: raw ECE 0.064 to 0.160 (test split, P(positive)) |
| Error under 2 points after calibration | isotonic ECE 0.006 to 0.018 for every setup (`results/variants.json`) |
| Platt got only partway; isotonic much better, even at ~100 examples | `results/variants.json`; `results/calibration_size.json` (at 100 examples, isotonic averaged about 5 points vs about 12 raw for `noul_pos`) |

Caveats to keep in mind if asked: one constructed sentiment dataset, one model version (`jev-1.13.0`), and about a fifth of the examples carry arbitrary labels (without that tier the average overconfidence is about 1 point for Noul and 9 for Choice). The 7 and 15 points are the all-examples figures. "Calibrate each setup separately" is an inference from the fitted curves differing across setups; we haven't tested reusing one setup's calibration on another.

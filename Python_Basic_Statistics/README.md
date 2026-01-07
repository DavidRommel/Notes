# Basic Statistics

This repository serves as a personal knowledge base for statistical analysis, covering probability theory, descriptive statistics, and inferential techniques like confidence intervals and hypothesis testing.

## Table of Contents

* [Probability Fundamentals](probability-fundamentals)
    * [Addition Rule (OR)](addition-rule-or)
    * [Multiplication Rule (AND)](multiplication-rule-and)
    * [Conditional Probability](conditional-probability)
    * [Bayes' Theorem](bayes-theorem)
* [Distributions & The Empirical Rule](distributions--the-empirical-rule)
    * [Poisson Distribution](poisson-distribution)
    * [Binomial Distribution](binomial-distribution)
    * [The Empirical Rule (68-95-99.7)](the-empirical-rule-68-95-997)
* [Z-Scores & Outlier Detection](z-scores--outlier-detection)
    * [Calculating Standard Deviation](calculating-standard-deviation)
* [Sampling & Standard Error](sampling--standard-error)
    * [DataFrame Sampling Techniques](dataframe-sampling-techniques)
        * [Random Sampling with Replacement](random-sampling-with-replacement)
* [Confidence Intervals](confidence-intervals)
    * [Calculating Margin of Error](calculating-margin-of-error)
* [Hypothesis Testing](hypothesis-testing)
    * [Error Types](error-types)
    * [Implementation Reference](implementation-reference)
    * [Example 1: One-Sample T-Test](example-1-one-sample-t-test)
    * [Example 2: Two-Sample T-Test (Independent)](example-2-two-sample-t-test-independent)

---

## Probability Fundamentals

### Addition Rule (OR)

Used for mutually exclusive events. $P(A \cup B) = P(A) + P(B)$.

> *Example:* The probability of rolling either a 2 or a 4 on a single die roll is $(\frac{1}{6} + \frac{1}{6}) \times 100 \approx 33.3\%$.

### Multiplication Rule (AND)

Used for independent events. $P(A \cap B) = P(A) \times P(B)$.

> *Example:* The probability of rolling a 2 on two consecutive rolls of a die is $(\frac{1}{6} \times \frac{1}{6}) \times 100 \approx 2.7\%$.

### Conditional Probability

The probability of an event occurring given that another event has already occurred. $P(A \cap B) = P(A) \times P(B|A)$.

> *Example:* The probability of drawing an Ace on the first draw ($\frac{4}{52}$) and an Ace on the second draw ($\frac{3}{51}$) is approximately $0.45\%$.

### Bayes' Theorem

A method for updating probabilities as more evidence becomes available.$$P(A|B) = \frac{P(B|A) \times P(A)}{P(B)}$$

> *Example:* Calculating the chance of rain given that the day started off cloudy, using the overall rain probability, the percentage of cloudy days, and the percentage of rainy days that start cloudy.   

---

## Distributions & The Empirical Rule

### Poisson Distribution
Used when you have an average rate of an event for a specific time period and want to find the probability of a certain number of events occurring in that same window.


```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats
```


```python
# Example: If the average number of customers per day is 10 (lambda=10)
# What is the probability of exactly 12 customers visiting today?
prob_12 = stats.poisson.pmf(k=12, mu=10)
print(f"Probability of exactly 12 customers: {prob_12:.4f}")
```

    Probability of exactly 12 customers: 0.0948


### Binomial Distribution
Used when given the exact probability of an event and you want to find the probability of that event occurring  times in repeated trials.


```python
# Example: Using 'tips' dataset to model smoker probability
tips = sns.load_dataset('tips')
p_smoker = (tips['smoker'] == 'Yes').mean() # Probability of a customer being a smoker

# If 10 customers walk in, what is the probability that exactly 3 are smokers?
prob_3_smokers = stats.binom.pmf(k=3, n=10, p=p_smoker)
print(f"Probability of exactly 3 smokers in 10 customers: {prob_3_smokers:.4f}")
```

    Probability of exactly 3 smokers in 10 customers: 0.2310


### The Empirical Rule (68-95-99.7)

In a normal distribution:
* 68% of values fall within $\pm 1$ standard deviation ($\sigma$).
* 95% of values fall within $\pm 2$ standard deviations.
* 99.7% of data falls within $\pm 3$ standard deviations.

---

## Z-Scores & Outlier Detection

Z-scores standardize data by calculating how many standard deviations a point is from the mean ($z = \frac{x - \mu}{\sigma}$).  

Outliers are typically defined as values where $|z| > 3$.


```python
# Load dataset and calculate Z-scores
penguins = sns.load_dataset('penguins').dropna()
penguins['mass_z'] = stats.zscore(penguins['body_mass_g'])

# Identify outliers (Z > 3 or Z < -3)
outliers = penguins[abs(penguins['mass_z']) > 3]
```

### Calculating Standard Deviation

Calculated by determining the mean, finding the sum of squared differences from that mean, dividing by the sample size minus one ($n-1$), and taking the square root.

$$s = \sqrt{\frac{\sum (x_i - \bar{x})^2}{n - 1}}$$

**Variable Definitions:**
* $s$: The sample standard deviation.
* $\sum$: Sigma, representing the sum of the values.
* $x_i$: Each individual value in the data set.
* $\bar{x}$: The sample mean (average) of the values.
 $n$: The total number of values in the sample.

--- 

## Sampling & Standard Error

Standard Error (SE) quantifies the variation in the sample mean or proportion.

* **SE of a Sample Mean:** $SE = \frac{s}{\sqrt{n}}$.
* **SE of a Proportion:** $SE = \sqrt{\frac{p(1-p)}{n}}$.

### DataFrame Sampling Techniques

When performing statistical analysis, it is often necessary to take subsets or random samples from a larger DataFrame.  

In Pandas, the `.sample()` method is the primary tool for this task.

#### Random Sampling with Replacement

Sampling **with replacement** means that a single row can be selected more than once in the same sample. This is useful for techniques like bootstrapping.


```python
# Take a sample of 30 penguins from the dataset
# replace=True allows the same row to be selected multiple times
df_sample = penguins.sample(n=30, replace=True, random_state=42)
```

**Key Parameters:**

* `n`: The number of items (rows) to return.
* `replace`: Boolean; whether the sample is with or without replacement.
* `random_state`: Seed for the random number generator, ensuring reproducibility.

---

## Confidence Intervals

Confidence intervals provide a range that likely contains the population parameter based on a specific confidence level.

* **Large Samples ($n \ge 30$):** Use the normal distribution via `stats.norm.interval`.
* **Small Samples ($n < 30$):** Use the t-distribution via `stats.t.ppf` to find critical values.


```python
# 95% Confidence Interval for Adelie penguin body mass
adelie_mass = penguins[penguins['species'] == 'Adelie']['body_mass_g']
standard_error = adelie_mass.std() / np.sqrt(len(adelie_mass))
ci = stats.norm.interval(confidence=0.95, loc=adelie_mass.mean(), scale=standard_error)
print("95% CI [{:.3f}, {:.3f}]".format(ci[0].item(), ci[1].item()))
```

    95% CI [3631.773, 3780.556]


### Calculating Margin of Error
The Margin of Error (MOE) represents the amount of random sampling error in a survey's results. It is calculated by multiplying the standard deviation (or standard error) by the appropriate z-score or t-score for the desired confidence level.


```python
# Example: Calculating MOE for a proportion
# Sample of 100 voters, 55% prefer Candidate A, 95% confidence (z-score = 1.96)
standard_deviation = np.sqrt((0.55 * (1 - 0.55)) / 100)
moe = standard_deviation * 1.96 * 100
print('Margin of error: {:.2f}%'.format(moe))
```

    Margin of error: 9.75%



```python
# Example: Calculating MOE for a mean with a small sample (n=15)
# Mean emission = 430, SD = 35, 95% confidence
std_err = 35 / np.sqrt(15) #
t_score = stats.t.ppf(1 - (1 - 0.95) / 2, df=15 - 1)
moe_mean = t_score * std_err
print('Margin of error: {:.2f}%'.format(moe_mean))
```

    Margin of error: 19.38%


---

## Hypothesis Testing

### Error Types

* **Type 1 Error:** Rejecting the null hypothesis ($H_0$) when it is actually true (False Positive).
* **Type 2 Error:** Failing to reject the null hypothesis when it is actually false (False Negative).

### Implementation Reference

| Test Type | Scenario | Python Function |
| --- | --- | --- |
| **One-Sample T-test** | Compare sample mean to a hypothesized value ($H_0: \mu = x$). | `stats.ttest_1samp()` |
| **Two-Sample T-test** | Compare means of two independent groups. | `stats.ttest_ind()` |

### Example 1: One-Sample T-Test

Used to determine if the sample mean of a variable differs from a specific value. In this example, we test if the mean flipper length of Adelie penguins is equal to 190 mm.
* **Null Hypothesis ($H_0$):** The population mean is equal to a specific, hypothesized value, meaning there's no significant difference between the true population mean and the known or proposed value


```python
# H0: Mean flipper length = 190mm
# HA: Mean flipper length != 190mm
adelie_flipper = penguins[penguins['species'] == 'Adelie']['flipper_length_mm']

t_stat, p_val = stats.ttest_1samp(a=adelie_flipper, popmean=190)

# If p_val < 0.05, reject the null hypothesis
print('P-value: {:.4f}'.format(p_val))
```

    P-value: 0.8493


### Example 2: Two-Sample T-Test (Independent)

Used to determine if the mean flipper length of Adelie penguins is significantly different from Gentoos.
* **Null Hypothesis ($H_0$):** There is no statistically significant difference between the means of the two populations being compared


```python
# Compare flipper length: Adelie vs. Gentoo
adelie_flipper = penguins[penguins['species'] == 'Adelie']['flipper_length_mm']
gentoo_flipper = penguins[penguins['species'] == 'Gentoo']['flipper_length_mm']

# Perform two-sample t-test (Welch's t-test if equal_var=False)
t_stat, p_val = stats.ttest_ind(a=adelie_flipper, b=gentoo_flipper, equal_var=False, alternative='less')

# If p_val < 0.05, reject the null hypothesis
print('P-value: {:.4f}'.format(p_val))
```

    P-value: 0.0000

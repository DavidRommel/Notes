# Basic Statistics

This repository serves as a personal knowledge base for statistical analysis, covering probability theory, descriptive statistics, and inferential techniques like confidence intervals and hypothesis testing.

## Table of Contents

* [Probability Fundamentals](#probability-fundamentals)
    * [Addition Rule (OR)](#addition-rule-or)
    * [Multiplication Rule (AND)](#multiplication-rule-and)
    * [Conditional Probability](#conditional-probability)
    * [Bayes' Theorem](#bayes-theorem)
* [Distributions & The Empirical Rule](#distributions--the-empirical-rule)
    * [Poisson Distribution](#poisson-distribution)
    * [Binomial Distribution](#binomial-distribution)
    * [The Empirical Rule](#the-empirical-rule)
* [Z-Scores & Outlier Detection](#z-scores--outlier-detection)
    * [Calculating Standard Deviation](#calculating-standard-deviation)
* [Sampling & Standard Error](#sampling--standard-error)
    * [DataFrame Sampling Techniques](#dataframe-sampling-techniques)
        * [Random Sampling with Replacement](#random-sampling-with-replacement)
* [Confidence Intervals](#confidence-intervals)
    * [Calculating Margin of Error](#calculating-margin-of-error)
* [Hypothesis Testing](#hypothesis-testing)
    * [Test Types](#test-types)
       * [Z-test](#z-test)
       * [T-Test](#t-test)
    * [Error Types](#error-types)
    * [Implementation Reference](#implementation-reference)
    * [Examples](#examples)
       * [Example 1: One-Sample T-Test](#example-1-one-sample-t-test)
       * [Example 2: Two-Sample T-Test (Independent)](#example-2-two-sample-t-test-independent)
       * [Example 3: One-Sample Z-Test](#example-3-one-sample-z-test)

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

A method for updating probabilities as more evidence becomes available. $$P(A|B) = \frac{P(B|A) \times P(A)}{P(B)}$$

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
print('Probability of exactly 12 customers: {:.4f}'.format(prob_12))
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
print('Probability of exactly 3 smokers in 10 customers: {:.4f}'.format(prob_3_smokers))
```

    Probability of exactly 3 smokers in 10 customers: 0.2310


### The Empirical Rule

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
### Test Types
#### Z-test
* Used when the population standard deviation is **known**.
* The test statistic is a z-score, which indicates how many standard deviations below or above the population mean a data point is.

   $\Large z = \frac{\bar{x} - \mu}{\frac{\sigma}{\sqrt{n}}}$
   * $\bar{x}$ is the sample mean
   * $\mu$ is the population mean
   * $\sigma$ is population standard deviation
   * $n$ is the sample size  

* T-tests do not apply to **proportions**, conduct a two-sample z-test to compare two population proportions.  
   $\Large z = \frac{\hat{p}_1 - \hat{p}_2}{\sqrt{\hat{p}_0(1 - \hat{p}_0)\left(\frac{1}{n_1} + \frac{1}{n_2}\right)}}$
   * **Numerator:** $\hat{p}_1 - \hat{p}_2$ represents the difference between the two sample proportions.
   * **Denominator:** The square root contains the standard error of the difference.
      * $\hat{p}_0$: This is the pooled proportion, calculated from both samples combined.
      * $n_1, n_2$: These are the respective sample sizes.
     
      **Calculating $\hat{p_0}$ example:**
      * Random sample of 50 employees in each office
      * 67 percent report being satisfied with their job at the London office
      * 57 percent report being satisfied with their job at the Beijing office.
      * Is there a difference in the proportion of satisfied employees in London and Beijing offices?
          
        $\Large \hat{p_0} = \frac{x_1 + x_2}{n_1 + n_2} = \frac{33.5 + 28.5}{50 + 50} = \frac{62}{100} = 0.62$   


        $\Large z = \frac{0.67 - 0.57}{\sqrt{0.62(1 - 0.62) \left( \frac{1}{50} + \frac{1}{50} \right)}} \approx 1.03$        

**Left-Tailed Test**
* **Hypothesis:** $H_a: \mu < \mu_0$  
* **Logic:** You want the area to the **left** of your $z$-score.  
* **Calculation:** $P(Z \le z)$  

**Right-Tailed Test**
* **Hypothesis:** $H_a: \mu > \mu_0$  
* **Logic:** You want the area to the **right** of your $z$-score.  
* **Calculation:** $1 - P(Z \le z)$  

**Two-Tailed Test**
* **Hypothesis:** $H_a: \mu \neq \mu_0$  
* **Logic:** You want the total area in **both tails**.  
* **Calculation:** $2 \times P(Z \ge |z|)$ (Find the area of one tail and double it).  

#### T-Test
* Used when the population standard deviation is **unknown**.
   * Population standard deviation is usually unknown and needs to be estimated from the data.
* The test statistic is a T-score that is based on the T-distribution.  

   $\Large t = \frac{(\bar{X_1}-\bar{X_2})}{\sqrt{\frac{S_1^2}{n_1}+\frac{S_2^2}{n_2}}}$
   * $\bar{X_1}$ and $\bar{X_2}$ are the sample means of your two groups
   * $n_1$ and $n_2$ are the sample sizes of your two groups
   * $s_1$ and $s_2$ are the sample standard deviations of your two groups


### Error Types

* **Type 1 Error:** Rejecting the null hypothesis ($H_0$) when it is actually true (False Positive).
* **Type 2 Error:** Failing to reject the null hypothesis when it is actually false (False Negative).

### Implementation Reference

| Test Type | Scenario | Python Function |
| --- | --- | --- |
| **One-Sample T-test** | Compare sample mean to a hypothesized value ($H_0: \mu = x$). | `stats.ttest_1samp()` |
| **Two-Sample T-test** | Compare means of two independent groups. | `stats.ttest_ind()` |

### Examples
#### Example 1: One-Sample T-Test

Used to determine if the sample mean of a variable differs from a specific value. In this example, we test if the mean flipper length of Adelie penguins is equal to 190 mm.
* **Null Hypothesis ($H_0$):** The population mean is equal to a specific, hypothesized value, meaning there's no significant difference between the true population mean and the known or proposed value
* Use the `alternative` parameter of the `ttest_1samp()` function for left-tailed, right-tailed, or two-sided tests.
   * `two-sided` (default, two-tailed), `less` (left-tailed), `greater` (right-tailed)


```python
# H0: Mean flipper length = 190mm
# HA: Mean flipper length != 190mm
adelie_flipper = penguins[penguins['species'] == 'Adelie']['flipper_length_mm']

t_stat, p_val = stats.ttest_1samp(a=adelie_flipper, popmean=190)

# If p_val < 0.05, reject the null hypothesis
print('P-value: {:.4f}'.format(p_val))
```

    P-value: 0.8493

#### Example 2: Two-Sample T-Test (Independent)

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


#### Example 3: One-Sample Z-Test
A premium coffee roaster claims that their "Single Origin Espresso" bags have a mean weight of $85\text{ g}$, with a known population standard deviation of $3.5\text{ g}$. A quality control manager suspects the machine might be overfilling the bags and selects a random sample of $n = 10$ bags, finding a sample mean weight of $89.1\text{ g}$.  

Determine the statistical significance of this weight deviation for three different hypotheses:
* **Left-tailed:** Is the weight significantly *less* than $85\text{ g}$?
* **Right-tailed:** Is the weight significantly *greater* than $85\text{ g}$?
* **Two-tailed:** Is the weight significantly *different* (either higher or lower) than $85\text{ g}$?

```python
from scipy import stats

pop_mean = 85
pop_std = 3.5  # Z-tests assume known population standard deviation
sample_mean = 89.1
n = 10

# Calculate Z-score
z_score = (sample_mean - pop_mean) / (pop_std / np.sqrt(n))


# Left-tailed p-value
p_left = stats.norm.cdf(z_score)

# Right-tailed p-value
p_right = 1 - stats.norm.cdf(z_score)

# Two-tailed p-value
p_two = 2 * (1 - stats.norm.cdf(abs(z_score)))
```

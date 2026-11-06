# Bootstrap, Confidence Intervals, and Hypothesis Testing

This project uses reproducible simulation in R to illustrate three connected ideas in statistical inference: **bootstrap distributions**, **confidence-interval coverage**, and **hypothesis testing under a simulated null distribution**. Interactive Plotly figures make the sampling distributions, confidence intervals, rejection regions, and p-values easier to inspect.

## Project objectives

The project addresses the following questions:

1. How can repeated resampling from one observed sample approximate the sampling distribution of a statistic?
2. How does the nominal confidence level relate to the empirical coverage rate across repeated samples?
3. How can a null distribution be used to identify rejection regions and calculate a p-value?

All examples are generated programmatically from fixed random seeds. No external wage microdata or coin-toss dataset is required.

## Methods and analysis

### 1. Bootstrap distribution for the mean wage

The script first generates a synthetic wage sample from a Normal population with a known mean of 100 and standard deviation of 20. The default sample size is 25.

It then draws 1,000 bootstrap samples **with replacement** from the original sample and calculates the mean for each resample. The resulting bootstrap distribution is used to estimate the standard error of the sample mean and percentile confidence intervals at the 90%, 95%, and 99% levels.

The interactive figure shows the distribution of bootstrap means. Select a confidence level from the dropdown to see the corresponding interval and tail regions. Reference lines mark the original sample mean and the known population mean.

The project also varies the number of bootstrap resamples (250, 1,000, and 5,000) and compares bootstrap distributions under different Normal population parameters. These comparisons illustrate how resampling effort and the underlying sample-generating parameters affect the observed bootstrap distribution.

**Key distinction:** bootstrap resampling repeatedly samples from the *observed sample*. It is not the same as drawing a new sample from the population.

### 2. Monte Carlo coverage of confidence intervals

The second experiment evaluates the repeated-sampling coverage of z-based confidence intervals when the population standard deviation is known.

For each sample size (10, 25, 50, and 100), the script generates 10,000 independent samples from the same Normal population. For each sample, it constructs two-sided confidence intervals at the 90%, 95%, and 99% levels using

$$\[
\bar X \pm z_{1-\alpha/2}\frac{\sigma}{\sqrt n}.
\]$$

An interval is counted as covering the parameter when it contains the true population mean. The empirical coverage rate is the proportion of the 10,000 intervals that contain the true mean.

The 95% results are displayed in a labeled HTML table, while the CSV file retains results for all three confidence levels. Because this is a simulation, empirical coverage should be close to, but will not necessarily equal, the nominal confidence level. The Monte Carlo standard error quantifies simulation variability in the estimated coverage rate.

### 3. Hypothesis testing using a simulated null distribution

The final experiment considers a two-sided test of a fair-coin null hypothesis:

$$\[
H_0: p=0.50
\qquad\text{versus}\qquad
H_1: p\ne 0.50.
\]$$

The number of tosses is 100, and the observed number of heads is 62. Under the null hypothesis,

$$\[
X\mid H_0 \sim \operatorname{Binomial}(100,0.50).
\]$$

The script simulates 10,000 values from this null distribution. It identifies outcomes at least as far from the null expectation (50 heads) as the observed result, and estimates the two-sided p-value using the simulated tail probability. It also calculates the exact two-sided binomial-test p-value for comparison.

For significance levels of 10%, 5%, and 1%, the script calculates equal-tail critical values and the corresponding discrete rejection regions. Since the binomial distribution is discrete, the actual null rejection probability can be smaller than the nominal significance level.

In the interactive null-distribution figure:

- **Orange bars** represent outcomes included in the two-sided p-value tails (outcomes at least as extreme as the observed statistic).
- **Red bars** represent outcomes in the selected rejection region that are not part of the p-value tails.
- **Blue bars** represent outcomes outside the rejection region and outside the p-value tails.
- Dashed vertical lines mark the critical boundaries; dotted lines mark the observed-extremeness cutoffs.

Use the significance-level dropdown to compare how the rejection region changes with \(\alpha\). The p-value is the probability, under the null hypothesis, of obtaining a test statistic at least as extreme as the observed statistic. It is not the probability that the null hypothesis is true.

## Data sources and simulation settings

### Data sources

There are **no external data sources** in this project. The wage observations and coin-toss outcomes are synthetic and generated within the R script:

- Wage sample and bootstrap resamples: generated with `rnorm()` and `sample(..., replace = TRUE)`.
- Repeated samples for coverage: generated with `rnorm()`.
- Null-distribution draws: generated with `rbinom()`.

The population parameters are known by construction for these teaching examples. The wage population is Normal with mean 100 and standard deviation 20. The hypothesis-testing example assumes a fair coin with head probability 0.50.

### Main default settings

| Component | Default setting |
|---|---:|
| Random seed | 6400 |
| Wage population mean | 100 |
| Wage population SD | 20 |
| Original wage sample size | 25 |
| Bootstrap repetitions | 1,000 |
| Coverage repetitions per sample size | 10,000 |
| Coverage sample sizes | 10, 25, 50, 100 |
| Null simulation repetitions | 10,000 |
| Coin tosses | 100 |
| Null probability of heads | 0.50 |
| Observed number of heads | 62 |

The settings are defined at the beginning of `code/bootstrap_and_stimulation.R`. Change these values to explore alternative simulation designs. The script uses separate, deterministic random seeds for the main simulation sections so that the results can be reproduced.

## Repository structure

```text
Bootstrap/
├── Bootstrap.Rproj
├── README.md
├── code/
│   └── bootstrap_and_stimulation.R
├── figures/
│   ├── bootstrap_confidence_interval.html
│   ├── bootstrap_distribution.html
│   ├── bootstrap_parameters.html
│   ├── coverage_simulation.html
│   ├── null_distribution.html
│   └── *_files/                 # Supporting assets for interactive HTML widgets
└── results/
    ├── original_wage_sample.csv
    ├── bootstrap_summary.csv
    ├── bootstrap_confidence_intervals.csv
    ├── bootstrap_distribution.csv
    ├── coverage_summary.csv
    ├── hypothesis_test_summary.csv
    ├── rejection_critical_values.csv
    └── null_distribution.csv
```

The `figures/` directory contains HTML figures generated by the script and the supporting files required by the Plotly widgets. The `results/` directory contains machine-readable CSV outputs generated directly from the simulations. No results need to be manually copied into the report.

## How to reproduce the results

### Requirements

- R (the project was developed in R).
- The R packages `plotly` and `htmlwidgets`.

Install the required packages once:

```r
install.packages(c("plotly", "htmlwidgets"))
```

### Run the script

1. Clone or download this repository.
2. Open `Bootstrap.Rproj` in RStudio, or open the project folder in R.
3. Run the script:

```r
source("code/bootstrap_and_stimulation.R")
```

Alternatively, run it from a terminal:

```bash
Rscript code/bootstrap_and_stimulation.R
```

The script creates the `figures/` and `results/` folders if they do not already exist, then writes the HTML figures and CSV outputs to those folders. Output paths are resolved relative to the script location when the script is run from a file, so it can be launched from a different working directory.

### View the figures

After the script finishes, open the following HTML files in a web browser:

| Figure | What it shows |
|---|---|
| [Bootstrap confidence interval](figures/bootstrap_confidence_interval.html) | Bootstrap distribution, percentile confidence intervals, tail regions, and reference means |
| [Bootstrap distribution by resample count](figures/bootstrap_distribution.html) | How the bootstrap distribution changes with 250, 1,000, or 5,000 resamples |
| [Bootstrap distribution by parameters](figures/bootstrap_parameters.html) | Bootstrap distributions under different Normal means and standard deviations |
| [95% CI coverage simulation](figures/coverage_simulation.html) | Empirical coverage across sample sizes |
| [Null distribution and hypothesis test](figures/null_distribution.html) | Simulated binomial null distribution, p-value tails, and rejection regions |

Keep each HTML file next to its corresponding `*_files/` support directory. Moving or downloading only the HTML file may prevent some interactive components from loading.

## Programmatically generated outputs

| Output file | Contents |
|---|---|
| `original_wage_sample.csv` | The simulated original wage sample |
| `bootstrap_summary.csv` | Population mean, sample mean, sample SD, and bootstrap standard error |
| `bootstrap_confidence_intervals.csv` | 90%, 95%, and 99% percentile bootstrap confidence intervals |
| `bootstrap_distribution.csv` | Bootstrap sample means from the main resampling experiment |
| `coverage_summary.csv` | Empirical coverage, number of covered intervals, Monte Carlo SE, and average interval width |
| `hypothesis_test_summary.csv` | Observed statistic, null expectation, simulated and exact p-values, and test decision |
| `rejection_critical_values.csv` | Critical values and actual null rejection probabilities at each significance level |
| `null_distribution.csv` | Simulated and theoretical probabilities for each possible number of heads |

## Selected results from the committed simulation

The following values summarize the results currently stored in the repository. They are outputs from the default seed and settings, not universal theoretical constants.

### 95% confidence-interval coverage

| Sample size | Empirical coverage |
|---:|---:|
| 10 | 94.87% |
| 25 | 95.22% |
| 50 | 94.96% |
| 100 | 94.89% |

All four empirical rates are close to the nominal 95% level, as expected from repeated simulation. The interval width decreases as sample size increases.

### Two-sided coin-toss test

For 62 heads in 100 tosses under the fair-coin null:

| Quantity | Result |
|---|---:|
| Simulated two-sided p-value | 0.02360 |
| Exact two-sided binomial p-value | 0.02098 |
| Decision at the 5% significance level | Reject \(H_0\) |

The exact p-value is below 0.05, so the null hypothesis is rejected at the 5% significance level. At the 1% level, the critical region does not include the observed result.



# Did Automatic Enrolment Work? A 14-Year Look at UK Workplace Pension Participation

I tidied and analysed the UK Department for Work and Pensions' (DWP) workplace pension participation data,
spanning 2009 to 2025 and released just days before I started this project (30 July 2026). I wanted to
test whether Automatic Enrolment (AE), introduced in 2012, actually worked as a behavioural "nudge" - 
and whether its effects held up through the economic shocks of the years since. Some of what I found lined 
up with my expectations; some of it didn't.

## Data

The data comes from the UK Department for Work and Pensions (DWP): their workplace pension participation and
savings trends publication, covering 2009 to 2025. It breaks participation and saving behaviour down by a 
wide range of factors. Analysing every possible breakdown wasn't feasible, so I focused on three I felt 
offered the most diverse insight - age, gender, and opt-out (stopping saving) rates.

It is important go note that the data was published as a human-readable spreadsheet, not an analysis-ready 
one - tables were laid out for a person reading in Excel, not for code, which meant a genuine amount of the 
work here was in the reshaping and cleaning stage before any analysis could begin.

## Method

I started by plotting the raw trends for each data set - participation by age, participation by gender, and
stopping rates by age, gender, and earnings band - to see what patterns were visible before running any formal
tests. The plots pointed to two things - a sharp rise in participation after 2012 (when Automatic Enrolment was
introduced), and a rise in stopping-saving rates from around 2021-22 onwards, concentrated in younger age groups.

However, a visible trend on a chart isn't the same as a statistically confirmed effect, though it could just be
normal year-to-year variation. To test this properly, I used segmented variation (a technique from interrupted time
series analysis): a model that checks whether a specific point in time (in this case, a policy change) causes a
genuine shift in either the level of a variable, its trend, or both - rather than just relying on how a line looks
by eye. 

I ran this twice - once testing whether Automatic Enrolment (2012) created a structural break in participation
rates, and once testing whether the cost-of-living period (from ~2021-22) created a structual break in 
stopping-saving rates. The second time, I also checked whether that effect differed by age group.

## Results

### Participation by age

In 2009, there was a roughly 40 percentage-point gap in pension participation between the youngest workers
(22-25, ~30%) and the oldest (51-55, ~70%). Following Automatic Enrolment in 2012, every age group's participation
rose sharply, but the youngest group rose the most dramatically - increasing from ~30% to over 85% by 2018. By 2018,
all age groups had converged to within a few percentage points of each other, around 85-90%. This is the clearest
evidence in the dataset that AE worked as a behavioural default - the group least likely to opt in voluntarily was
the group the nudge moved the most.

### Participation by gender

Gender shows a smaller effect. Women had a slightly higher participation rate than men throughout the entire period
(e.g. 61% vs 56% in 2009), and this gap barely changed in relative terms even as both groups were pulled upward by
AE - by 2025, women sit at 90% and men at 89%. Unlike age, where AE closed a large pre-existing gap, gender shows AE
bringing both groups up by roughly the same amount without meaningfully altering the (already small) gap between them.

### The AE structural break test

A segmented regression on the overall age-participation trend found every coefficient pointing in the hypothesised
direction - a flat pre-2012 trend, an upward increase followed by a steeper post-2012 slope - and the model explained
the large majority of variance in participation over time (R² = 0.87). However, none of the individual coefficients
reached statistical significance, likely because the model had only 17 annual data points to work with. The visual 
pattern (a steep rise flattening into a plateau by ~2018) also suggests the true relationship is a curve rather than
two straight lines, which a simple segmented model does not fully capture. In summary, the effect looks real and large,
but this dataset does not have enough statistical power to confirm it beyond doubt.

### Stopping rates and the cost-of-living era

The rate at which AE-eligible employees stop saving stayed broadly flat (with a regular seasonal pattern) from 2014
until around 2021-22, then began steadily increasing - rising from a baseline of roughly 0.6-0.7% to over 1% by 2025.
A segmented regression confirmed this was not a sudden shock response (no significant jump at the cutoff point, and 
notable no spike during COVID itself) but a genuine, borderline-significant change in trend (p=0.054) from that point
onward. Breaking this down by age band sharpened the findings considerably - the youngest workers (22-29) showed a
significantly steeper rise in their stopping rate than every older age group (p<0.05 to p<0.001), an effect strong
enough to be clearly confirmed with the larger quarterly sample. In simple terms, the nudge held firm against a sudden
shock (COVID), but is measurably eroding under sustained financial pressure - and that erosion is concentrated almost
entirely in younger workers, who have the least financial slack to keep saving when money becomes a problem.

## Limitations

A few limitations are worth flagging. First, the segmented regression testing whether Automatic Enrolment created a
structural break in participation had only 17 annual data points to work with, which limits its statistical power - 
the effect looked large and directionally consistent, but could not be confirmed with a confidence at this sample size.
Second, the high stopping-saving rate among the £60,000+ earnings band likely reflects a different mechanism than the
hardship-driven opt-outs seen in younger and lower-income groups - UK pension tax rules reduce the tax-free contribution
allowance for higher earners, which can make deliberately reducing contributions a rational tax-planning decision rather
than a sign of financial strain. This dataset cannot distinguish between the two, so that finding should be read with
more caution. Third, when testing the overall age-participation trend, I averaged across groups without weighting by how
many people were actually in each group - meaning a small age band and a larger one counted equally toward the "overall"
trend, which is a simplification worth bearing in mind. 

## Project Structure + How to Reproduce

### Project structure

pension_ae_project/
├── README.md                    # this file
├── data/
│   ├── raw/                     # original DWP workbook, untouched
│   └── processed/                # cleaned, tidy CSVs (output of 01_load_and_tidy.Rmd)
│       ├── age_participation.csv
│       ├── gender_participation.csv
│       └── stopping_rates.csv
└── scripts/
    ├── 01_load_and_tidy.Rmd     # reads raw Excel sheets, reshapes to tidy long format
    └── 02_explore.Rmd           # exploratory plots + segmented regression analysis

### How to run it

1. Download the source data from DWP’s publication page (https://www.gov.uk/government/statistics/workplace-pension-participation-and-savings-trends-2009-to-2025) and place it in data/raw/.
2. Run scripts/01_load_and_tidy.Rmd to produce the tidy CSVs in data/processed/.
3. Run scripts/02_explore.Rmd to reproduce the plots and regression results.

Requires R with the readxl, dplyr, tidyr, stringr, and ggplot2 packages installed.

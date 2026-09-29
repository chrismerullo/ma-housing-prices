# What Explains Home Prices Across Massachusetts Towns?

## Question
Home prices vary enormously across Massachusetts, from under $400,000 in some western towns to over $1 million near Boston. This project asks which town-level characteristics (income, education, population, and distance from Boston) are associated with those differences.

## Data
- **Home prices:** Zillow Home Value Index (ZHVI), all homes (single-family and condo), smoothed and seasonally adjusted, August 2026.
- **Income, education, population:** American Community Survey 5-year estimates (2024), pulled with the `tidycensus` package. Education is the share of adults 25+ with a bachelor's degree or higher.
- **Distance to Boston:** Miles from each town's geographic center to Boston's center, calculated from Census town boundaries.

Zillow lists 354 Massachusetts places. After cleaning town names to match Census spellings, dropping villages that are part of larger towns (e.g., Turners Falls, Harwich Port) and one town without income data (Gosnold), the final sample is **346 towns**.

## Method
I estimated OLS regressions in R with log home price as the outcome. A first model using raw dollar prices showed a fan-shaped pattern in the residuals (larger spread at higher incomes), so I logged price, income, and population. Coefficients on logged variables read as elasticities. I checked the residual plot for patterns and used heteroskedasticity-robust (HC1) standard errors for all reported results. I added predictors in stages to see how much each group contributed.

## Results
| Model | Predictors | Adjusted R² |
|---|---|---|
| 1 | Log income | 0.568 |
| 2 | + Education, population | 0.690 |
| 3 | + Distance to Boston | 0.696 |

In the full model (all coefficients significant at the 5% level or better with robust standard errors):
- **Education:** A 10-percentage-point higher share of adults with a bachelor's degree is associated with roughly 10% higher home prices, holding the other variables constant.
- **Income:** A 1% higher median household income is associated with about 0.47% higher home prices.
- **Population:** A 10% larger population is associated with about 0.4% higher prices, a small effect.
- **Distance to Boston:** A town 10% farther from Boston has prices about 0.8% lower. This is the weakest effect (p = 0.03), and adding it raised adjusted R² by only about 0.006, because income and education already capture much of the Boston-proximity pattern.

## Limitations
- These are associations, not causal effects.
- The model omits factors such as school quality, housing supply, local zoning, and property taxes.
- The Census reports median household income as "$250,000+" at the top end, so the highest-income towns are capped.
- ZHVI is a smoothed estimate of typical home values, not actual sale prices, and it mixes single-family homes and condos.
- Prices are from August 2026 while ACS estimates cover 2020 to 2024.
- Distance is measured center to center, which is an approximation of travel distance.

## Next steps
Add school quality and housing supply measures, and compare towns within commuting distance of Boston separately from the rest of the state.

## Reproducing
The full R script is in `housing_analysis.R`. It requires the `tidyverse`, `tidycensus`, `sf`, `sandwich`, and `lmtest` packages and a free Census API key.

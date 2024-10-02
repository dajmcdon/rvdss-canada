# Respiratory Virus Detections in Canada

This is a data repository for respiratory virus detections in Canada collected by the Respiratory Virus Detection Surveillance System (RVDSS) and published by the Public Health Agency of Canada (PHAC).

The data was historically reported in weekly reports, but have moved to an interactive dashboard as of week 24 of the 2023-2024 season (week ending June 15, 2024). Historic reports and the interactive dashboard can be found [here](https://www.canada.ca/en/public-health/services/surveillance/respiratory-virus-detections-canada.html).

## Directory Structure

For each season, there are 2 or 3 files:

-   positive_tests.csv

    -   Shows the percentage of tests for each virus that are positive by week

    -   Regional level (with national aggregation)

    -   Has revisions

    -   Corresponds to table 1, usually titled "Respiratory virus detections for the week ending..."

        -   eg. [Table 1: Respiratory virus detections for the week ending July 16, 2022 (surveillance week 2022-28)](https://www.canada.ca/en/public-health/services/surveillance/respiratory-virus-detections-canada/2021-2022/week-28-ending-july-16-2022.html#a2)

-   number_of_detections.csv

    -   Shows the number of positive tests/detection for each virus by week

    -   National level

    -   No revisions

    -   Not always available

    -   Corresponds to figure 2, usually titled "Number of positive respiratory virus tests"

        -   eg. [Figure 2: Number of positive respiratory virus tests reported by participating laboratories in Canada by surveillance week](<https://www.canada.ca/en/public-health/services/surveillance/respiratory-virus-detections-canada/2021-2022/week-28-ending-july-16-2022.html#a4>)

-   respiratory_detections.csv

    -   Shows the positive tests for each virus (including subtypes) by weel

    -   Lab level (with regional aggregation)

    -   Has revisions

    -   Corresponds to figures 3-9, usually titles "Positive [virus] tests (%)..."

        -   eg. [Figure 3: Positive influenza (Flu) tests (%) in Canada by region by surveillance week](<https://www.canada.ca/en/public-health/services/surveillance/respiratory-virus-detections-canada/2021-2022/week-28-ending-july-16-2022.html#a5>)

## Column Names

-   epiweek: the epidemiological week, in the format of yearweek (eg. 201712 is the 12th epidemiological week of 2017). The list of epidemiological weeks for the current 2024-2025 season can be found [here](<https://www.canada.ca/en/public-health/services/diseases/flu-influenza/influenza-surveillance/fluwatch-weeks-calendar.html>)

-   time_value: the last day of the epiweek

-   issue: the date (still the end of an epiweek) that the observation was revised

-   geo_type: the type of geographical location

-   geo_value: the actual geographical location

-   [virus]\_tests: the total number of tests for a given virus

-   [virus]\_positive_tests: the number of tests for a given virus that are positive

-   [virus]\_pct_positive: the percentage of tests for a given virus that are positive

## Reading the data directly

For convenience, we provide some code snippets to read in this data programmatically.

### In Python

Read in data for a single season

``` python
import pandas as pd

base_url = "https://raw.githubusercontent.com/dajmcdon/rvdss-canada/main/data/"
one_season = "season_2024_2025/positive_tests.csv"
positive_tests= pd.read_csv(base_url+one_season)
```

Read in data for all seasons, merge into a single dataframe

``` python

import pandas as pd

base_url = "https://raw.githubusercontent.com/dajmcdon/rvdss-canada/main/data/"
years = list(range(2013,2025))
all_seasons = ["season_"+str(y)+"_"+str(y+1) for y in years]
all_seasons = [pd.read_csv(base_url+season+"/positive_tests.csv") for season in all_seasons]
all_seasons = [df.set_index(['epiweek', 'time_value', 'issue', 'geo_type', 'geo_value']) for df in all_seasons]

positive_tests = pd.concat(all_seasons)
```

### In R

Read in data for a single season

``` r
library(readr)
base_url <- "https://raw.githubusercontent.com/dajmcdon/rvdss-canada/main/data/"
one_season <- "season_2024_2025/positive_tests.csv"
positive_tests <- read_csv(paste0(base_url, one_season))
```

Read in data for all seasons, merge into a single tibble

``` r
library(readr)
library(dplyr)
years <- 2013:2024
all_seasons <- paste0("season_", years, "_", years + 1, "/positive_tests.csv")
all_seasons <- lapply(all_seasons, \(.x) read_csv(paste0(base_url, .x))) # ~ 30MB
positive_tests <- bind_rows(all_seasons)
```

You can also convert this data to a more compact format using CMU Delphi Group's [`{epiprocess}`](https://cmu-delphi.github.io/epiprocess) R Package.

``` r
remotes::install_github("cmu-delphi/epiprocess")
library(epiprocess)
pt <- as_epi_archive(positive_tests, other_keys = "epiweek", compactify = TRUE) # ~ 3.3MB
```

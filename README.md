# Respiratory Virus Detections in Canada

This is a data repository for respiratory virus detections in Canada collected by the Respiratory Virus Detection Surveillance System (RVDSS) and published by the Public Health Agency of Canada (PHAC). 

The data was historically reported in weekly reports, but have moved to an interactive dashboard as of week 24 of the 2023-2024 season (week ending June 15, 2024). Historic reports and the interactive dashboard can be found [here](https://www.canada.ca/en/public-health/services/surveillance/respiratory-virus-detections-canada.html).

## Reading the data directly

For convenience, we provide some code snippets to read in this data programmatically.

### In Python

Read in data for a single season

```python
import pandas as pd

base_url = "https://raw.githubusercontent.com/dajmcdon/rvdss-canada/main/data/"
one_season = "season_2024_2025/positive_tests.csv"
positive_tests= pd.read_csv(base_url+one_season)
```

Read in data for all seasons, merge into a single dataframe
```python

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

```r
library(readr)
base_url <- "https://raw.githubusercontent.com/dajmcdon/rvdss-canada/main/data/"
one_season <- "season_2024_2025/positive_tests.csv"
positive_tests <- read_csv(paste0(base_url, one_season))
```

Read in data for all seasons, merge into a single tibble

```r
library(readr)
library(dplyr)
years <- 2013:2024
all_seasons <- paste0("season_", years, "_", years + 1, "/positive_tests.csv")
all_seasons <- lapply(all_seasons, \(.x) read_csv(paste0(base_url, .x))) # ~ 30MB
positive_tests <- bind_rows(all_seasons)
```

You can also convert this data to a more compact format using CMU Delphi Group's [`{epiprocess}`](https://cmu-delphi.github.io/epiprocess)
R Package.

```r
remotes::install_github("cmu-delphi/epiprocess")
library(epiprocess)
pt <- as_epi_archive(positive_tests, other_keys = "epiweek", compactify = TRUE) # ~ 3.3MB
```

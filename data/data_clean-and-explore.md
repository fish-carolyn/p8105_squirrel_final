Data cleaning & exploration
================
2023-12-02

**Data cleaning check list:**

- every squirrel has a unique ID \[x\]
- every squirrel has geodata (`hectare`, `x`, `y`) \[x\]
- non-missing data for:
  - primary fur color \[x\]
  - age \[x\]
  - location description \[x\]
  - activity descriptions \[x\]
  - date \[x\]
- continuous variables as numeric \[x\]
- recode binary variables with more meaningful character descriptions
  \[\]
- create binary for movement, eating related, vocalizations, reactions
  \[\]
- document number of squirrels that fit all criteria \[\]
- ensure any missing values are coded appropriately for tables, etc.
  \[\]

#### get data

Read in 2018 Squirrel Census data from:
<https://data.cityofnewyork.us/Environment/2018-Central-Park-Squirrel-Census-Squirrel-Data/vfnx-vebw>.

``` r
df_dirty_squirrel <- read_csv("2018_Central_Park_Squirrel_Census_-_Squirrel_Data_20231202.csv")
```

Use skimmr to check variable completeness, types, etc.

``` r
skimr::skim(df_dirty_squirrel)
```

|                                                  |                   |
|:-------------------------------------------------|:------------------|
| Name                                             | df_dirty_squirrel |
| Number of rows                                   | 3023              |
| Number of columns                                | 31                |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |                   |
| Column type frequency:                           |                   |
| character                                        | 14                |
| logical                                          | 13                |
| numeric                                          | 4                 |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |                   |
| Group variables                                  | None              |

Data summary

**Variable type: character**

| skim_variable                              | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:-------------------------------------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| Unique Squirrel ID                         |         0 |          1.00 |  13 |  14 |     0 |     3018 |          0 |
| Hectare                                    |         0 |          1.00 |   3 |   3 |     0 |      339 |          0 |
| Shift                                      |         0 |          1.00 |   2 |   2 |     0 |        2 |          0 |
| Age                                        |       121 |          0.96 |   1 |   8 |     0 |        3 |          0 |
| Primary Fur Color                          |        55 |          0.98 |   4 |   8 |     0 |        3 |          0 |
| Highlight Fur Color                        |      1086 |          0.64 |   4 |  22 |     0 |       10 |          0 |
| Combination of Primary and Highlight Color |         0 |          1.00 |   1 |  27 |     0 |       22 |          0 |
| Color notes                                |      2841 |          0.06 |   3 | 153 |     0 |      135 |          0 |
| Location                                   |        64 |          0.98 |  12 |  12 |     0 |        2 |          0 |
| Above Ground Sighter Measurement           |       114 |          0.96 |   1 |   5 |     0 |       41 |          0 |
| Specific Location                          |      2547 |          0.16 |   4 | 102 |     0 |      304 |          0 |
| Other Activities                           |      2586 |          0.14 |   4 | 132 |     0 |      307 |          0 |
| Other Interactions                         |      2783 |          0.08 |   2 | 106 |     0 |      197 |          0 |
| Lat/Long                                   |         0 |          1.00 |  38 |  45 |     0 |     3023 |          0 |

**Variable type: logical**

| skim_variable | n_missing | complete_rate | mean | count                |
|:--------------|----------:|--------------:|-----:|:---------------------|
| Running       |         0 |             1 | 0.24 | FAL: 2293, TRU: 730  |
| Chasing       |         0 |             1 | 0.09 | FAL: 2744, TRU: 279  |
| Climbing      |         0 |             1 | 0.22 | FAL: 2365, TRU: 658  |
| Eating        |         0 |             1 | 0.25 | FAL: 2263, TRU: 760  |
| Foraging      |         0 |             1 | 0.47 | FAL: 1588, TRU: 1435 |
| Kuks          |         0 |             1 | 0.03 | FAL: 2921, TRU: 102  |
| Quaas         |         0 |             1 | 0.02 | FAL: 2973, TRU: 50   |
| Moans         |         0 |             1 | 0.00 | FAL: 3020, TRU: 3    |
| Tail flags    |         0 |             1 | 0.05 | FAL: 2868, TRU: 155  |
| Tail twitches |         0 |             1 | 0.14 | FAL: 2589, TRU: 434  |
| Approaches    |         0 |             1 | 0.06 | FAL: 2845, TRU: 178  |
| Indifferent   |         0 |             1 | 0.48 | FAL: 1569, TRU: 1454 |
| Runs from     |         0 |             1 | 0.22 | FAL: 2345, TRU: 678  |

**Variable type: numeric**

| skim_variable           | n_missing | complete_rate |        mean |       sd |          p0 |         p25 |         p50 |         p75 |        p100 | hist  |
|:------------------------|----------:|--------------:|------------:|---------:|------------:|------------:|------------:|------------:|------------:|:------|
| X                       |         0 |             1 |      -73.97 |     0.01 |      -73.98 |      -73.97 |      -73.97 |      -73.96 |      -73.95 | ▅▇▅▆▂ |
| Y                       |         0 |             1 |       40.78 |     0.01 |       40.76 |       40.77 |       40.78 |       40.79 |       40.80 | ▇▇▃▅▆ |
| Date                    |         0 |             1 | 10119487.40 | 42466.71 | 10062018.00 | 10082018.00 | 10122018.00 | 10142018.00 | 10202018.00 | ▇▂▇▂▃ |
| Hectare Squirrel Number |         0 |             1 |        4.12 |     3.10 |        1.00 |        2.00 |        3.00 |        6.00 |       23.00 | ▇▂▁▁▁ |

#### clean variable names and remove missing data

``` r
df_washing_squirrel = df_dirty_squirrel |> 
  janitor::clean_names() |> 
  filter(
    complete.cases(unique_squirrel_id, 
                   x, y, 
                   primary_fur_color, 
                   age, 
                   location
                   )
  ) |> 
  mutate(
    x = as.numeric(x), 
    y= as.numeric(y), 
    day = as.numeric(substr(date, 3, 4)),     #date results in a lot of parsing failures so I'm using a work around
    month = as.numeric(substr(date, 1, 2)),  
    year = as.numeric(substr(date, 5, 11)), 
    date_assembled = paste(month, day, year, sep = "-") 
  ) |> 
  mutate(
    date = lubridate::mdy(date_assembled)
  ) |> 
  filter(!is.na(date))


df_washing_squirrel |> 
  select(
    running:eating
  ) |> count()
```

    ## # A tibble: 1 × 1
    ##       n
    ##   <int>
    ## 1  2824

``` r
max(df_washing_squirrel |>  pull(date))
```

    ## [1] "2018-10-20"

In the cleaner version of the data for which only squirrels with
non-missing data have been kept there are data for 2819 distinct
squirrels. The data was collected between 2018-10-20 and 2018-10-06.

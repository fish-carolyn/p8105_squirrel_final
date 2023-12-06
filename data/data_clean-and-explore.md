squirrel data clean and explore
================

*Summary*

The `clean_squirrel_2018` data is a wide data set with one row per
squirrel. These squirrels have nonmissing data for the most critical
variables for analysis (identifier, geodata, fur color, age, activity,
date).

### Data cleaning check list:

- every squirrel has a unique ID \[x\]
- every squirrel has geodata (`hectare`, `x`, `y`) \[x\]
- non-missing data for:
  - primary fur color \[x\]
  - age \[x\]
  - location description \[x\]
  - activity descriptions \[x\]
  - date \[x\]
- continuous variables as numeric \[x\]
- document number of squirrels that fit all criteria \[x\]

### Get data

Read in 2018 Squirrel Census data from:
<https://data.cityofnewyork.us/Environment/2018-Central-Park-Squirrel-Census-Squirrel-Data/vfnx-vebw>.

Use skimmr to check variable completeness, types, etc.

``` r
df_dirty_squirrel <- read_csv("2018_Central_Park_Squirrel_Census_-_Squirrel_Data_20231204.csv")

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

### Clean variable names and remove missing data

``` r
df_clean_squirrel = df_dirty_squirrel |> 
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
```

In the cleaner version of the data for which only squirrels with
non-missing data have been kept, there are data for 2819 distinct
squirrels. The data was collected between 2018-10-06 and 2018-10-20.

### Adding squirrel names

``` r
squirrel_names <- c(
  "Sparky", "Nuttail", "Cocoa", "Whiskertail", "Hazel", "Paws", "Sable", "Furryfluff", "Almond","Bushytoes", "Pecan", "Snugglewhisk", "Popcorn", "Nutbuster", "Chestnut", "Snickersnack", "Butterscotch", "Fluffpaws", "Maple", "Tangletoe", "Walnut", "Giggletail", "Acorn", "Snickersnout", "Pistachio", "Wigglesnout", "Cashew", "Sillytail", "Toffee", "Fluffernut", "Macadamia", "Wigglewhisk", "Cinnamon", "Nibbler", "Russet", "Snickerwhisk", "Caramel", "Fuzzernose", "Mocha", "Whiskernib", "S'mores", "Snickersnout", "Olive", "Gigglenose", "Mango", "Snuggletail", "Taffy", "Wigglesnout", "Marshmallow", "Nibbler", "Chestnut", "Scurrypaws", "Butter", "Pouncewhisk", "Truffle", "Fluffpounce", "Pumpkin", "Nibblesnout", "Cocoa", "Wigglepaws", "Peanut", "Whiskersnack", "Sesame", "Nuzzlenib", "Blueberry", "Fluffernibble", "Hazelnut", "Pouncetail", "Raspberry", "Whiskernibble", "Muffin", "Nutbounce", "Peach", "Snickerscuffle", "Ginger", "Fluffpounce", "Biscuit", "Nuzzlesnack", "Cherry", "Scurryfluff", "Bluebell", "Wigglewhisk", "Apricot", "Snugglesnack", "Plum", "Gigglesnout", "Sunny", "Snickersnuggle", "Banana", "Whiskerbounce", "Papaya", "Nutwhisk", "Coconut", "Snickersnuggle", "Mango", "Wiggletail", "Berry", "Gigglesnout", "Kiwi", "Pouncesnout", "Melon", "Nuzzlenose", "Tangerine", "Wigglepaws", "Plum", "Scurrywhisk", "Pumpkin", "Fluffertail", "Grape", "Nibblesnack", "Blackberry", "Pouncepaws", "Raspberry", "Nutwhisk", "Peach", "Fluffertail", "Kiwi", "Wigglepaws", "Mango", "Giggletail", "Tangerine", "Fluffernose", "Grape", "Whiskertail", "Blackberry", "Nutbounce", "Blueberry", "Scurrypaws", "Cranberry", "Wiggletail", "Mulberry", "Pouncewhisk", "Orange", "Nibbler", "Pomegranate", "Nutsnack", "Strawberry", "Wigglewhisk", "Lemon", "Nuzzlenose", "Lime", "Snickerscuffle", "Watermelon", "Snickersnuggle", "Apple", "Nutbounce", "Cherry", "Whiskertail", "Grapes", "Fluffertail", "Peach", "Wigglepaws", "Sunny", "Chestnut", "Maple", "Peanut", "Hazel", "Walnut", "Oreo", "Marshmallow", "Snickers","Buttercup", "Pumpkin", "Pinecone", "Cocoa", "Blueberry", "Raspberry", "Sparky", "Furry", "Chestnut", "Pippin", "Acorn", "Pistachio", "Hazelnut", "Cinnamon", "Mocha", "Pecan", "Acorn", "Biscuit", "Almond", "S'mores", "Wiggles", "Twix", "Bubbles", "Doodle", "Wombat", "Fluffernutter", "Muffin", "Fuzzball", "Cupcake", "Wonton", "Snuggles", "Pippin", "Moonbeam", "Cheesecake", "Jigsaw", "Cashew", "Jumper", "Marmalade", "Nimbus", "Toffee", "Poppy", "Squirt", "Munchkin", "Tidbit","Jingle", "Piston", "Fandango", "Jolt", "Nectar", "Sprinkle", "Nudge", "Tickles", "Hiccup", "Sassafras", "Zippy", "Daffodil", "Raisin", "Frizzle", "Caramel", "Stardust", "Quicksilver", "Nishi", "Jeff", "Selina", "Ghislaine", "Carolyn", "Camille", "Sanika", "Z", "Will", "Bella", "Frank", "Rose", "Gerry", "Ryan", "Fuzzy", "Wuzzy", "Fern", "Rain", "Bob", "Larry", "Doug", "John", "Mimosa", "Juicy", "Megan", "Lizzie", "Santa", "Jingles", "Lucy", "Lady", "King", "Queen", "Prince", "Madam", "Sir", "Czar", "Princess", "Your Highness", "Ronald", "Tapioca", "Boba", "Candycane", "Moscato", "Shrek", "Legolas", "Adrian", "Reggie", "Sophie", "Francesca", "Spiderman", "Peter", "Super", "Henry", "Sebastian", "Frodo", "Gandalf", "Bilbo"
)
```

``` r
squirrel_names <- unique(squirrel_names)

generated_squirrel_names <- data.frame(name = character(), stringsAsFactors = FALSE)

num_iterations <- 3000 

# Loop to randomly select two names, concatenate them, and store them
for (i in 1:num_iterations) {

  selected_names <- sample(squirrel_names, 2)
  
  new_row <- data.frame(name = paste(selected_names, collapse = " "), stringsAsFactors = FALSE)
  generated_squirrel_names <- rbind(generated_squirrel_names, new_row)
}
```

The squirrel names are exported as a .csv (`squirrel_names_final`). I am
not showing this here because I do not want to overwrite the
`squirrel_names_final` file every time this .Rmd is knit.

``` r
squirrel_names_final = read_csv("squirrel_names_final.csv")

df_clean_squirrel_names <- df_clean_squirrel  |> 
  mutate(name_key = row_number())

# Randomly shuffle the rows of names
squirrel_names_final <- squirrel_names_final |> 
  mutate(name_key = row_number())

# Merge the shuffled names_df with main_df based on the row index
df_clean_squirrel_names <- df_clean_squirrel_names  |> 
  left_join(squirrel_names_final, by = c("name_key"))

# If you want to keep only the Squirrel_ID and Assigned_Name columns
df_clean_squirrel_names <- df_clean_squirrel_names  |> 
  select(-name_key)
```

### Export clean data

``` r
save_path = "clean_squirrel_2018.csv"

write_csv(df_clean_squirrel_names, save_path, na="")
```

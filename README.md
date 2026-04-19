
# Analysis on NBA players statistical performance

#### Dominic DiRe, Blake Lewis, Will Stone

## Introduction

This project explores NBA player and team statistics pulled from the NBA
API across 5 seasons (2020-21 season through 2024-25 season). Basketball
is a sport with a great tie to statistics, and modern data collection
allows us to track how individual players and the league as a whole
evolve over time, although it’s just 5 seasons. Analysis on this topic
can reveal trends in player development, positional changes in the
modern NBA, and how elite players separate themselves from the rest of
the league. The goal is to visualize league-wide stat distributions and
track individual player development over time, with a focus on Luka
Doncic, the future face of the NBA. Who we believe might be able to be a
good basis to separate the stars from the future hall of famers.

In pursuit of the goal stated above, we will explore the following
questions:

1.  What does the distribution of key stats (points, rebounds, assists)
    look like across the league, and how have those distributions
    shifted across seasons?

2.  Which players are the most efficient scorers? How does scoring
    volume relate to shooting efficiency?

3.  How has Luka Doncic developed from season to season across points,
    assists, rebounds, and shooting percentages?

4.  How do Luka’s per-game stats compare to league averages each season?

These are the main questions we are looking to answer through the
completion of this project. With the findings we will be able to draw
meaningful conclusions on player development and league-wide trends in
the NBA.

## Data

### Structure

Data was pulled from the NBA Stats API (`stats.nba.com`) using the
`nba_api` Python package. The python file was basic but too long and
embarrassing to add, if divine intervention strikes we can add it.
Player game logs were collected for every regular season game across 5
seasons and aggregated into per-game averages. This produced one CSV per
season for league-wide stats.Also there is another separate combined CSV
for Luka Doncic spanning all 5 seasons.

**League CSVs** (`league_stats_YYYY-YY.csv`) — one row per player,
containing per-game averages for all NBA players in that regular season.

**Luka CSV** (`luka_career_stats.csv`) — one row per season, pulled from
the PlayerCareerStats endpoint.

**Columns included:** `PLAYER_ID`, `PLAYER_NAME`, `TEAM_ABBREVIATION`,
`GP`, `MIN`, `PTS`, `REB`, `AST`, `STL`, `BLK`, `FG_PCT`, `FG3_PCT`,
`FT_PCT`, `TOV`, `PLUS_MINUS`

### Loading

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.2.0     ✔ readr     2.1.6
    ## ✔ forcats   1.0.1     ✔ stringr   1.6.0
    ## ✔ ggplot2   4.0.2     ✔ tibble    3.3.1
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.2
    ## ✔ purrr     1.2.1     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
league_2021 <- read_csv("league_stats_2020-21.csv")
```

    ## Rows: 626 Columns: 15
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (2): PLAYER_NAME, TEAM_ABBREVIATION
    ## dbl (13): PLAYER_ID, GP, MIN, PTS, REB, AST, STL, BLK, FG_PCT, FG3_PCT, FT_P...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
league_2022 <- read_csv("league_stats_2021-22.csv")
```

    ## Rows: 715 Columns: 15
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (2): PLAYER_NAME, TEAM_ABBREVIATION
    ## dbl (13): PLAYER_ID, GP, MIN, PTS, REB, AST, STL, BLK, FG_PCT, FG3_PCT, FT_P...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
league_2023 <- read_csv("league_stats_2022-23.csv")
```

    ## Rows: 609 Columns: 15
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (2): PLAYER_NAME, TEAM_ABBREVIATION
    ## dbl (13): PLAYER_ID, GP, MIN, PTS, REB, AST, STL, BLK, FG_PCT, FG3_PCT, FT_P...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
league_2024 <- read_csv("league_stats_2023-24.csv")
```

    ## Rows: 657 Columns: 15
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (2): PLAYER_NAME, TEAM_ABBREVIATION
    ## dbl (13): PLAYER_ID, GP, MIN, PTS, REB, AST, STL, BLK, FG_PCT, FG3_PCT, FT_P...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
league_2025 <- read_csv("league_stats_2024-25.csv")
```

    ## Rows: 654 Columns: 15
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (2): PLAYER_NAME, TEAM_ABBREVIATION
    ## dbl (13): PLAYER_ID, GP, MIN, PTS, REB, AST, STL, BLK, FG_PCT, FG3_PCT, FT_P...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
luka <- read_csv("luka_career_stats.csv")
```

    ## Rows: 7 Columns: 14
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (2): SEASON, TEAM_ABBREVIATION
    ## dbl (12): PLAYER_AGE, GP, MIN, PTS, REB, AST, STL, BLK, FG_PCT, FG3_PCT, FT_...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

### Cleaning

Since each league CSV covers a single season but contains no season
identifier, so we added a `SEASON` column to each dataframe before
combining them. Without the column there wouldn’t be a way to tell where
data came from

``` r
league_2021$SEASON <- "2020-21"
league_2022$SEASON <- "2021-22"
league_2023$SEASON <- "2022-23"
league_2024$SEASON <- "2023-24"
league_2025$SEASON <- "2024-25"
```

With the season identifier in place, we combined all 5 dataframes into a
single `master` dataframe using `bind_rows`. `left_join` merges datasets
horizontally but for this dataset that doesn’t make as much sense. So we
used `bind_rows`, which stacks datasets vertically. This is appropriate
here because all 5 league CSVs share identical columns — they are the
same data structure repeated across different seasons. Which makes this
process quite easy.

``` r
master <- bind_rows(league_2021, league_2022, league_2023, league_2024, league_2025)
```

The raw data includes all players who appeared in at least one regular
season game, including players who played only a handful of minutes
across a few games. We filtered to players with at least 20 games played
to ensure per-game averages are based on a meaningful sample size. Also
if we didn’t add this we had a player who scored 30 points in a single
game appearance would show a 30.0 PPG average and distort league-wide
distributions.

``` r
master <- master %>% filter(GP >= 20)
```

Problem with the dataset as is, is that the traded players or players
who get picked up by multiple team in a season, the player has 2 team
rows. This caused a problem a bit later using `pivot_wider`, wher it
didn’t know what to pick essentially. Now this function is to sort out
players with multiple team rows in the same season.

``` r
master <- master %>%
  group_by(PLAYER_ID, PLAYER_NAME, SEASON) %>%
  summarise(
    GP = sum(GP),
    MIN = mean(MIN),
    PTS = mean(PTS),
    REB = mean(REB),
    AST = mean(AST),
    STL = mean(STL),
    BLK = mean(BLK),
    FG_PCT = mean(FG_PCT, na.rm = TRUE),
    FG3_PCT = mean(FG3_PCT, na.rm = TRUE),
    FT_PCT = mean(FT_PCT, na.rm = TRUE),
    TOV = mean(TOV),
    PLUS_MINUS = mean(PLUS_MINUS),
    .groups    = "drop"
  )
```

The `master` dataframe is in “long format”, where each player has one
row per season. This structure is wpuld be good for league-wide
distributions and filtering by season. However, for comparing the same
player’s stats side by side across multiple seasons, we also opted for
creating a “wide format” dataframe using `pivot_wider`. In
`master_wide`, each player occupies a single row and each stat gets its
own column per season, such as `PTS_2020-21` and `PTS_2021-22`. Players
who did not appear in a given season will have `NA` for that season’s
columns, newer rookies and retirees.

``` r
master_wide <- master %>%
  select(PLAYER_ID, PLAYER_NAME, SEASON, PTS, REB, AST, STL, BLK,
         FG_PCT, FG3_PCT, FT_PCT, TOV, PLUS_MINUS) %>%
  pivot_wider(
    names_from  = SEASON,
    values_from = c(PTS, REB, AST, STL, BLK, FG_PCT, FG3_PCT, FT_PCT, TOV, PLUS_MINUS),
    names_glue  = "{.value}_{SEASON}"
  )
```

From `master_wide` we derived a `PTS_growth` column by subtracting a
player’s 2020-21 scoring average from their 2024-25 average, this is
work in progress so fat but the idea is there. This gives us a simple
stat to identify which players improved the most in scoring over the
full 5 year span. Players with `NA` in either season, meaning they did
not meet the games played threshold in one of those years, will have
`NA` for `PTS_growth` as well.

``` r
master_wide <- master_wide %>%
  mutate(PTS_growth = `PTS_2024-25` - `PTS_2020-21`)
```

## Analysis

### What does the distribution of key stats look like across the league?

``` r
# placeholder
```

### Which players are the most efficient scorers?

``` r
# placeholder
```

### How has Luka Doncic developed season to season?

``` r
# placeholder
```

### How do Luka’s stats compare to league averages each season?

``` r
# placeholder
```

## Conclusion

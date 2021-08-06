# dplyr-to-pandas

> A small reference example, showing how to perform a `dplyr`-like data transformation in `pandas` with some non-trivial steps.

## R

```r
library(tidyverse)

url <- 'https://raw.githubusercontent.com/justmarkham/DAT8/master/data/chipotle.tsv'

df <- read_delim(url, delim = "\t")

df %>%
  mutate(item_price = as.numeric(str_trim(str_sub(item_price, start = 2)))) %>%
  group_by(item_name) %>%
  summarize(mean_item_price = mean(item_price)) %>%
  arrange(mean_item_price) %>% 
  filter(mean_item_price > 5, str_sub(item_name, 1, 1) == "C") %>%
  mutate(half_mean_item_price = mean_item_price / 2)
```

## Python

```python
import pandas as pd

url = 'https://raw.githubusercontent.com/justmarkham/DAT8/master/data/chipotle.tsv'

df = pd.read_csv(url, sep = '\t')

(
    df[["item_name", "item_price"]]
        .assign(item_price=df["item_price"].apply(lambda x: float(x[1:].strip())))
        .groupby(by="item_name")
        .agg({"item_price": "mean"})
        .rename(columns={"item_price": "mean_item_price"})
        .sort_values(by="mean_item_price")
        .reset_index()
        .query("mean_item_price > 5")
        .loc[lambda df: df['item_name'].str[0] == 'C']
        .assign(half_mean_item_price=lambda df: df["mean_item_price"] / 2)
)
```

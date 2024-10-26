+++
title = 'Tidyverse Notes'
date = 2024-02-28T18:56:24+08:00
draft = false
tags = ['R']
+++

## Removing Specific Columns in Data Frames
In the tidyverse, specifically using the dplyr package, you can remove specific columns from a data frame using the `select()` function along with the `-` operator to exclude columns you want to remove. Here's how you can do it:

```R
library(dplyr)

# Example data frame
df <- data.frame(
  A = 1:5,
  B = letters[1:5],
  C = c(TRUE, FALSE, TRUE, FALSE, TRUE),
  D = c(3.5, 2.1, 6.7, 4.2, 8.9)
)

# Removing specific columns (e.g., columns B and D)
df_removed <- df %>%
  select(-B, -D)

# Displaying the modified data frame
print(df_removed)
```

In this example, `select(-B, -D)` removes columns B and D from the data frame `df`. You can specify multiple columns to remove by separating them with a comma inside the `select()` function, each prefixed with the `-` sign.

### Filter Not NA
```R
df_removed_filtered <- df %>%
  select(-B, -D) %>%
  filter(!is.na(C))
```

## Segregating Data Options Other than Color

Besides using color, you can segregate data in a plot using other aesthetics such as shape, size, fill, and linetype. Here's how you can do it:

1. **Shape**: You can use different shapes for different categories. This is particularly useful if you're plotting black-and-white graphs or if you have colorblind audiences.

```R
# Using shape to segregate data
ggplot(df, aes(x = x, y = y, shape = category)) +
  geom_point() +
  labs(x = "X axis label", y = "Y axis label", title = "Scatter plot of Y against X with segregation by category (shape)")
```
**note** there's only like 5 or 6 shapes available so you can only use this when you have <= 5 or 6 categorical item.

2. **Size**: You can vary the size of the points based on a category.

```R
# Using size to segregate data
ggplot(df, aes(x = x, y = y, size = category)) +
  geom_point() +
  labs(x = "X axis label", y = "Y axis label", title = "Scatter plot of Y against X with segregation by category (size)")
```

**note** it's probably not a good idea to categorize things by size.

3. **Fill**: If you're working with shapes that can be filled (e.g., bars), you can use fill to segregate data.

```R
# Using fill to segregate data (for filled shapes like bars)
ggplot(df, aes(x = x, y = y, fill = category)) +
  geom_bar(stat = "identity") +
  labs(x = "X axis label", y = "Y axis label", title = "Bar plot of Y against X with segregation by category (fill)")
```

4. **Linetype**: If you're working with lines (e.g., line plots), you can vary the line type based on a category.

```R
# Using linetype to segregate data (for line plots)
ggplot(df, aes(x = x, y = y, linetype = category)) +
  geom_line() +
  labs(x = "X axis label", y = "Y axis label", title = "Line plot of Y against X with segregation by category (linetype)")
```

These are just a few examples. Depending on the type of plot and your specific data, you may find other aesthetics more suitable for segregating data.

## Aggregating to a Plot
To aggregate data before plotting it in a bar chart (or any other chart for that matter), you can use the `dplyr` package to perform the aggregation operations and then pipe the result into `ggplot2` for visualization. Here's how you can do it:

```R
library(dplyr)
library(ggplot2)

# Example data frame
df <- data.frame(
  category = c("A", "B", "C", "A", "B", "C"),
  count = c(10, 20, 15, 8, 12, 18)
)

# Aggregate the data by category
df_aggregated <- df %>%
  group_by(category) %>%
  summarise(total_count = sum(count))

# Plotting the aggregated data as a bar chart
ggplot(df_aggregated, aes(x = category, y = total_count)) +
  geom_bar(stat = "identity") +
  labs(x = "Category", y = "Total Count", title = "Aggregated Bar Chart")
```

In this code:

- We first use `group_by(category)` to group the data by the `category` column.
- Then, `summarise(total_count = sum(count))` calculates the sum of `count` within each group and creates a new column named `total_count`.
- After aggregation, the data is piped into `ggplot()` for creating the bar chart.
- `geom_bar(stat = "identity")` is used to create the bar chart with the aggregated data.
- `labs(x = "Category", y = "Total Count", title = "Aggregated Bar Chart")` sets the labels for the x-axis, y-axis, and the title of the plot.
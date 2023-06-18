# Data Analysis

It should be noted that the data was cleaned before analysis. Only the relevant coulmns were kept to keep the analysis and coding simple and to the point. 

Let us start!

#### - Summary of all the relevant parameters that form the basis of coffee judgment.

This will help us understand our coffee judgement parameters in a more refined manner. 

````R
summary(coffee)
````

**Results:**

<img width="783" alt="summary_of_data" src="https://github.com/arshiyamahajan/R_programming/assets/136816563/bafaf90a-9344-4500-a6de-68a5bb37a36b">

#### - Comparing countries on the basis of Processing Method along with graph.

This will help us gain insight into the most-used processing method for different countries and whether these processing methods vary for different places. 

````R
result <- coffee %>%
  group_by(Country_of_Origin, Processing_Method) %>%
  summarize(count = n()) %>%
  ungroup()

result <- result %>%
  group_by(Country_of_Origin) %>%
  slice_max(count)

print(result)

````

**Results:**

<img width="423" alt="processing_method_count" src="https://github.com/arshiyamahajan/R_programming/assets/136816563/2e147537-8904-4d4f-b962-a6222c4ce0fb">

````R
ggplot(result, aes(x = Country_of_Origin, y = count, fill = Processing_Method)) +
+   geom_bar(stat = "identity", position = "dodge") +
+   labs(x = "Country of Origin", y = "Count", fill = "Processing Method") +
+   theme_minimal()
````

**Results:**

<img width="1339" alt="processing_methods" src="https://github.com/arshiyamahajan/R_programming/assets/136816563/2291758e-6d7b-440c-9310-e26781559517">

It can be clearly observed that "Washed/Wet" is the most used processing method for majority of the countries.

#### - Now, let's find out the variety produced by each country.

This aims to provide the different varieties of coffee produced by each country. 
````R
country_varieties <- coffee %>%
  group_by(Country_of_Origin) %>%
  summarise(varieties = paste(unique(Variety), collapse = ", "))

print(country_varieties)

````

**Results:**
<img width="1317" alt="country_varities" src="https://github.com/arshiyamahajan/R_programming/assets/136816563/adfa30dc-5538-436d-8abc-f5fdabe9bd31">

#### - It becomes relevant to note the most produced varieties.
````R
most_produced_varieties <- coffee %>%
  group_by(Variety) %>%
  summarise(count = n()) %>%
  arrange(desc(count)) %>%
  slice(1:5)
print(most_produced_varieties)

````

**Results:**
<img width="176" alt="most_produced_varities" src="https://github.com/arshiyamahajan/R_programming/assets/136816563/63d33351-f83f-4f8b-99c0-d650434826cf">

"Caturra", "Gesha", "Typica" are the most produced varieties.

#### - Is there any significant correlatiom between Aroma, Flavour and Aftertaste?

````R
correlation_matrix <- cor(coffee[, c("Aroma", "Flavor", "Aftertaste")])

print(correlation_matrix)

pairs(coffee[c("Aroma", "Flavor", "Aftertaste")])

````

**Results:**

|        | Aroma      | Flavor     | Aftertaste |
|--------|------------|------------|------------|
| Aroma  | 1.0000000  | 0.8227785  | 0.7933975  |
| Flavor | 0.8227785  | 1.0000000  | 0.8768114  |
| Aftertaste | 0.7933975 | 0.8768114 | 1.0000000 |


<img width="960" alt="aroma,flavor,aftertase_correlation" src="https://github.com/arshiyamahajan/R_programming/assets/136816563/26530d05-efe7-4a69-a522-48fe79baa398">

The matrix shows the correlation coefficients between each pair of variables. The values range from -1 to 1, where 1 represents a perfect positive correlation, -1 represents a perfect negative correlation, and 0 represents no correlation.

Based on the correlation coefficients, we can observe the following relationships:

1. Aroma and Flavor: The correlation coefficient between aroma and flavor is 0.8227785, indicating a strong positive correlation. This suggests that there is a significant relationship between aroma and flavor, and they tend to vary together.

2. Aroma and Aftertaste: The correlation coefficient between aroma and aftertaste is 0.7933975, indicating a strong positive correlation. This implies that there is a notable relationship between aroma and aftertaste, and they tend to vary together.

3. Flavor and Aftertaste: The correlation coefficient between flavor and aftertaste is 0.8768114, indicating a strong positive correlation. This suggests that there is a substantial relationship between flavor and aftertaste, and they tend to vary together.

Overall, based on the correlation coefficients, we can conclude that aroma, flavor, and aftertaste are positively correlated with each other, meaning that an increase or decrease in one variable is generally associated with a corresponding increase or decrease in the others.

#### - Now, let us move on to see which variety has category two defects greater than 0. 

Let us recall that "Category Two defects" are secondary defects that are more subtle and can only be detected through tasting. These defects include Over-fermentation, staleness, rancidness, chemical taste, etc.

To undersatnd it better, we shall also look at its bar chart.


````R
library(ggplot2)

defects_greater_than_zero <- subset(coffee, coffee$`Category_Two_Defects` > 0)
variety_counts <- table(defects_greater_than_zero$Variety)

bar_plot <- ggplot(data = data.frame(variety = names(variety_counts), count = as.numeric(variety_counts)),
                   aes(x = reorder(variety, -count), y = count)) +
  geom_bar(stat = "identity", fill = "blue") +
  labs(x = "Variety", y = "Count", title = "Variety with Category Two Defects > 0") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))

print(bar_plot)

````

**Results:**

<img width="1007" alt="category_two_defects" src="https://github.com/arshiyamahajan/R_programming/assets/136816563/29e448c7-a7fd-4405-8d16-7c9f96020f2c">

"Caturra" has the most category two defects followed by "Catuai" and "Bourbon".

#### - Let us now look at the distribution of Coffee Colours throughout the database.

````R
library(ggplot2)

color_counts <- table(coffee$Color)

pie_chart <- ggplot(data = data.frame(color = names(color_counts), count = as.numeric(color_counts)),
                    aes(x = "", y = count, fill = color)) +
  geom_bar(stat = "identity", width = 1, color = "white") +
  coord_polar("y", start = 0) +
  labs(fill = "Color", title = "Distribution of Coffee Colors") +
  theme_minimal() +
  theme(legend.position = "right")

print(pie_chart)

````

**Results:**
<img width="783" alt="distribution_coffee_colours" src="https://github.com/arshiyamahajan/R_programming/assets/136816563/f0ff87f2-41fa-4435-af67-b96b71aa6ac4">

#### - Which countries have the highest average total cup points?

This question helps identify the countries with the highest overall coffee quality based on the "Total Cup Points" metric.


````R
library(dplyr)

country_avg_points <- coffee %>%
  group_by(`Country_of_Origin`) %>%
  summarise(Avg_Total_Cup_Points = mean(`Total_Cup_Points`)) %>%
  arrange(desc(Avg_Total_Cup_Points))

print(country_avg_points)

````

**Results:**

<img width="370" alt="avg_total_cup_points" src="https://github.com/arshiyamahajan/R_programming/assets/136816563/af0c33e5-6b0c-46ad-88ed-9b1f019da2fe">

"Ethiopia", "Tanzania", "Taiwan" are the top three countries when it comes to highest average total cup points.

#### - We shall now determine the relationship between altitude and coffee quality.

This question explores whether there is a correlation between the altitude at which coffee is grown and its quality, as measured by "Total Cup Points."

````R
library(ggplot2)

scatter_plot <- ggplot(coffee, aes(x = Altitude, y = `Total_Cup_Points`)) +
  geom_point() +
  labs(x = "Altitude", y = "Total Cup Points", title = "Relationship between Altitude and Coffee Quality") +
  theme_minimal()

print(scatter_plot)

````

**Results:**

![altitude_coffeequality](https://github.com/arshiyamahajan/R_programming/assets/136816563/30becac8-00ff-4b25-a0e0-9e264112ea34)

It can be observed that the coffee quality more or less remains the same with respect to change in altitude.






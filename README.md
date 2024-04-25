# customer-behaviour-analysis
# Customer Behaviour Analysis using R
 Customer behaviour analysis is a method businesses employ to examine and understand how customers with their offerings, be it a product, service, or overall brand ecosystem. By gaining a deep understanding of customer behaviour, businesses can derive actionable insights to inform strategic decision-making across multiple facets of operations, including product development, marketing strategies, pricing optimization, and customer experience enhancements.

_Data used for the analysis: https://statso.io/customer-behaviour-case-study/_

## Data Preparation

We will start with installing and loading the packages in R Studio.

    install.packages("plotly")
    install.packages("ggplot2")
    install.packages("dplyr")
    library(ggplot2)
    library(plotly)
    library(dplyr)

Loading CSV files with our data:

    data <- read.csv("ecommerce_customer_data.csv")

Let’s see the first columns of the loaded file

    head(data)

    User_ID Gender Age  Location Device_Type Product_Browsing_Time Total_Pages_Viewed
    1       1 Female  23 Ahmedabad      Mobile                    60                 30
    2       2   Male  25   Kolkata      Tablet                    30                 38
    3       3   Male  32 Bangalore     Desktop                    37                 13
    4       4   Male  35     Delhi      Mobile                     7                 20
    5       5   Male  27 Bangalore      Tablet                    35                 20
    6       6   Male  31   Kolkata      Mobile                    34                 47
      Items_Added_to_Cart Total_Purchases
    1                   1               0
    2                   9               4
    3                   5               0
    4                  10               3
    5                   8               2
    6                   5               4


To explore the data further, we will use the summary() function

    data_summary <- summary(data)
    print(data_summary)

    User_ID         Gender               Age          Location        
     Min.   :  1.0   Length:500         Min.   :18.00   Length:500        
     1st Qu.:125.8   Class :character   1st Qu.:22.00   Class :character  
     Median :250.5   Mode  :character   Median :26.00   Mode  :character  
     Mean   :250.5                      Mean   :26.28                     
     3rd Qu.:375.2                      3rd Qu.:31.00                     
     Max.   :500.0                      Max.   :35.00                     
     Device_Type        Product_Browsing_Time Total_Pages_Viewed Items_Added_to_Cart
     Length:500         Min.   : 5.00         Min.   : 5.00      Min.   : 0.00      
     Class :character   1st Qu.:16.00         1st Qu.:16.00      1st Qu.: 2.00      
     Mode  :character   Median :31.00         Median :27.00      Median : 5.00      
                        Mean   :30.74         Mean   :27.18      Mean   : 5.15      
                        3rd Qu.:44.00         3rd Qu.:38.00      3rd Qu.: 8.00      
                        Max.   :60.00         Max.   :50.00      Max.   :10.00      
     Total_Purchases
     Min.   :0.000  
     1st Qu.:1.000  
     Median :2.000  
     Mean   :2.464  
     3rd Qu.:4.000  
     Max.   :5.000


Let's examine the age distribution within the dataset.

    ggplot(data, aes(x = Age)) +
      geom_histogram(binwidth = 1, fill = "blue", color = "black", alpha = 0.7) +
      labs(title = "Distribution of Age") +
      theme_minimal()

![image](https://github.com/anife-osmanova/customer-behaviour-analysis/assets/77293628/036b0d88-d36e-476b-9c1f-7a9dbcca801d)



Now, for the gender distribution, calculate gender counts

    gender_counts <- table(data$Gender)
    gender_df <- as.data.frame(gender_counts)
    names(gender_df) <- c("Gender", "Count")

Create a bar plot for gender distribution

    ggplot(gender_df, aes(x = Gender, y = Count, fill = Gender)) +
      geom_bar(stat = "identity", position = "dodge") +
      labs(title = "Gender Distribution") +
      theme_minimal()

![image](https://github.com/anife-osmanova/customer-behaviour-analysis/assets/77293628/5e877be9-2024-48c2-81c3-cda48cd66a44)


## Data Exploration and Visualization

Now, we’ll explore the connection between the duration of a product browsing and the total number of pages viewed:

    ggplot(data, aes(x = Product_Browsing_Time, y = Total_Pages_Viewed)) +
      geom_point() +
      geom_smooth(method = "lm", se = FALSE, color = "blue") +
      labs(title = "Product Browsing Time vs. Total Pages Viewed") +
      theme_minimal()

![image](https://github.com/anife-osmanova/customer-behaviour-analysis/assets/77293628/c3b2be5c-a4d1-4741-afde-cb8a101a7d94)

This scatter plot shows no strong connection between the time spent on product browsing and the total number of pages viewed. Thus, customers may not necessarily explore more pages even with increased browsing time, potentially influenced by factors like website design, content relevance, or individual user preferences.




Next, let's examine the average number of total pages viewed based on gender and create a bar plot:

Calculate the average total pages viewed by gender

    gender_grouped <- aggregate(Total_Pages_Viewed ~ Gender, data, mean)

    ggplot(gender_grouped, aes(x = Gender, y = Total_Pages_Viewed, fill = Gender)) +
      geom_bar(stat = "identity") +
      labs(title = "Average Total Pages Viewed by Gender") +
      theme_minimal()

![image](https://github.com/anife-osmanova/customer-behaviour-analysis/assets/77293628/bd64131c-b221-4bfc-baed-b18527cf88d1)



The average total pages viewed by devices:

Calculate the average total pages viewed by device type

    devices_grouped <- aggregate(Total_Pages_Viewed ~ Device_Type, data, mean)

Create a bar plot for the average total pages viewed by device type

    ggplot(devices_grouped, aes(x = Device_Type, y = Total_Pages_Viewed, fill = Device_Type)) +
         geom_bar(stat = "identity") +
         labs(title = "Average Total Pages Viewed by Devices") +
         theme_minimal()

![image](https://github.com/anife-osmanova/customer-behaviour-analysis/assets/77293628/474b55ff-5038-453b-b31e-9acb79287f42)



Let's calculate the customer lifetime value and illustrate segments based on this metric.

Calculate CLV and create segments

    data <- data %>%
      mutate(
        CLV = (Total_Purchases * Total_Pages_Viewed) / Age,
        Segment = cut(CLV, breaks = c(1, 2.5, 5, Inf), labels = c("Low Value", "Medium Value", "High Value"))
      )

Calculate segment counts

    segment_counts <- count(data, Segment)

Create a bar chart to visualize customer segments

    ggplot(segment_counts, aes(x = Segment, y = n, fill = Segment)) +
      geom_bar(stat = "identity") +
      labs(title = "Customer Segmentation by CLV",
           x = "Segment",
           y = "Number of Customers") +
       theme_minimal()

![image](https://github.com/anife-osmanova/customer-behaviour-analysis/assets/77293628/324dfb25-55ed-4490-8fd5-ec79609b5c66)



Next is a funnel analysis:

Select relevant columns and perform groupby operation

    funnel_data <- data %>%
      select(Product_Browsing_Time, Items_Added_to_Cart, Total_Purchases) %>%
      group_by(Product_Browsing_Time, Items_Added_to_Cart) %>%
      summarise(Total_Purchases = sum(Total_Purchases)) %>%
      ungroup()


Create a funnel plot using ggplot2

    ggplot(funnel_data, aes(x = reorder(Product_Browsing_Time, -Total_Purchases), y = Total_Purchases, fill = Items_Added_to_Cart)) +
      geom_col() +
      labs(title = "Conversion Funnel",
           x = "Product Browsing Time",
           y = "Total Purchases") +
      theme_minimal()

![image](https://github.com/anife-osmanova/customer-behaviour-analysis/assets/77293628/e36c5415-ac1f-40c8-b68d-aace12ef6056)



Calculate churn rate

    churn_rate <- data %>% 
      mutate(Churned = Total_Purchases == 0) %>%
      summarize(churn_rate = mean(Churned))

    print(churn_rate$churn_rate)

    churn_rate
    1      0.198


A churn rate of 0.198 suggests a substantial proportion of customers have discontinued their engagement. It underscores the significance of addressing this churn to sustain business growth and profitability.



## Acknowledgments

This project was inspired by the work of Aman Kharwal in their project "Customer Behaviour Analysis using Python". The mentioned project served as a valuable reference and source of inspiration for this project. We express our gratitude for their contribution to the open-source community.

Link to the [original project](https://thecleverprogrammer.com/2023/11/06/customer-behaviour-analysis-using-python/)

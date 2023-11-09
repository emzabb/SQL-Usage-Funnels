# SQL-Usage-Funnels

# Project Description: Warby Parker Usage Funnels Analysis

## Introduction

Warby Parker, a groundbreaking lifestyle brand to provide designer eyewear at a revolutionary price. The brand takes its name from two characters in an early Jack Kerouac journal, embodying values of creative thinking, smart design, and a commitment to doing good in the world. At the heart of Warby Parker's ethos is the belief that for every pair of eyeglasses and sunglasses sold, a pair should be distributed to someone in need.

In this project, I will delve into Warby Parker's marketing funnels, aiming to calculate conversion rates and gain insights into user behavior. The project involves the analysis of two primary funnels—Quiz Funnel and Home Try-On Funnel—each associated with specific tables in the provided dataset.

## Dataset Overview

### Quiz Funnel:

-   **survey Table:** question, user_id, response

### Home Try-On Funnel:

-   **quiz Table:** user_id, style, fit, shape, color
-   **home_try_on Table:** user_id, number_of_pairs, address
-   **purchase Table:** user_id, product_id, style, model_name, color_price

The data utilized in this project is fictional and has been collaboratively provided by Warby Parker's Data Science team.

## Project Objectives


-   **Data Exploration:** Conduct a thorough exploration of the dataset, ensuring a clear understanding of the variables, data types, and potential relationships.
    
-   **Conversion Rate Calculation:** Calculate conversion rates for each stage of the Quiz Funnel and Home Try-On Funnel, providing a quantitative assessment of user engagement.
    
-   **User Behavior Analysis:** Analyze user behavior, identifying key touchpoints and potential bottlenecks in the funnels.
    

This project offers a unique opportunity to contribute to Warby Parker's mission-driven approach by enhancing the efficiency and effectiveness of their marketing funnels. By applying data-driven insights, the project aims to support Warby Parker in achieving its transformative goals within the eyewear industry.

    -- 1
    
    -- Examine survey table in Quiz Funnel
    
    SELECT *
    
    FROM survey
    
    LIMIT  10;
![enter image description here](https://i.ibb.co/L546L1t/1.png)
    
      
    
    -- 2
    
    -- Analyze how many users move from Question 1 to Question 2, etc. in Quiz Funnel
    
    SELECT question,
    
    COUNT(DISTINCT user_id)  AS  'num_answered'
    
    FROM survey
    
    GROUP  BY  1;
![enter image description here](https://i.ibb.co/xJkQwKy/2.png)
    
      
    
    -- 3
    
    -- Examine the three tables in the Home Try-On Funnel
    
    SELECT *
    
    FROM quiz
    
    LIMIT  5;
    
      
    
    SELECT *
    
    FROM home_try_on
    
    LIMIT  5;
    
      
    
    SELECT *
    
    FROM purchase
    
    LIMIT  5;
![enter image description here](https://i.ibb.co/sWbTbMd/3.png)
    
      
    
    -- 4
    
    -- Combine the three tables starting with the top of the funnel (quiz) and ending with the bottom of the funnel (purchase)
    
      
    
    SELECT q.user_id,
    
    (h.user_id IS  NOT  NULL)  AS  "is_home_try_on",
    
    h.number_of_pairs,
    
    (p.user_id IS  NOT  NULL)  AS  "is_purchase"
    
    FROM quiz q
    
    LEFT  JOIN home_try_on h
    
    ON q.user_id = h.user_id
    
    LEFT  JOIN purchase p
    
    ON p.user_id = h.user_id
    
    LIMIT  10;
![enter image description here](https://i.ibb.co/cYPbpwy/4.png)
    
      
    
    -- 5
    
    -- Calculate the number of users in the first step of the funnel
    
    WITH funnels AS  (SELECT q.user_id,
    
    (h.user_id IS  NOT  NULL)  AS  "is_home_try_on",
    
    h.number_of_pairs,
    
    (p.user_id IS  NOT  NULL)  AS  "is_purchase"
    
    FROM quiz q
    
    LEFT  JOIN home_try_on h
    
    ON q.user_id = h.user_id
    
    LEFT  JOIN purchase p
    
    ON p.user_id = h.user_id)
    
    SELECT  COUNT(*)  AS  'number_browsers'
    
    FROM funnels;
![enter image description here](https://i.ibb.co/SP4hPmt/5.png)
    
      
    
    -- 6
    
    -- Calculate the number of users who opt for a home try-on
    
    WITH funnels AS  (SELECT q.user_id,
    
    (h.user_id IS  NOT  NULL)  AS  "is_home_try_on",
    
    h.number_of_pairs,
    
    (p.user_id IS  NOT  NULL)  AS  "is_purchase"
    
    FROM quiz q
    
    LEFT  JOIN home_try_on h
    
    ON q.user_id = h.user_id
    
    LEFT  JOIN purchase p
    
    ON p.user_id = h.user_id)
    
    SELECT  COUNT(*)  AS  "number_browsers",
    
    SUM(is_home_try_on)  AS  "num_home_try_on"
    
    FROM funnels;
![enter image description here](https://i.ibb.co/FBM1gmN/6.png)
    
      
    
    -- 7
    
    -- Claculate the number of users who try 3 pairs
    
    WITH funnels AS  (
    
    SELECT q.user_id,
    
    (h.user_id IS  NOT  NULL)  AS  "is_home_try_on",
    
    h.number_of_pairs,
    
    (p.user_id IS  NOT  NULL)  AS  "is_purchase"
    
    FROM quiz q
    
    LEFT  JOIN home_try_on h
    
    ON q.user_id = h.user_id
    
    LEFT  JOIN purchase p
    
    ON p.user_id = h.user_id
    
    )
    
    SELECT  COUNT(*)  AS  "number_browsers",
    
    SUM(is_home_try_on)  AS  "num_home_try_on",
    
    COUNT(CASE  WHEN number_of_pairs = '3 pairs'  THEN  1  ELSE  NULL  END)  AS  "num_try_3_pairs"
    
    FROM funnels;
![enter image description here](https://i.ibb.co/LxHccL3/7.png)
    
      
    
    -- 8
    
    -- Calculate the number of users who try 5 pairs VS 3 pairs
    
    WITH funnels AS  (
    
    SELECT q.user_id,
    
    (h.user_id IS  NOT  NULL)  AS  "is_home_try_on",
    
    h.number_of_pairs,
    
    (p.user_id IS  NOT  NULL)  AS  "is_purchase"
    
    FROM quiz q
    
    LEFT  JOIN home_try_on h
    
    ON q.user_id = h.user_id
    
    LEFT  JOIN purchase p
    
    ON p.user_id = h.user_id
    
    )
    
    SELECT  COUNT(*)  AS  "number_browsers",
    
    SUM(is_home_try_on)  AS  "num_home_try_on",
    
    COUNT(CASE  WHEN number_of_pairs = '3 pairs'  THEN  1  ELSE  NULL  END)  AS  "num_try_3_pairs",
    
    COUNT(CASE  WHEN number_of_pairs = '5 pairs'
    
    THEN  1  ELSE  NULL  END)  AS  "num_try_5_pairs"
    
    FROM funnels;
![enter image description here](https://i.ibb.co/vL1kZTh/8.png)
    
      
    
    -- 9
    
    -- Calculate the number of users who make a purchase
    
    WITH funnels AS  (
    
    SELECT q.user_id,
    
    (h.user_id IS  NOT  NULL)  AS  "is_home_try_on",
    
    h.number_of_pairs,
    
    (p.user_id IS  NOT  NULL)  AS  "is_purchase"
    
    FROM quiz q
    
    LEFT  JOIN home_try_on h
    
    ON q.user_id = h.user_id
    
    LEFT  JOIN purchase p
    
    ON p.user_id = h.user_id
    
    )
    
    SELECT  COUNT(*)  AS  "number_browsers",
    
    SUM(is_home_try_on)  AS  "num_home_try_on",
    
    COUNT(CASE  WHEN number_of_pairs = '3 pairs'  THEN  1  ELSE  NULL  END)  AS  "num_try_3_pairs",
    
    COUNT(CASE  WHEN number_of_pairs = '5 pairs'
    
    THEN  1  ELSE  NULL  END)  AS  "num_try_5_pairs",
    
    SUM(is_purchase)  AS  "num_purchase"
    
    FROM funnels;
![enter image description here](https://i.ibb.co/dgkPX9M/9.png)
    
      
    
    -- 10
    
    -- Calculate the number of users at each step in the funnel
    
    WITH funnels AS  (
    
    SELECT q.user_id,
    
    (h.user_id IS  NOT  NULL)  AS  "is_home_try_on",
    
    h.number_of_pairs,
    
    (p.user_id IS  NOT  NULL)  AS  "is_purchase"
    
    FROM quiz q
    
    LEFT  JOIN home_try_on h
    
    ON q.user_id = h.user_id
    
    LEFT  JOIN purchase p
    
    ON p.user_id = h.user_id
    
    )
    
    SELECT  COUNT(*)  AS  "number_browsers",
    
    1.0 * SUM(is_home_try_on) / COUNT(*)  AS  "browse_to_try_on",
    
    1.0 * SUM(is_purchase) / SUM(is_home_try_on)  AS  "try_on_to_purchase"
    
    FROM funnels;
![enter image description here](https://i.ibb.co/ssg2RS3/10.png)
    
      
    
    -- 11
    
    -- Calculate the percentage of users who make a purchase after trying 3 pairs vs after trying 5 pairs
    
    WITH funnels AS  (
    
    SELECT q.user_id,
    
    (h.user_id IS  NOT  NULL)  AS  "is_home_try_on",
    
    h.number_of_pairs,
    
    (p.user_id IS  NOT  NULL)  AS  "is_purchase"
    
    FROM quiz q
    
    LEFT  JOIN home_try_on h
    
    ON q.user_id = h.user_id
    
    LEFT  JOIN purchase p
    
    ON p.user_id = h.user_id
    
    )
    
    SELECT  COUNT(*)  AS  "number_browsers",
    
    ROUND(1.0 * SUM(CASE  WHEN number_of_pairs = '3 pairs'  THEN  1  ELSE  0  END) / SUM(is_purchase), 2)  AS  "try_3_pairs_to_purchase",
    
    ROUND(1.0 * SUM(CASE  WHEN
    
    number_of_pairs = '5 pairs'  THEN  1  ELSE  0  END) / SUM(is_purchase), 2)
    
    AS  "try_5_pairs_to_purchase"
    
    FROM funnels;
![enter image description here](https://i.ibb.co/8d72R1t/11.png)
    
      
      
    
    -- 12
    
    -- Find the most popular results of the style quiz
    
    -- Most popular fit for women & men
    
    WITH common_fits AS  (
    
    SELECT style, fit, COUNT(*)  AS fit_count
    
    FROM quiz
    
    GROUP  BY style, fit
    
    )
    
    SELECT style,
    
    MAX(CASE  WHEN style = "Men's Styles"  THEN fit END)  AS most_common_fit_men,
    
    MAX(CASE  WHEN style = "Women's Styles"  THEN fit END)  AS most_common_fit_women
    
    FROM common_fits
    
    WHERE style IN  ("Men's Styles", "Women's Styles")
    
    GROUP  BY style;
![enter image description here](https://i.ibb.co/GWRfSXv/12-a.png)
    
      
    
    -- Most popular shape for men & women
    
    WITH common_shape AS  (
    
    SELECT style, shape, COUNT(*)  AS  'shape_count'
    
    FROM quiz
    
    GROUP  BY style, shape
    
    )
    
    SELECT style,
    
    MAX(CASE  WHEN style = "Men's Styles"  THEN shape END)  AS most_common_shape_men,
    
    MAX(CASE  WHEN style = "Women's Styles"  THEN shape END)  AS most_common_shape_women
    
    FROM common_shape
    
    WHERE style IN  ("Men's Styles", "Women's Styles")
    
    GROUP  BY style;
![enter image description here](https://i.ibb.co/yyxH3Y6/12-b.png)
    
      
    
    -- Most popular color for men & women
    
    WITH common_color AS  (
    
    SELECT style, color, COUNT(*)  AS  'color_count'
    
    FROM quiz
    
    GROUP  BY style, color
    
    )
    
    SELECT style,
    
    MAX(CASE  WHEN style = "Men's Styles"  THEN color END)  AS  'most_common_color_men',
    
    MAX(CASE  WHEN style = "Women's Styles"  THEN color END)  AS  'most_common_color_women'
    
    FROM common_color
    
    WHERE style IN  ("Men's Styles", "Women's Styles")
    
    GROUP  BY style;
![enter image description here](https://i.ibb.co/JjTH4mD/12-c.png)
    
      
    
    -- 13
    
    -- Calculate sales based on style, model, and color
    
    -- Men's style sales vs Women's style sales
    
    SELECT style, SUM(price)  AS  'total_sales'
    
    FROM purchase
    
    GROUP  BY  1
    
    ORDER  BY  2  DESC;
![enter image description here](https://i.ibb.co/sH40pTm/13-style.png)
    
      
    
    -- Sales by model
    
    SELECT model_name, SUM(price)  AS  'total_sales'
    
    FROM purchase
    
    GROUP  BY  1
    
    ORDER  BY  2  DESC;
![enter image description here](https://i.ibb.co/yR0fm27/13-model.png)
    
      
    
    -- Sales by color
    
    SELECT color, SUM(price)  AS  'total_sales'
    
    FROM purchase
    
    GROUP  BY  1
    
    ORDER  BY  2  DESC;
![enter image description here](https://i.ibb.co/9hmcVmp/13-color.png)
    
      
    
    -- Total overall sales
    
    SELECT  SUM(price)  AS  'total_sales'
    
    FROM purchase;
![enter image description here](https://i.ibb.co/8bFhVdr/13-total-slaes.png)

# Scenario: Promotion Effectiveness at a Fashion Retail Chain
## B1. Problem Formulation
### (a) Formulating the Machine Learning Problem
------------------------------------------------------------------
**Target Variable:**
Number of items sold 

**Candidate Input Features:**
1) Store characteristics: store size, location type (urban/semi-urban/rural)
2) Demand indicators: monthly footfall, historical sales, conversion rate
3) Customer demographics: income levels, customer segments
4) External factors: competition density, seasonal trends (month/festivals)
5) Promotion type: Flat Discount, BOGO, Free Gift, Category Offer, Loyalty Bonus

**Type of ML Problem:**
This is a supervised learning regression problem.

**Justification:**
The business objective is to maximize the number of items sold. It is a quantitative optimization problem. 
So a regression model is appropriate because:
- It predicts a continuous outcome (sales volume) rather than a discrete label.
- It allows estimation of expected sales for each promotion type under given conditions.
- By generating predictions for all possible promotions, the retailer can select the promotion with the highest predicted sales, thereby directly achieving the objective of maximization.
- Unlike classification, which only identifies the best promotion, this approach shows how much each promotion differs in sales, making comparisons clearer and more meaningful.




### (b) Choosing the Right Target Variable for Business Alignment
------------------------------------------------------------------

**Why “Items Sold” is a Better Target than Revenue**
Using items sold (sales volume) is more reliable than total revenue in this scenario because:

- **Promotions directly affect price:**
  Many promotions (e.g., Flat Discount, BOGO) reduce the effective price per item. This can increase units sold but decrease revenue per unit. Revenue may      therefore understate the true impact of a successful promotion.
- **Separates demand from pricing effects:**
  Sales volume reflects customer demand and response to promotions, while revenue mixes demand with pricing decisions, making it harder to interpret.
- **Fair comparison across promotions:**
  Different promotions change pricing structures differently. Comparing them using revenue can be misleading, whereas items sold provides a consistent basis for evaluation.
- **Aligns with the objective:**
  The stated goal is to maximize the number of items sold, so using sales volume directly ensures the model is optimized for the correct outcome.

**Broader Principle for target variable selection**
The target variable should directly reflect the true business objective and be minimally distorted by external or confounding factors.




### (c) Alternative Modelling Strategy to Capture Store-Level Differences
------------------------------------------------------------------

**Alternative Modelling Strategy**
Instead of one global model, use a segmented or hierarchical modelling approach.
This approach allows the model to learn both:
- Common patterns across all stores, and
- Differences specific to store types or individual stores

**How to Implement**
*Step 1: Segment the stores*
Group stores based on:
- Location (urban / semi-urban / rural)
- Footfall levels
- Customer demographics
- Competition density

*Step 2: Model building*
- Option 1: Train separate models for each segment
- Option 2: Build a single model with additional variables:
  - Store ID or store type
  - Interaction terms (e.g., Promotion × Location)
- This helps the model learn:
  - “BOGO works better in urban stores”
  - “Discounts work better in rural stores”

**Why This is Needed**
- Stores in different locations behave differently
- The same promotion can give different results in different stores
- Customer preferences and spending power vary by location

**Problem with a Single Global Model**
- It assumes all stores behave the same
- It averages out important differences
- It can give less accurate and generic predictions

**Benefits of the Proposed Approach**
- Captures local variations in promotion response
- Gives more accurate predictions
- Helps choose better promotions for each store type
- Supports data-driven and context-specific decisions

## B2. Data and EDA Strategy
### (a) Data Integration, Grain Definition, and Aggregation Strategy
------------------------------------------------------------------
**1. How to Join the Tables**

Use the transactions table as the base because it contains the actual sales data
- Join store attributes using store_id
- Join promotion details using store_id + month (or promotion_id if available)
- Join calendar table using date

Ensure all tables are aligned on store and time (date/month)

**2. Grain of the Final Dataset**

One row represents: One store × one month × one promotion

This matches the business decision: which promotion to run per store per month

**3. Aggregations Before Modelling**

From transactions (aggregate to store-month level):
- Total items sold (target variable)
- Total revenue
- Number of transactions
- Average basket size

From calendar:
- Number of weekends in the month
- Number of festival/holiday days
- Month or seasonal indicators

From promotion details:
- Promotion type (categorical feature)

  
### (b) Exploratory Data Analysis and Its Impact on Modelling
------------------------------------------------------------------
**1. Target Distribution Plot (Sales Volume)**

What to check:
- Are sales values very uneven or skewed?
- Are there extreme values (outliers)?

Impact on Feature Engineering and Modelling:
- If data is skewed → use log transformation
- If outliers exist → clean or cap them
- Helps choose the right model

**2. Sales by Promotion Type**

What to check:
- Which promotion gives higher sales?
- Do promotions behave very differently?

Impact on Feature Engineering and Modelling:
- Confirms promotion is an important feature
- Add features like promotion × store type
- Helps model learn better patterns

**3. Time / Seasonality Analysis**

What to check:
- Are there spikes during festivals or weekends?
- Do some months have higher sales?

Impact on Feature Engineering and Modelling:
- Add month, festival, weekend features
- Helps model capture seasonal patterns

**4. Correlation Heatmap**

What to check:
- Which features affect sales the most?
- Are some features very similar to each other?

Impact on Feature Engineering and Modelling:
- Keep important features
- Remove duplicate/redundant features
- Improves model performance

### (c) Impact of Data Imbalance on Model Performance
------------------------------------------------------------------

**Problem: Data Imbalance**
- 80% of transactions have no promotion
- Only 20% have promotions

**How This Affects the Model**
- Model may become biased towards “no promotion”
- It may not learn the true effect of promotions properly
- Predictions for promotions may be less accurate
- Model may ignore smaller but important patterns

**Steps to Address the Imbalance**

Balance the data:
- Oversample promotion data
- Undersample no-promotion data

Use better evaluation metrics:
- Don’t rely only on accuracy
- Use metrics that check performance across all cases

Feature engineering:
- Create a clear promotion indicator feature
- Add interaction features (promotion × store type)

Model approach:
- Use models that can handle imbalance better (e.g., tree-based models)

Data strategy:
- Collecting more promotion data
- Ensuring all promotion types are represented
- Designing better experiments 

## B3. Model Evaluation and Deployment
### (a) Evaluation Metrics and Business Interpretation
------------------------------------------------------------------

**Train–Test Split Strategy**

Use a time-based split (not random)

Example:
- Train set: First 2–2.5 years
- Test set: Last 6–12 months

Keep the order of time:
- Past → Train
- Future → Test

Optionally use rolling/validation splits:
- Train on Year 1 → validate on Year 2
- Train on Years 1–2 → validate on Year 3


**Why Random Split is Inappropriate**
- It mixes past and future data
- Causes data leakage (model sees future patterns)
- Gives over-optimistic results
- Does not reflect real-world use (we predict future using past)

**Evaluation Metrics**

***MAE (Mean Absolute Error)***

Interpretation:
- Shows the average error in number of items sold

In this business:
- If MAE = 50 → on average, predictions are off by 50 items per store per month

Why useful:
- Easy for business teams to understand
- Directly relates to sales volume accuracy

***RMSE (Root Mean Squared Error)***

Interpretation:
- Gives more weight to large errors

In this business:
- High RMSE means the model is making big mistakes in some stores or months

Why useful:
- Helps detect cases where wrong promotion choice could lead to large loss in sales


### (b) Using Feature Importance to Explain Model Decisions
------------------------------------------------------------------

*Understanding the Situation*
- Same store (Store 12)
- Different months (December vs March)
- Model recommends different promotions

***1. Check Feature Importance***

Identify which features are most important in the model:
- Month / season
- Festival indicators
- Footfall
- Past sales patterns
- Promotion interactions

This tells us what factors influence predictions the most

***2. Compare Features Across Months***

Look at how key features differ:

December:
- High demand (festive season)
- Higher footfall
- Customers less price-sensitive

March:
- Normal or lower demand
- More price-sensitive customers

***3. Explain the Model’s Logic***

In December:
- Customers already willing to spend
- Loyalty Points Bonus works better to increase basket size

In March:
- Demand is lower
- Flat Discount helps attract more buyers


***How to Communicate This to the Marketing Team***

Start with the key message:

“The model is not inconsistent—it is adapting to changes in customer behaviour across months.”

Explain the reason step by step:

In December:
- There is high demand due to festive season
- Customers are already willing to buy

So, Loyalty Points Bonus works better to:
- Increase purchase quantity
- Encourage repeat buying

In March:
- Demand is relatively lower
- Customers are more price-sensitive

So, Flat Discount works better to:
- Attract more customers
- Increase conversion

Connect it to data (feature importance):
“The model is using important factors like month, seasonality, and demand patterns to make these decisions.”

Reinforce trust:
“So the difference in recommendations is intentional and based on data—not random. It helps us choose the most effective promotion for each situation.”


### (c) End-to-End Deployment Process
------------------------------------------------------------------

**1. Save the Trained Model**

- After training, save the model to a file
  - Example: .pkl or .joblib

- Save full pipeline (preprocessing + model together)
  - Ensures same transformations are applied during prediction

**2. Monthly Data Preparation**

- At the start of each month:
  - Collect latest data:
    - Store attributes
    - Recent sales trends
    - Calendar features (month, festivals, weekends)

- Apply same preprocessing steps:
  - Encoding (categorical variables)
  - Scaling (if used)

- Create input data at correct grain:
    - One row = store × month × promotion

**3. Generate Predictions**
- Load the saved model
  - For each store:
  - Create 5 rows (one per promotion)

- Predict expected sales

- Select promotion with highest predicted sales

Output: recommended promotion per store

**4. Deployment Setup**
- Automate the process using:
  - Scheduled job (monthly pipeline)

- Steps run automatically:
  - Data collection → preprocessing → prediction → output

**5. Monitoring Model Performance**

Track model over time:

- Prediction vs Actual sales
  - Compare predicted vs real sales

- Error metrics
  - MAE / RMSE over time

- Data drift
  - Check if input data patterns change
  - Example: footfall or customer behaviour shifts

- Performance drop
  - If errors increase significantly → issue detected

**6. Retraining Strategy**
- Retrain model when:
  - Performance degrades
  - Data patterns change
  - New trends emerge

- Use latest data to update model

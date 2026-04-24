# Scenario: Promotion Effectiveness at a Fashion Retail Chain
## B1. Problem Formulation
### (a) Formulating the Machine Learning Problem
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

**Why “Items Sold” is a Better Target than Revenue**
-----------------------------------------------------
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
--------------------------------------------------
The target variable should directly reflect the true business objective and be minimally distorted by external or confounding factors.

### (c) Alternative Modelling Strategy to Capture Store-Level Differences

**Alternative Modelling Strategy**
----------------------------------
Instead of one global model, use a segmented or hierarchical modelling approach.
This approach allows the model to learn both:
- Common patterns across all stores, and
- Differences specific to store types or individual stores

**How to Implement**
-------------------
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
----------------------
- Stores in different locations behave differently
- The same promotion can give different results in different stores
- Customer preferences and spending power vary by location

**Problem with a Single Global Model**
-------------------------------------
- It assumes all stores behave the same
- It averages out important differences
- It can give less accurate and generic predictions

**Benefits of the Proposed Approach**
------------------------------------
- Captures local variations in promotion response
- Gives more accurate predictions
- Helps choose better promotions for each store type
- Supports data-driven and context-specific decisions

## B2. Data and EDA Strategy


## B3. Model Evaluation and Deployment

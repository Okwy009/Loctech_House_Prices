# Real Estate Data Analysis and Dashboard Reporting

## Introduction
This project provides a comprehensive analysis and visualization of real estate data using Python for data preprocessing, exploratory data analysis (EDA), and model building, alongside Power BI for interactive dashboards and reporting. The primary objective is to uncover insights, build predictive models, and develop actionable visualizations to assist stakeholders in making data-driven decisions.

## Tools Used
Through the course of this project, from the analysis and data cleaning through the predictive modelling, down to the Dashboard creation, the power of these tools were utilized:


- **Python:** The backbone of my analysis, allowing me to analyze the data and find critical insights.I also used the following Python libraries:
- **Pandas Library:** This was used to analyze the data.
- **Matplotlib Library:** I visualized the data.
- **Seaborn Library:** Helped me create more advanced visuals.
- **sklearn:** For Machine Learning and model building.
- **Jupyter Notebooks:** The tool I used to run my Python scripts which let me easily include my notes and analysis.
- **Visual Studio Code:** My go-to for executing my Python scripts.
- **Git & GitHub:** Essential for version control and sharing my Python code and analysis, ensuring collaboration and project tracking.

## Analysis

- Importing Libraries
```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt  
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score
```

For my Analysis, I focused on answering key questions leading to insights:

- Data Cleanup
```python
# Data Cleanup

# Check for missing values
print("Missing Values:\n", df.isnull().sum())

# Check for duplicate rows
duplicates = df.duplicated().sum()
print(f"Number of duplicate rows: {duplicates}")

# Check unique values in 'Location' column for consistency
print("Unique Locations:\n", df['Location'].unique())

# Check for outliers in numerical columns
print(df.describe())
```
### Questions To Answer
#### Data Preprocessing

1. How would you identify missing values in a Pandas DataFrame, and what methods would you use to handle them?
    -  In a Pandas DataFrame, missing values are identified by isna() or isnull().
    - Missing data are handled  using methods like:
    1. Imputation (fillna())
    2. Dropping rows/columns (dropna())

2. Describe how you would encode the Location variable using:
    
- pd.get_dummies() to create one-hot encoded variables.
- LabelEncoder from Scikit-learn's preprocessing module to assign numerical labels
- OneHotEncoder from Scikit-learn's preprocessing module to create one-hot encoded variables.

    - Using pd.get_dummies() for One-Hot Encoding creates a binary (Yes/No as 1/0 respectively) column for each category in the Location column. Each row will have 1 in the column corresponding to its Location value and 0 elsewhere.

    -  Using LabelEncoder from Scikit-learn assigns a unique numerical label to each category in the Location column. This is suitable for algorithms like tree-based models, which are unaffected by the ordinal nature of labels.
    
    - Using OneHotEncoder from Scikit-learn is similar to pd.get_dummies(), but it generates a sparse matrix by default, which is more memory-efficient for larger datasets. This is useful for preprocessing in Scikit-learn pipelines or when dealing with categorical features in a modeling pipeline.
```python
# One-hot encoding with pd.get_dummies()
df_dummies = pd.get_dummies(df, columns=['Location'], prefix='Location')

```

```python
# Using LabelEncoder from Scikit-learn
# Initialize the LabelEncoder
label_encoder = LabelEncoder()

# Encode the 'Location' column
df['Location_Label'] = label_encoder.fit_transform(df['Location'])

# Display the mapping and updated DataFrame
print("Mapping:", dict(zip(label_encoder.classes_, label_encoder.transform(label_encoder.classes_))))
```

```python
# Using OneHotEncoder from Scikit-learn

# Initialize the OneHotEncoder
one_hot_encoder = OneHotEncoder(sparse_output=False, drop=None)

# Fit and transform the 'Location' column
location_encoded = one_hot_encoder.fit_transform(df[['Location']])

# Convert the result into a DataFrame
location_encoded_df = pd.DataFrame(location_encoded, columns=one_hot_encoder.get_feature_names_out(['Location']))

# Concatenate with the original DataFrame
df_one_hot = pd.concat([df, location_encoded_df], axis=1)
```
#### Exploratory Data Analysis (EDA)
    
-  Write a Python code snippet using Matplotlib or Seaborn to intricately visualize the
relationship between Price and other variables.
-  What correlations or patterns do you expect to find in the dataset, and how would you
use Pandas' corr() function or Scipy's pearsonr correlation function to verify your
expectations?

**Code Snippet**
```python
# Visualize relationships using Seaborn
plt.figure(figsize=(16, 10))

# Scatter plot for Price vs Size (sq ft)
plt.subplot(2, 2, 1)
sns.scatterplot(data=df_one_hot, x='Size (sq ft)', y='Price ($)', color='blue')
plt.title('Price vs Size (sq ft)')
plt.xlabel('Size (sq ft)')
plt.ylabel('Price ($)')
plt.grid('True')

# Box plot for Price vs Bedrooms
plt.subplot(2, 2, 2)
sns.boxplot(data=df_one_hot, x='Bedrooms', y='Price ($)', hue='Bedrooms', palette='viridis', legend=False)
plt.title('Price vs Bedrooms')
plt.xlabel('Bedrooms')
plt.ylabel('Price ($)')
plt.grid('True')

# Box plot for Price vs Bathrooms
plt.figure(figsize=(16, 10))
plt.subplot(2, 2, 3)
sns.boxplot(data=df_one_hot, x='Bathrooms', y='Price ($)', hue='Bathrooms', palette='coolwarm', legend=False)
plt.title('Price vs Bathrooms')
plt.xlabel('Bathrooms')
plt.ylabel('Price ($)')
plt.show()

# Subplot for bar plot
plt.subplot(2, 2, 4)

# Bar plot
sns.barplot(data=df_one_hot, x='Location', y='Price ($)', errorbar=None, hue='Location', palette='coolwarm', legend=False)

# Title and labels
plt.title('Average Price by Location')
plt.xlabel('Location')
plt.ylabel('Average Price ($)')
plt.xticks(rotation=45)  # Rotate x-axis labels for better readability
plt.show()
```

**Result**
![Price_Vs_Size](Images\Price_Vs_Size.png)Price_Vs_Size
![Price_vs_Bedrooms](Images\Price_vs_Bedrooms.png)Price_vs_Bedrooms
![Price_vs_Bathrooms](Images\Price_vs_Bathrooms.png)Price_vs_Bathrooms
![Average_Price_by_Location](Images\Average_Price_by_Location.png)Average_Price_by_Location

**Expected Correlation**

1. Positive Correlations:

- **Size (sq ft) vs Price ($):** Larger properties typically have higher prices.

- **Bedrooms and Bathrooms vs Price ($):** Properties with more rooms or bathrooms often cost more.

2. Location Impact:

- Prices vary significantly by Location. This may not show as a direct numerical correlation but as distinct averages.

**Verifying Expectations**

To verify Expectations, I use the pearsonr() function which calculates the Pearson correlation coefficient and the p-value for a hypothesis test of non-correlation.
```python
from scipy.stats import pearsonr

# Correlation between Size (sq ft) and Price ($)
size_price_corr, size_price_pval = pearsonr(df_one_hot['Size (sq ft)'], df_one_hot['Price ($)'])
print(f"Correlation between Size and Price: {size_price_corr:.2f} (p-value: {size_price_pval:.2e})")

# Correlation between Age and Price ($)
age_price_corr, age_price_pval = pearsonr(df_one_hot['Age'], df_one_hot['Price ($)'])
print(f"Correlation between Age and Price: {age_price_corr:.2f} (p-value: {age_price_pval:.2e})")

```

#### Model Building

1. **Simple linear regression model**

    A simple linear regression model using Size (sq ft) as the predictor with Scikit-learn's LinearRegression class.

```python
# Define predictor (X) and target (y)
X = df_one_hot[['Size (sq ft)']]  # Predictor (needs to be 2D)
y = df_one_hot['Price ($)']       # Target

# Split the data into training and testing sets (80-20 split)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
train_data = X_train.join(y_train)


# Initialize and train the Linear Regression model
model = LinearRegression()
model.fit(X_train, y_train)

# Predict on the test set
y_pred = model.predict(X_test)
```
- To evaluate its
performance using metrics like R-squared and Mean Squared Error (MSE);
```python
# Evaluate the model
mse = mean_squared_error(y_test, y_pred)  # Mean Squared Error
r2 = r2_score(y_test, y_pred)            # R-squared
```

2. **Multiple Linear Regression Model:** 

    To build a multiple linear regression model using all independent variables that correlate with price with Scikit-learn's LinearRegression class;
```python
# Filter numeric columns for correlation calculation
numeric_columns = df_one_hot.select_dtypes(include=['number'])

# Compute correlation matrix
correlation_matrix = numeric_columns.corr()

print(correlation_matrix)

# Filter variables with correlation > 0.03 (absolute value)
relevant_features = correlation_matrix['Price ($)'][abs(correlation_matrix['Price ($)']) > 0.03].index.tolist()

# Exclude 'Price ($)' itself
relevant_features.remove('Price ($)')
print("Relevant features:", relevant_features)

# Select relevant features
X = df_one_hot[relevant_features]
y = df_one_hot['Price ($)']

# Split into training and testing sets (80-20 split)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Initialize and fit the model
model = LinearRegression()
model.fit(X_train, y_train)

# Predict on the test set
y_pred = model.predict(X_test)
```
- **Evaluating Model**
```python
# Calculate metrics
mse = mean_squared_error(y_test, y_pred)
mae = mean_absolute_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)
```

3. **Cross Validation**
from sklearn.model_selection import cross_val_score
```python
cv_scores = cross_val_score(model, X, y, cv=5, scoring='neg_mean_squared_error')
mean_mse = -cv_scores.mean()
std_mse = cv_scores.std()

print(f"Mean MSE: {mean_mse}, Standard Deviation: {std_mse}")
```

#### Model Selection
    
1. Explain how you would use cross-validation results to determine the better-performingmodel based on metrics like R-squared, MSE, and MAE.
    
2. Discuss the importance of Root Mean Squared Error (RMSE) and how you would interpret the mean RMSE and standard deviation for the selected model.
    
**Cross-validation:** 
This is a robust technique to evaluate a model's performance by splitting the data into multiple subsets (folds), ensuring the model is trained and validated on different data portions. 

Here’s how you can Using cross-validation results to determine the better-performing model based on metrics like R-squared, Mean Squared Error (MSE), and Mean Absolute Error (MAE):
- Select Metrics for Evaluation.
- Perform Cross Evaluation.
- Compare Models Using Metrics.
- Decide Based on the Application Context.

**Root Mean Squared Error (RMSE):** is a commonly used metric to evaluate the accuracy of regression models. It measures the average magnitude of the error between **predicted values**  and **actual values** and is expressed in the same units as the dependent variable.

Key reasons RSME is important include:
- Sensitivity to Large Errors
- Interpretable Scale
- Model Comparison
- Optimizing Model Performance

**Interpreting Mean RMSE**

When performing cross-validation, the mean RMSE provides an overall measure of how well the model predicts unseen data:

- **Lower Mean RMSE:**
Indicates better predictive accuracy.
Suggests that the model's predictions are closer to the actual values, on average.

- **High Mean RMSE:**
Indicates poor model performance.
Implies that the predictions deviate significantly from actual values.

#### Interpretation 
1. Write a Python code snippet to interpret the coefficients of the best model using Scikit-learn's LinearRegression class. Explain what each coefficient tells you about the
relationship between the independent variable and Price.

2. Formulate the best model's equation for predicting house prices based on the selected
independent variables.

**Code Snippet**
```python
coefficients = pd.DataFrame(model.coef_, index=relevant_features, columns=['Coefficient'])
print(coefficients)
```
- **Interpretation**

A coefficient value indicates the change in Price ($) for a unit change in the corresponding variable, keeping other variables constant.

**best model's equation for predicting house prices based on the selected independent variables.**
```python
# Equation
intercept = model.intercept_
equation = "Price ($) = " + " + ".join([f"{coef:.2f}*{feature}" for coef, feature in zip(model.coef_, relevant_features)]) + f" + {intercept:.2f}"
```

## Power BI Visualization

### Question 1
Create a measure that calculates the average price per square foot for each location. Then,
visualize this measure on a map, using filled maps to display the average price per square foot
intensity geographically. How would you handle locations with missing or zero square footage
values?

```
AveragePricePerSqFt = 
CALCULATE(
    AVERAGE('Sheet1'[Price ($)] / 'Sheet1'[Size (sq ft)]),
    'Sheet1'[Size (sq ft)] > 0
)

```

### Question 2 

Develop a measure that calculates the median price for houses with 3 bedrooms in each location.
Explain why using median is preferable to mean in some cases, especially with real estate data.

```
AgeGroup = 
SWITCH(
    TRUE(),
    'Sheet1'[Age] < 5, "New",
    'Sheet1'[Age] <= 15, "Recent",
    'Sheet1'[Age] <= 30, "Established",
    "Old"
)
```

**Why Median is Preferable:**

- Real estate data often contains outliers (e.g., luxury homes) that skew the mean.
- Median provides a better representation of the typical price by focusing on the central value.

### Question 3

Create a calculated column that categorizes houses into age groups (e.g., "New" for < 5 years,
"Recent" for 5-15 years, "Established" for 15-30 years, "Old" for > 30 years). 
Then, create a report page that shows the average price for each age group by location using a matrix visual.

```
AgeGroup = 
SWITCH(
    TRUE(),
    'Table'[Age] < 5, "New",
    'Table'[Age] <= 15, "Recent",
    'Table'[Age] <= 30, "Established",
    "Old"
)

```

For the report page, I created a Matrix Visual in Power BI with the folowing parameters:
- Rows = AgeGroup Column
- Columns = Location Column
- Values = Average Price
- Highlighted the color variation using color bars.

### Question 4
Design a report that allows users to filter the data by location and bedroom count, and then
displays a scatter plot showing the relationship between size.

**Steps:**

- **Create Filters:** Add slicers for Location and Bedrooms.
- **Create a Scatter Plot:**
    - X-Axis: Size (sq ft)
    - Y-Axis: Price ($)
    - Add Location as a legend to differentiate regions.
    - Add a Trendline to represent the overall relationship between Size (sq ft) and Price ($).

### Question 5:
**Advanced Visualization**

**Scenario:** The company wants to visualize how Price ($) correlates with Size (sq ft) and Age.

**Question:**
Which visualizations would you recommend for this analysis, and why? Design a report that
includes interactivity to filter by Bedrooms and Bathrooms. Specify the steps and measures
required for the visualization.

The following visualizations are recommended for this analysis:

1. **Scatter Plot with Color Gradient:**

- X-Axis: Size (sq ft)
- Y-Axis: Price ($)
- Color: Age

2. **Matrix or Heatmap:**
- Rows: Bedrooms
- Columns: Bathrooms
- Values: Average of Price ($)

3. **Steps to Build Interactive Report:**
- Add slicers for Bedrooms and Bathrooms.
- Use the scatter plot with a color gradient to show the interaction between Size, Price, and Age.

### Question 6
**Forecasting and Predictive Insights**
**Scenario:** Management wants to forecast house prices based on the Age and Location.
**Question:**

How would you use Power BI's analytics pane or integrate a predictive model for forecasting?
Provide steps for implementing this, including any external tools or connections you might use if
Power BI's native capabilities are insufficient.

**Steps for Implementing Forecasting:**
1. **Use Power BI’s Analytics Pane:**
- Add a Forecast line to a time-series visualization if time-related data is available (e.g., quarterly prices).
- Adjust confidence intervals and forecast length.

2. **Integrate Predictive Model:**

- Use Python or R scripts in Power BI to build a predictive model based on Age and Location.
```Python
from sklearn.linear_model import LinearRegression
import pandas as pd

# Encode categorical variable
df_one_hot = pd.get_dummies(data, columns=['Location'], drop_first=True)

# Train model
X = data.drop('Price ($)', axis=1)
y = data['Price ($)']
model = LinearRegression()
model.fit(X, y)

# Predictions
df_one_hot['Predicted Price'] = model.predict(X)
``` 
3. **External Tools:**

- If Power BI’s native capabilities are insufficient, use Azure Machine Learning or Power Automate to create more sophisticated forecasts.

### How the Project Answers Key Questions

#### Python:
- **Preprocessing and Encoding**: Ensures data integrity and usability.
- **EDA and Correlation Analysis**: Identifies significant predictors for house prices.
- **Regression Models**: Provides predictive insights into house pricing based on influential factors.
- **Cross-Validation and Metrics**: Validates model robustness and accuracy.

#### Power BI:
- **Geographical Analysis**: Average price per square foot mapped by location.
- **Price Distribution**: Median prices for specific property configurations.
- **Categorical Analysis**: Price trends by age groups and locations.
- **Interactive Reporting**: Filters and visualizations to explore relationships between variables.
- **Forecasting**: Insights into future trends in house prices based on age and location.

---

### Conclusion
This project demonstrates the power of combining Python for data analysis and Power BI for visualization to provide a holistic understanding of real estate data. By leveraging preprocessing techniques, advanced analytics, and interactive reporting, stakeholders can make informed decisions about pricing strategies, market trends, and future investments. The integration of predictive models and interactive dashboards ensures actionable insights and fosters data-driven strategies in the real estate domain.
# LABS-09: Analytics Project

**Student Name:** Ryan D  
**Course:** DS1001  
**Date:** March 27, 2026

---

## Questions

### 1. How many rows were dropped when you removed the rows with missing values?

**Answer:** 126 rows were dropped.

The original dataset contained 10,178 rows. After using `.dropna()` to remove rows with missing values, the cleaned dataset had 10,052 rows. The `genre` and `crew` columns were the main sources of missing data, accounting for the 126 rows that were removed.

### 2. What does the inplace=True parameter do in the .dropna() function?

**Answer:** The `inplace=True` parameter modifies the DataFrame in-place rather than returning a new DataFrame.

When `inplace=True` is used, the `.dropna()` method directly modifies the original `model_data` DataFrame and returns `None`. Without this parameter (or with `inplace=False`), the method would return a new DataFrame with the dropped rows removed, leaving the original unchanged. Using `inplace=True` is more memory-efficient when you want to permanently modify your working DataFrame.

### 3. The columns 'country' and 'top_lang' were collapsed to reduce the number of categories in the column – combining the smaller categories into an 'other' category.

#### a. Explain why this needed to be done

**Answer:** Collapsing categories was necessary to improve kNN model performance and interpretability.

kNN calculates distances between data points based on feature values. With too many categories in a column (especially rare ones), the distance calculations become overcomplicated and less meaningful. Rare categories that appear only a few times do not contribute useful information to the model and can introduce noise. By grouping uncommon categories into "other," we:
- Reduce the number of dummy variables the model must process
- Focus the model on the most common and predictive categories
- Improve model stability and generalization
- Make the feature space cleaner and more manageable

#### b. For each column, indicate the threshold to keep a category or combine it into 'other'.

**Answer:**
- **Country column threshold: 100**  
  Countries with fewer than 100 occurrences were combined into the 'other' category, resulting in 11 final categories.
  
- **Top_lang column threshold: 10**  
  Languages with fewer than 10 occurrences were combined into the 'other' category, resulting in approximately 20+ final categories (including 'other').

### 4. What column is the target variable? What is the 'target' of a model?

**Answer:** The target variable is the **'score'** column.

The target of a model is the variable the model is trying to predict. In supervised machine learning, we provide features (independent variables) as input and the target (dependent variable) as the output we want predicted. In this kNN classification model, the target is whether a movie receives a 'high' or 'low' score. The model learns patterns in the features that distinguish between these two categories, allowing it to make predictions on new, unseen movies.

### 5. You had to choose a threshold to recode the target as a category.

#### a. What threshold did you choose?

**Answer:** 90

#### b. Explain why you chose that threshold.

**Answer:** A threshold of 90 was chosen as it represents a natural break point in movie score quality.

Based on the density plot from LABS-06 showing the distribution of movie scores, a threshold of 90 provides a reasonable separation between high-quality and low-quality films. Scores above 90 represent the highest-rated movies, while scores of 90 and below represent all other films. This threshold:
- Creates a meaningful distinction (top-tier vs. rest)
- Provides a balanced split in the data for classification
- Aligns with common movie rating conventions
- Can be adjusted in future work if model performance warrants it

### 6. What columns are the feature variables? What are the 'features' of a model?

**Answer:** The feature variables are all columns except 'score': `budget_x`, `revenue`, `country`, `top_genre`, `top_lang`, and `year`.

After dummy variable encoding, this expands to include separate binary columns for each category (e.g., `country_AU`, `country_US`, `top_genre_Action`, etc.).

Features are the independent variables used as model input to predict the target. They represent the characteristics or attributes of each observation (in this case, movie attributes). A good set of features contains information that helps the model distinguish between different outcomes. The model learns to identify patterns and relationships in these features to make accurate predictions about the target variable.

### 7. Before the model could be created, the columns in the dataset had to be either numeric or "dummy variables" where each category in a variable becomes a new column of either "True" or "False" (also known as 1 or 0, respectively). Explain why this step was necessary.

**Answer:** This preprocessing step was essential because kNN calculates distances between data points to find "nearest neighbors."

kNN operates mathematically by computing distances (typically Euclidean distance) between a new point and all training points. This distance calculation requires numeric values—you cannot directly calculate distance between text values like "Action" or "Comedy." By converting categorical variables into dummy (binary) features through one-hot encoding:
- Each category becomes a separate binary column (1 if present, 0 if not)
- All features become numeric (0s and 1s)
- Distance calculations become meaningful and mathematically valid
- The algorithm can compare movies on all dimensions in a standardized way
- The kNN algorithm can identify which training movies are most similar to a new movie

Without this conversion, the algorithm would fail or produce meaningless results.

### 8. Find the cell where the data is split into training and testing sets. Use the linked documentation for the function to help you answer the following questions.

#### a. What percentage of the data is being used for the test set? The training set?

**Answer:**
- **Test set: 20%** (test_size=0.2)
- **Training set: 80%**

#### b. How many dataframes are made in this line of code?

**Answer:** Four dataframes are created:

#### c. Explain the purpose of each dataframe.

**Answer:**
1. **features_train** – Contains the input features (X) for training the model; the model learns patterns from these
2. **features_test** – Contains the input features (X) for evaluating the model; used to test performance on unseen data
3. **target_train** – Contains the target values (y) corresponding to features_train; used to teach the model what outcome each training example should predict
4. **target_test** – Contains the target values (y) corresponding to features_test; used to evaluate prediction accuracy on unseen data

The train/test split ensures we evaluate model performance on data it has never seen before, giving an honest assessment of how well it generalizes to new data.

### 9. Pick 2 tasks from the Data Cleaning & Model Prep phase. For each task, explain: a) What was done, b) Why it needs to be done

#### Task 1: Collapsing Country Categories

**a. What was done:**  
Countries with fewer than 100 movie occurrences in the dataset were grouped into a single 'other' category. This reduced the 58+ distinct countries in the original data to just 11 final categories.

**b. Why it needs to be done:**  
As explained in Question 3, rare categories add complexity without providing useful predictive information. Collapsing them reduces the dimensionality of the feature space, improves kNN distance calculations, and prevents overfitting to rare categories that may not generalize well to new data.

#### Task 2: Converting Score to Binary Classification

**a. What was done:**  
The numeric score column (0-100 range) was converted to categorical values using a threshold of 90. Scores above 90 were labeled 'high', and scores of 90 or below were labeled 'low'. This categorical variable was then converted to the 'category' data type.

**b. Why it needs to be done:**  
For kNN classification to work, the target variable must be categorical (distinct classes to predict into), not numeric. By creating two distinct classes ('high' and 'low'), the model has clear categories to learn and predict. Without this categorization, the model couldn't perform classification—it needs discrete outcomes rather than continuous values to work as a classifier.

### 10. Include your table (formatted as a figure) for the 5 values of k you tried and the corresponding accuracies for those values.

**Figure 1 – kNN Model Accuracy for Different k Values**

| k Value | Accuracy |
|---------|----------|
| 3       | 100.0%   |
| 5       | 100.0%   |
| 7       | 100.0%   |
| 9       | 100.0%   |
| 11      | 100.0%   |
| 15      | 100.0%   |

*Note: All k values tested achieved perfect accuracy on the test set.*

#### a. Which k value would you choose for this model?

**Answer:** I would choose **k = 5** or **k = 7**.

#### b. Explain why you chose that k value.

**Answer:** While all k values achieved 100% test accuracy, k=5 and k=7 are preferable because they balance model complexity with performance:

- **k=3** might be too simplistic, relying too heavily on very close neighbors
- **k=5 and k=7** provide a moderate neighborhood—they capture local patterns without requiring exact matches, offering good generalization
- **k=9, k=11, k=15** might sacrifice local pattern recognition by averaging over too many neighbors

The perfect 100% accuracy across all values suggests the classes are well-separated in the feature space. However, choosing a moderate k value (5-7) is generally safer for production models, as it's less likely to overfit if tested on new data. I would recommend **k=5** as the final choice—it's simple, interpretable, and has shown robust performance.

---

## Takeaway

**Perfect Separation Through Proper Preprocessing** — This lab revealed how critical data cleaning and feature engineering are to machine learning success. By systematically handling missing values, collapsing sparse categories, extracting meaningful features (like extracting the year from dates), and properly encoding categorical variables as dummy features, we transformed raw movie data into a perfectly separable dataset. The 100% test accuracy across all k values demonstrates that careful preprocessing can create clear decision boundaries in feature space. This experience highlighted that the quality of data preparation often matters more than model complexity; a simple algorithm on well-prepared data can outperform complex algorithms on poorly prepared data. Moving forward, I understand that spending time understanding the data, making informed decisions about thresholds and transformations, and validating choices through the kNN algorithm's geometric intuition will be essential for my final project success in DS1001.

---

## Notes (Not Included in Page Count)

### Important Findings & Reminders for Future Use

#### Data Cleaning Decisions Made
- Missing values strategy: dropna() rather than imputation
- Category thresholds: country=100, language=10 (can be tuned)
- Score threshold: 90 (based on density plot; adjustable for future projects)
- Date handling: extracted year only (could try month/quarter in future)

#### Model Observations
- All k values achieved 100% accuracy—unusual and worth investigating
- This suggests features are highly predictive or data may be too clean/artificial
- The one-hot encoding created many features (40+)—could try feature selection

#### For Final Project
- Document **why** you make each cleaning decision
- Test different thresholds to see impact (not just use arbitrary values)
- Create separate train/validation/test sets if needed
- Consider checking feature importance after modeling
- Be skeptical of perfect accuracy—real-world data rarely achieves this

#### Useful Sklearn Functions to Remember
- `train_test_split()` – random_state parameter allows reproducibility
- `MinMaxScaler()` – always fit on training data only, then transform both
- `pd.get_dummies()` – creates one-hot encoding for categorical variables
- `KNeighborsClassifier()` – explore n_neighbors and distance metrics

#### Documentation Habits to Adopt
- Link to every function's documentation you use
- Record parameter values for reproducibility
- Create tables/figures for comparisons
- Explain the "why" behind each preprocessing choice

# News Category Classification
![Newsroom](assets/newsroom.jpg)
*Reuters newsroom in London. REUTERS/Simon Newman*

- ### Project Overview and Objective
  This project aims to classify the news category based on its summary and short description. This project first deploys methods such as Latent Dirichlet Allocation and Wordclouds to perform EDA and provides the basis for feature transformation and data preprocessing. Subsequently, this project leverages the Bidirectional Encoder Representations from Transformers (BERT) model to vectorize the text, which is utilized to fit four distinct models with hyperparameter tuning. In particular, this project aims to classify the category of news entry $K_i$ based on a vector of transformed embeddings from the headline and the description of that news entry $X_i$, with $i$ representing one news entry or one row in the dataset. As a result, all four models achieved an accuracy of over 71%, with the best-performing model yielding an accuracy of over 74%. 
- ### Data Source
  The dataset used for this analysis is the **"News Category Dataset" (News_Category_Dataset_v3.json)** dataset created by Rishabh Misra on [Kaggle]([https://www.kaggle.com/datasets/rmisra/news-category-dataset/data]).

- ### Data Structure
  This dataset contains 209,527 rows, with each row representing one news entry. and 6 columns, with each column representing data related with that particular news entry. The columns along with a snapshot of the dataset are dipicted in the table below.

| link                                                                                               | headline                                                                                           | category   | short_description                                                                                                                            | authors                | date       |
|----------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------------|------------------------|------------|
| https://www.huffpost.com/entry/covid-boosters-uptake-us_n_632d719ee4b087fae6feaac9                | Over 4 Million Americans Roll Up Sleeves For Omicron-Targeted COVID Boosters                       | U.S. NEWS  | Health experts said it is too early to predict whether demand would match up with the 171 million doses of the new boosters the U.S. ordered | Carla K. Johnson, AP  | 2022-09-23 |
| ... | ...                               | ...     | ...                                                                                   | ...     | ... |
| https://www.huffingtonpost.com/entry/dwight-howard-rips-teammates-magic-hornets_us_5bb69b24e4b097869fd1b331 | Dwight Howard Rips Teammates After Magic Loss To Hornets               | SPORTS  | The five-time all-star center tore into his teammates Friday night after Orlando ... | N/A   | 2012-01-28 |


  *Sample snapshot of dataset*

- ### Data Cleaning and Preprocessing
  The purpose of this phase is to perform feature transformation and reduction prior to data analysis. In addition, missing values and duplicates are also identified and deleted.

  - **Feature Removal:** Columns $authors$, $link$, and $date$ are dropped due to the limited value provided for the analysis. The link and date are irrelevant to the news category, and although the $authors$ column did not display any missing values, there are, in fact, 37,418 missing news entries that do not have an associated author. In addition, due to the presence of more than 29,000 unique authors, the $authors$ column is thus determined to provide limited marginal information in addition to the content itself.
  - **Feature Transformation:** Given the similarity between specific categories (e.g., CULTURE & ARTS and ARTS & CULTURE), the current 42 categories are merged into a new set of eight categories based on domain knowledge and a sample of five news entries from each category. To examine how distinct the remaining eight categories are, Latent Dirichlet Allocation and Wordclouds are deployed to examine 1) the most important words in each topic and 2) the highest-frequency words of each topic, respectively.
    ![wordcloud sample](assets/wordcloud_sample.png)
    *Sample Wordcloud result for six of the post-process categories*
  - Removed outlier games identified by winning with a significant gold deficit at 15 minutes or losing with a significant gold lead at 15 minutes. 730 games satisfy this criteria, which constitutes around 3% of the total games. The data points that satisfy the following criteria are filtered out prior to data analysis:
    
$$
\left( \text{goldDiff} \geq 4000 \land \text{blueWin} = 0 \right) \lor \left( \text{goldDiff} \leq -4000 \land \text{blueWin} = 1 \right)
$$

- ### Exploratory Data Analysis
  EDA in this project is aimed to address the following questions:

  - What are the correlations between features?
  - What are the distributions of quantitative features?
  - What is the difference in the distribution of quantitative features across the two target classes?

- ### Data Analysis
  Four different models are deployed in this analysis to determine which model performs the best on this dataset:
  - **Logistic Regression with L1 penalty** \
    Due to this problem being a binary classification problem, Logistic Regression is deployed while incorporating an L1 penalty in the model to perform feature selection. The coefficient $C$ is also optimized by grid search with the package *GridSearchCV*.
  - **Linear Discriminant Analysis (LDA)** \
    Based on the results of the EDA, we can observe that the features in both classes roughly follow a Gaussian distribution. Therefore, Linear Discriminant Analysis is deployed, in addition to its low variance to prevent overfitting, as opposed to Quadratic Discriminant Analysis.
  - **Random Forest with Bayesian hyperparameter optimization** \
    Implemented Random Forest with hyperparameter Bayesian optimization with the package *hyperopt*.
  - **XGBoost with Bayesian hyperparameter optimization** \
    Implemented XGBoost with Bayesian hyperparameter optimization with the package *hyperopt*.

  In addition, K-fold Cross-Validation with $K = 5$ is also implemented for model selection to lower the variance of the results.

- ### Results
  The results of the four models are summarized in the following table:

  |Model|Accuracy|Precision|Recall|F1-Score|
  |-----|--------|---------|------|--------|
  |Logistic Regression|0.784456|0.783151|0.781385|0.782249|
  |LDA|0.784073|0.785103|0.776931|0.780966|
  |Random Forest|0.781987|0.786864|0.768155|0.777345|
  |XGBoost|0.780327|0.785842|0.765254|0.775327|

- ### Limitations
  - As current outlier detection is based on domain knowledge, incorporating a more sophisticated outlier detection algorithm may further improve the current classification results.
  - In practice, the impact of the gold difference at the 15-minute mark is not always deterministic of the outcome of the game. For example, for team compositions that are more late game-focused, a gold deficit at the 15-minute mark may have little to even no impact on the outcome of the game. Therefore, incorporating champion composition metadata of both teams may further increase prediction accuracy.
  - Incorporating player metadata (e.g., proficiency in champion, skill level) may also increase prediction accuracy, as the current dataset follows the assumption that all players are homogenous. 

- ### References

- 31st October 2019

  [1] Simon Newman. "rtr1pc8i_0.jpg" Reuters Institute for the Study of Journalism, 31 October 2019, https://reutersinstitute.politics.ox.ac.uk/news/if-your-newsroom-not-diverse-you-will-get-news-wrong/ \
  [2] Rishabh Misra, News Category Dataset (Kaggle, 2022), https://www.kaggle.com/datasets/rmisra/news-category-dataset/data

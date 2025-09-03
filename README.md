# predicting-corporate-ownership

## 1. Overview
A company can be identified as a "Domestic Ultimate" or a "Global Ultimate" using factors such as Company Age, certain keywords and a company’s industry:
- A Domestic Ultimate is an entity that is the highest-level company within a corporate structure based in its home country.
- A Global Ultimate is an entity that holds the status of being the ultimate or highest-level company within a corporate structure on a global scale.

## 2. Key Insights
<img width="1920" height="934" alt="image" src="https://github.com/user-attachments/assets/1cfff7aa-5020-4406-b9ac-4f77ca1f1b50" />

From the dataset provided, features were created to be able to predict whether a company is a Domestic Ultimate or Global Ultimate more accurately:
- From a company’s latitudes and longitudes provided, we extracted their **city (e.g. Bishan, Bukit Timah)**.
- Instead of using 4-Digit SIC codes or 8-Digit SIC codes, we decided to extract the company’s **2-Digit SIC code**.
- We found that certain keywords in a company’s description were actually associated with Domestic Ultimates or Global Ultimates. For example, Domestic Ultimates tend to mention their presence in **“Singapore” or “Asia”**. Meanwhile, Global Ultimates tend to mention them as **“holding securities”**.
- By eyeballing the data, we found that Global Ultimates tend to **pool their employee and sales data together**. This means that their domestic and global sales and employees were equal. This meant that we could find the difference between a company’s global sales/employees and domestic sales/employees and if it was 0, it was probably a Global Ultimate.
- We derive the **age of a company** from the year that they were founded.

## 3. Models
## 3.1 Decision Tree
  
We implemented a Decision Tree before and after SMOTE to see if there are any noticeable differences. These are the results:

<ins>Before SMOTE:</ins>

<img width="736" height="910" alt="image" src="https://github.com/user-attachments/assets/09db88d6-18bb-40a4-bcb0-d47b54210011" />

<ins>After SMOTE:</ins>

<img width="755" height="926" alt="image" src="https://github.com/user-attachments/assets/f42bb6f7-91c1-4236-9323-622a86b1ac7b" />

Comparing both results, it seems like SMOTE does not increase the Cross-Validation and training accuracy by much. Hence, we have decided not to use the resampled training set for the learning models in the below parts.

## 3.2 Random Forest
Initially, we used a singular model to predict whether a company was a Domestic Ultimate, Global Ultimate or Neither. However, we saw that this singular model often could not identify a Global Ultimate as seen from the F1-score of 0.49.

<img width="878" height="400" alt="image" src="https://github.com/user-attachments/assets/13f5aae8-a7d8-4aa3-b4ff-8ac5ddbbd98b" />

We realised that one thing we had not accounted for was that ALL Global Ultimates are also Domestic Ultimates. It could be wiser from this point onwards to perform a 2-pronged prediction model. To predict `Is Domestic Ultimate` first and then from the predicted numbers, we will predict `Is Global Ultimate`.

This model proved to be much more accurate in their predictions:
<ins>Is Domestic Ultimate (Accuracy: 72.41%)</ins>

<img width="1043" height="843" alt="image" src="https://github.com/user-attachments/assets/c9e4f675-0a44-4ae8-871d-fc46b21458d3" />

<ins>Is Global Ultimate (Accuracy: 97.92%)</ins>

<img width="1045" height="834" alt="image" src="https://github.com/user-attachments/assets/a82ae5a1-895a-4003-bff1-baa609ec2c01" />

We had also come up with another metric, a pooled accuracy. Since both models (Domestic Ultimate & Global Ultimate) have different numbers of test samples, we need to weight the accuracies based on the number of samples to get a pooled accuracy score. **In the end, this new 2-pronged Random Forest model achieved an 80.94% pooled accuracy.**

## 3.3 XGBoost
We found that the accuracy of the XGBoost method (71.65%) was similar to the 1-pronged method of the Random Tree (71.93%). Thus, we decided not to go ahead with the 2-pronged method of the XGBoost method:
- XGBoost natively supports multi-class classification, meaning it can learn direct distinctions between Domestic Ultimate, Global Ultimate, and Neither in a single model.
- The Random Forest one-pronged approach already showed comparable results to XGBoost. If XGBoost does not significantly outperform Random Forest in a single-step approach, it is unlikely that a more complex two-pronged XGBoost model will add value.

## 3.4 K-nearest Neighbors
The k-nearest neighbors algorithm shows the worst accuracy than the other classifiers at only 60.23%. Therefore, we will not be using this machine learning algorithm.

## 4. Findings
The model with the best performance in predicting whether the Is Domestic Ultimate and Is Global Ultimate classifications is the two-pronged Random Forest model. Using the pooled accuracy formula, we see that it has achieved an 80.94% accuracy for the test set:
- Predicting Domestic Ultimates first helps refine predictions for Global Ultimates.
- This suggests that Domestic Ultimates have clearer features (e.g. regional presence, specific keywords). Global Ultimates may overlap more with other categories, making them harder to classify.

The features in the first model that are useful to predict whether a company is a Domestic Ultimate and Global Ultimate are `employee_difference`, `Company Age`, `has_domestic_keyword_singapore` and `2-Digit SIC Code`:

<img width="2048" height="982" alt="image" src="https://github.com/user-attachments/assets/75c5108f-ecfe-4d47-86ee-ce3ada838f68" />

The features in the second model that are useful to predict whether a company is a Global Ultimate are `employee_difference`, `predicted_domestic`, `has_domestic_keyword_holding` and `Company Age`:

<img width="2048" height="1030" alt="image" src="https://github.com/user-attachments/assets/275fbca4-dff1-4b2c-bc4e-7dc8751c2033" />

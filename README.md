# IMU-Sales-prediction

# Sales Analysis of 'The Vault'

## Project Overview
This project focuses on analyzing sales data from **Indiana Memorial Union's (IMU) concept: 'The Vault'**, specifically from Grubhub orders placed by university students. The sales data spans **June to November**.

## Methodology
1. **Data Partitioning**:
   - **Training and Validation**: Sales data from **September** (`ProductMix_2024-09-01-2024-09-30.xlsx`) was used for training and validation.
   - **Testing**: Sales data from **October**(`ProductMix_2024-10-01-2024-10-31.xlsx`) was used as test data.

2. **Preprocessing and Modeling**:
   - The **`DM_project`** file includes:
     - Preprocessing of September's sales data.
     - Training multiple models and determining the best-performing one.
     - Using the best model to predict test data (`X_scaled.csv`).
   - The **`DM_project_test`** file includes:
     - Similar preprocessing steps applied to October's sales data as those used for September's data.
     - Splitting the preprocessed data into:
       - `X_test`: Scaled test features.
       - `Y_true`: True sales values (`y_total_merchant_sales.csv`).

3. **Model Evaluation**:
   - The results of the model were evaluated using the predictions on **October's test data**.
   - The evaluation focuses on comparing predicted results with true sales values.

## Files Description
- **`DM_project`**: Contains preprocessing, model training, and prediction on test data.
- **`DM_project_test`**: Handles preprocessing for October's data and prepares test inputs and true sales values.

## Conclusion
This analysis helps identify the best-performing model for predicting sales and provides insights into the sales trends at 'The Vault.'


# SuperStore EDA: Sales & Profitability Analysis

A comprehensive **Exploratory Data Analysis (EDA)** project on the SuperStore sales dataset, uncovering actionable business insights through rigorous data cleaning, feature engineering, and statistical analysis.

---

## Project Overview

This project performs an end-to-end analysis of 10,000+ sales records to identify patterns in customer behavior, product performance, and regional profitability. The analysis includes advanced data preprocessing, outlier detection, customer segmentation, and comprehensive visualizations to drive data-driven business decisions.

---

## Project Structure

```
SuperStoreEDA
├── README.md
├── REPORT.md
├── .gitignore 
├── SuperStoreEDA.ipynb
├── data/
│   ├── SuperStore_Dataset.csv
│   ├── CustomerData.csv
│   └── SaleStatsCustomerWise.csv
```

---

## Technologies & Libraries

- **Python 3.x**
- **Data Processing**: Pandas, NumPy
- **Visualization**: Matplotlib, Seaborn
- **Statistical Analysis**: Scipy, Scikit-learn
- **Environment**: Jupyter Notebook

---

## Key Features & Methodology

### Data Cleaning & Preprocessing
- **Duplicate Removal**: Identified and removed 21 duplicate records using comprehensive duplicate detection
- **Date Normalization**: Standardized Order Date and Ship Date formats across inconsistent date types
- **Missing Value Imputation**: 
  - Ship Mode: Intelligent imputation based on Days to Ship logic (98 rows affected)
  - Quantity: Median per Sub-Category & Segment approach (17 rows affected)
- **Data Masking**: Protected PII by masking customer names while preserving analytical value
- **Categorical Standardization**: Normalized state names and postal codes (266 rows affected)
- **Type Conversion**: Converted string numerics to appropriate data types for analysis

### Feature Engineering
- **Derived Business Metrics**:
  - `Original Price` = Sales Price / (1 - Discount)
  - `Total Sales` = Quantity × Sales Price
  - `Total Profit` = Quantity × Profit
  - `Discount Price` = Original Price × Discount
  - `Total Discount` = Quantity × Discount Price
- **Behavioral Features**:
  - `Shipping Urgency`: Categorized as Immediate/Urgent/Standard based on Days to Ship
  - `Days Since Last Order`: Customer retention analysis metric
- **Customer Aggregations**: Total sales, quantity, and discount statistics per customer

### Advanced Analytics
- **Outlier Detection**: Applied 3×IQR rule with reusable functions for flexibility
- **Customer Segmentation**: Quintile-based analysis creating sales vs. profit cross-grids
- **Correlation Analysis**: Statistical relationships between sales, profit, and discount variables
- **Temporal Analysis**: Monthly and yearly trend identification with seasonality detection

---

## Technical Implementation

### Data Quality Assurance
- **Comprehensive Validation**: Product ID consistency checks across category mappings
- **Statistical Integrity**: Median-based imputation to minimize outlier influence
- **Modular Design**: Reusable functions for outlier detection and data transformation

### Analytical Rigor
- **Kurtosis Analysis**: Identified heavy-tailed distributions indicating outlier presence
- **Cross-Validation**: Multiple validation approaches for data consistency
- **Robust Statistics**: 3×IQR methodology for outlier treatment vs. standard 1.5×IQR

---

## Future Enhancements

- **Predictive Modeling**: Time series forecasting for sales and demand planning
- **Machine Learning**: Customer lifetime value prediction and churn analysis
- **Advanced Segmentation**: Clustering algorithms for customer behavior analysis
- **Dashboard Development**: Interactive business intelligence dashboard using Streamlit or Dash
- **A/B Testing Framework**: Systematic discount strategy optimization

---

## How to Run

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/yourusername/super-store-eda.git
   cd super-store-eda
   ```

2. **Setup Environment**:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   pip install pandas numpy matplotlib seaborn scikit-learn jupyter notebook
   ```

3. **Launch Analysis**:
   ```bash
   jupyter notebook SuperStoreEDA.ipynb
   ```

---

## Author

**Sudarshan Bankar**  
Data Analyst | Business Intelligence Specialist ;)
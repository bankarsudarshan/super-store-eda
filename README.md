# 📑 SuperStore EDA: Summary of Findings

This report summarizes the key insights, transformations, and outcomes derived from an Exploratory Data Analysis (EDA) of the SuperStore sales dataset.

---

## Data Cleaning & Preprocessing

### 1. Initial Data Checks
- Loaded 10,000+ rows with 21 columns.
- Detected multiple data inconsistencies across date, category, and numeric fields.

### 2. Duplicate Handling
- Removed exact and not so exact (details in the notebook) duplicate rows.
- a code snippet from this section
```
print(f'Number of duplicate (entire) rows are - {len( df.loc[duplicate_rows_mask] )}')
print(f'Number of rows with duplicate row_id attribute are - {len( df.loc[duplicate_rowIDs_mask] )}')
```
output
```
Number of duplicate (entire) rows are - 17
Number of rows with duplicate row_id attribute are - 20
```
- dropped the `row_id` column. the index suffices its need
- Documented the number of rows affected and deleted.
- <mark>**In total**, there existed **21** duplicate records</mark>

### 3. Date Normalization
- Normalized `Order Date` and `Ship Date`.
- Normalizing of `Ship Date` was done in two parts because it consisted of dates of two types.
- Extracted year from `Order ID` and corrected mismatches with `Order Date`.
- Calculated `Days to Ship`.
- Checked if the category code and sub-category code in the `product_id` columns match the respective category and sub-category in the `category` and `sub-category` columns or not. for eg.  
    - Product ID = OFF-PA-10001970  
    - Category = Office Supplies
    - Sub-category = Paper
- no inconsistencies in `Product ID` column were found

### 4. Missing Value Imputation
- Handled some inconsitencies in `Ship Date` column
- **Ship Mode**: Imputed based on `Days to Ship`:
  - 0 days → "Same Day"
  - 7 days → "Standard Class"
  - **rows affected = 98**
- **Quantity**: Imputed using **median per Sub-Category & Segment**  
    - Reasoning:
    1. Grouping by both `segment` and `sub_category` will provide a more accurate estimate of quantity.  
  For example, a <ins>consumer</ins> segment might order <ins>smaller quantities</ins> of supplies compared to the <ins>corporate</ins> segment, even for the same sub-category.
    2. `median` is robust against outliers, ensuring that extremely high or low values do not skew the imputation.
    - **rows affected = 17**

### 5. Data Masking & Text Cleaning
- Masked PII by dropping `Customer Name` and creating `Customer Name Masked` with initials.
- Standardized `Postal Code` to 5-character strings.

### 6. Type Conversion
- Converted `Quantity`, `Sales Price`, and other numeric fields from string to `int`/`float`.

### 7. Handling Inconsistent Categorical Data
- Normalized state names (e.g., "CA" → "California", 'WA\\' → "Washington").
    - **rows affected = 266**

---

## 8. Feature Engineering

- Created new columns:
    - `Original Price` = Sales Price / (1 - Discount)
    - `Total Sales` = Quantity × Sales Price
    - `Total Profit` = Quantity × Profit
    - `Discount Price` = Original Price × Discount
    - `Total Discount` = Quantity × Discount Price
    - `Shipping Urgency`: Immediate / Urgent / Standard based on Days to Ship
    - `Days Since Last Order` per customer
- Aggregated customer-level stats (Total Sales, Quantity, Discount) and merged back to main dataset and saved this dataframe into a csv file

---

## 9. Outlier Detection
- code snippet from this section
```df.kurt(axis='index', numeric_only=True)```
output
```
sales_price               305.281770
quantity                    1.990846
discount                    2.409977
profit                    397.150385
days_to_ship               -0.287827
original_price            811.750344
total_sales               328.446832
total_profit              652.214371
discount_price           1716.669976
total_discount           1852.229788
days_since_last_order      -1.253626
dtype: float64
```
- `sales_price` has very high kurtosis. And since most of the other attributes are dependent on sales_price, the others have the same situation as well.
- **high kurtosis means heavy tails** which means **higher likelihood of outliers**
- Plotted box plots for the `sales_price` and `profit` attributes. This showed that there are way **too many outliers**.
- Used the 3\*IQR to flag outliers as the 1.5\*IQR method would flag most of the data as outliers. 
- Created reusable `count_outliers(s, whis=1.5)` & `remove_outliers(df, col, whis=1.5)` function that provide flexibility for the IQR rule.
- Applied to:
  - `Sales Price`: Removed extreme high-priced anomalies.
  - `Profit`: Removed extremely negative or positive profits.

---

## 10. Customer Segmentation and Analysis

- **Quintiles**:
  - Sales and profit quintiles calculated per `Customer ID`.
  - Created a cross-grid to analyze customers who generate high sales vs. high profits.
- Found that:
    - there are *83 customers* who contributed least to both - sales and profits for our shop (Q1-Q1)
    - and there are *88 customers* that contributed in both high sales and high profits for our shop (Q5-Q5)
    - there are *7 customers* that bought a lot from the shop but did not contribute that much in profits (Q5-Q1).

---

## 11. Key Visual Insights

### 11.1. Sales & Profitability
- Plotted the **Top 10 Most Profitable Products** & **Top 10 Loss-Making Products**
- **Sales vs. Profit**
```
              total_sales  total_profit
total_sales      1.000000      0.418484
total_profit     0.418484      1.000000
```
- The 'total sale of a product' is moderately correlated to the 'total profit from that product'
- **Jointplot** revealed clusters of profitable vs. loss-making transactions.

### 11.2 Customer Behavior
- Visualized the quintile cross tab created above using a heatmap
- Created a Pivot table to analyze the <ins>total sales</ins>, <ins>total profit</ins> and <ins># of orders</ins> by Category and Segment. Key Findings -
    - 'Consumer' segment brings in the most total sale as well as profits!
    - The <mark>'Consumer'x'Office Supplies'</mark> combination <mark>leads in all three criterias</mark> of total # orders, total amount of sale and total profits
    - The <mark>poorest performance in the three criterias</mark> of total # orders, total amount of sale and total profits is seen from the <mark>'Home Office'x'Furniture'</mark> combination

### 11.3. Shipping Analysis
- Majority of orders fell under “Standard” urgency (*67.6%*), followed by "Urgent" (*27.3%*) and lastly "Immediate" (*5.1%*).
- The 'Days to Ship vs. Profit' violin plot showed that there is no relation between the speed of shipping of orders and profit earned on those orders.
- The Profit Distribution across different Shipping Modes Bar Plot showed two key findings:
    - The 'Standard Class' shipping mode forms the highest total profits
    - But the 'Second Class' shipping mode brings in the highest profit per order

### 11.4 Regional Performance
- **South** and **West** regions had highest total sales.
- **California** was most profitable; **Illinois** and **Pennsylvania** were making the most losses.
- output of a code snippet from the notebook
```
Top 5 States by Profit: 

                profit
state                 
California  30447.1659
New York    16891.3635
Washington   7930.6299
Michigan     5103.1421
Virginia     4178.5846

=========================

Bottom 5 States by Profit: 

                 profit
state                  
Illinois      -186.8631
Pennsylvania  -128.5627
West Virginia   43.4336
North Dakota    68.0549
Ohio            89.0117
```
- Label encoding used to explore correlation between State and Profit. <mark>But this did not output results as expected</mark> (see details in notebook).

### 11.5 Discount Dynamics
- Clear negative correlation between **Discount** and **Profit**.
- High discounts (>=30%) generally led to losses.
- Original vs. Discounted Price plots showed inconsistent discounting practices across categories.

### 11.6 Temporal Trends
- Monthly and yearly sales/profit trends showed seasonality:
    - the months of January, February consistently over the years, record unusually low orders
    - the months of October, November and December consistently over the years, record unusually high orders
- **Year-over-year**: Sales increased, but profit margins did not increase hand in hand with the sales.

---

## Final Takeaways

- **Discount Strategy Needs Revision**: High discounts eat into profits significantly.
- **Copiers and Phones** are extremely profitable – consider marketing focus.
- **Furniture Sub-Categories** (especially Tables) need review for pricing and cost structure.
- **Shipping Optimization**: Same Day shipping sometimes leads to losses – optimize its usage.
- **Customer Retargeting**: Focus on Q5-Q5 customers and understand what makes them tick.
- **Data is clean, structured, and ready for modeling or dashboarding.**

---

## 🧠 Challenges Faced

- Handling inconsistent date formatting and mapping `Order ID` years.
- Filling missing values while preserving statistical integrity.
- Dealing with extreme values without deleting useful variance.
- Ensuring all transformations work **modularly** for any dataset with same schema.

---

## 📌 Project Preparedness

- Code is **modular and reusable**.
- Notebook runs end-to-end with no manual intervention.
- Dataset ready for modeling, reporting, or dashboarding.

---

## ✍️ Author

**Sudarshan Bankar**  
Built with ❤️, Python, and Pandas.


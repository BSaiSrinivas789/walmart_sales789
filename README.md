# Walmart Data Analysis: End-to-End SQL + Python Project P-9

## Project Overview
![image](https://github.com/user-attachments/assets/a11136cb-89e8-4ee6-be03-81f01ad50eaa)

This project is an end-to-end data analysis solution designed to extract critical business insights from Walmart sales data. We utilize Python for data processing and analysis, SQL for advanced querying, and structured problem-solving techniques to solve key business questions. The project is ideal for data analysts looking to develop skills in data manipulation, SQL querying, and data pipeline creation.

---

## Project Steps

### 1. Set Up the Environment
   - **Tools Used**: Visual Studio Code (VS Code), Python, SQL (MySQL)
   - **Goal**: Create a structured workspace within VS Code and organize project folders for smooth development and data handling.

### 2. Set Up Kaggle API
   - **API Setup**: Obtain your Kaggle API token from [Kaggle](https://www.kaggle.com/) by navigating to your profile settings and downloading the JSON file.
   - **Configure Kaggle**: 
      - Place the downloaded `kaggle.json` file in your local `.kaggle` folder.
      - Use the command `kaggle datasets download -d <dataset-path>` to pull datasets directly into your project.

### 3. Download Walmart Sales Data
   - **Data Source**: Use the Kaggle API to download the Walmart sales datasets from Kaggle.
   - **Dataset Link**: [Walmart Sales Dataset](https://www.kaggle.com/najir0123/walmart-10k-sales-datasets)
   - **Storage**: Save the data in the `data/` folder for easy reference and access.

### 4. Install Required Libraries and Load Data
   - **Libraries**: Install necessary Python libraries using:
     ```bash
     pip install pandas numpy sqlalchemy mysql-connector-python psycopg2
     ```
   - **Loading Data**: Read the data into a Pandas DataFrame for initial analysis and transformations.

### 5. Explore the Data
   - **Goal**: Conduct an initial data exploration to understand data distribution, check column names, types, and identify potential issues.
   - **Analysis**: Use functions like `.info()`, `.describe()`, and `.head()` to get a quick overview of the data structure and statistics.

### 6. Data Cleaning
   - **Remove Duplicates**: Identify and remove duplicate entries to avoid skewed results.
   - **Handle Missing Values**: Drop rows or columns with missing values if they are insignificant; fill values where essential.
   - **Fix Data Types**: Ensure all columns have consistent data types (e.g., dates as `datetime`, prices as `float`).
   - **Currency Formatting**: Use `.replace()` to handle and format currency values for analysis.
   - **Validation**: Check for any remaining inconsistencies and verify the cleaned data.

### 7. Feature Engineering
   - **Create New Columns**: Calculate the `Total Amount` for each transaction by multiplying `unit_price` by `quantity` and adding this as a new column.
   - **Enhance Dataset**: Adding this calculated field will streamline further SQL analysis and aggregation tasks.

### 8. Load Data into MySQL 
   - **Set Up Connections**: Connect to MySQL using `sqlalchemy` and load the cleaned data into each database.
   - **Table Creation**: Set up tables in MySQL using Python SQLAlchemy to automate table creation and data insertion.
   - **Verification**: Run initial SQL queries to confirm that the data has been loaded accurately.

9. SQL Analysis: Complex Queries and Business Problem Solving

- **Business Problem-Solving:** Write and execute complex SQL queries to answer critical business questions, such as:

    1. Highest-Rated Category in Each Branch:
    ```sql
    SELECT branch, category, avg_rating
    FROM (
        SELECT 
            branch,
            category,
            AVG(rating) AS avg_rating,
            RANK() OVER(PARTITION BY branch ORDER BY AVG(rating) DESC) AS rank
        FROM walmart
        GROUP BY branch, category
    ) AS ranked
    WHERE rank = 1;
    ```

    2. Busiest Day in Each Branch Based on Transactions:
    ```sql
    SELECT branch, day_name, no_transactions
    FROM (
        SELECT 
            branch,
            DAYNAME(STR_TO_DATE(date, '%d/%m/%Y')) AS day_name,
            COUNT(*) AS no_transactions,
            RANK() OVER(PARTITION BY branch ORDER BY COUNT(*) DESC) AS rank
        FROM walmart
        GROUP BY branch, day_name
    ) AS ranked
    WHERE rank = 1;
    ```

3 Total Quantity Sold Per Payment Method (Q4):
    ```sql
    SELECT 
        payment_method,
        SUM(quantity) AS no_qty_sold
    FROM walmart
    GROUP BY payment_method;
    ```

4. Average, Minimum, and Maximum Ratings for Categories by City (Q5):
    ```sql
    SELECT 
        city,
        category,
        MIN(rating) AS min_rating,
        MAX(rating) AS max_rating,
        AVG(rating) AS avg_rating
    FROM walmart
    GROUP BY city, category;
    ```

5. Total Profit for Each Category (Q6):
    ```sql
    SELECT 
        category,
        SUM(unit_price * quantity * profit_margin) AS total_profit
    FROM walmart
    GROUP BY category
    ORDER BY total_profit DESC;
    ```

6. Most Common Payment Method for Each Branch (Q7):
    ```sql
    WITH cte AS (
        SELECT 
            branch,
            payment_method,
            COUNT(*) AS total_trans,
            RANK() OVER(PARTITION BY branch ORDER BY COUNT(*) DESC) AS rank
        FROM walmart
        GROUP BY branch, payment_method
    )
    SELECT branch, payment_method AS preferred_payment_method
    FROM cte
    WHERE rank = 1;
    ```

7. Categorize Sales into Morning, Afternoon, and Evening Shifts (Q8):
    ```sql
    SELECT
        branch,
        CASE 
            WHEN HOUR(TIME(time)) < 12 THEN 'Morning'
            WHEN HOUR(TIME(time)) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift,
        COUNT(*) AS num_invoices
    FROM walmart
    GROUP BY branch, shift
    ORDER BY branch, num_invoices DESC;
    ```

   - **Documentation**: Keep clear notes of each query's objective, approach, and results.

### 10. Project Publishing and Documentation
   - **Documentation**: Maintain well-structured documentation of the entire process in Markdown or a Jupyter Notebook.
   - **Project Publishing**: Publish the completed project on GitHub or any other version control platform, including:
     - The `README.md` file (this document).
     - Jupyter Notebooks (if applicable).
     - SQL query scripts.
     - Data files (if possible) or steps to access them.

---

## Requirements

- **Python 3.8+**
- **SQL Databases**: MySQL
- **Python Libraries**:
  - `pandas`, `numpy`, `sqlalchemy`, `mysql-connector-python`
- **Kaggle API Key** (for data downloading)

## Getting Started

1. Clone the repository:
   ```bash
   git clone <repo-url>
   ```
2. Install Python libraries:
   ```bash
   pip install -r requirements.txt
   ```
3. Set up your Kaggle API, download the data, and follow the steps to load and analyze.

---

## Project Structure

```plaintext
|-- data/                     # Raw data and transformed data
|-- sql_queries/              # SQL scripts for analysis and queries
|-- notebooks/                # Jupyter notebooks for Python analysis
|-- README.md                 # Project documentation
|-- requirements.txt          # List of required Python libraries
|-- main.py                   # Main script for loading, cleaning, and processing data
```
---

## Results and Insights

This section will include your analysis findings:
**1. Sales Insights**
Key Categories:
The highest-rated and top-selling categories vary across branches. Categories like "Electronics" and "Clothing" consistently perform well in terms of both sales volume and customer ratings.
**Branches with Highest Sales:**
Branch A leads in overall sales, driven by strong performance in high-margin categories. Branch B shows significant sales in groceries and household items.
**Preferred Payment Methods:**
Across all branches, "Credit Card" is the most frequently used payment method, accounting for the majority of transactions, followed by "E-Wallet." Cash usage is relatively low.
**2. Profitability**
Most Profitable Categories:
Categories like "Electronics" and "Appliances" contribute the highest profits, owing to their higher margins and sales volume.
Despite high sales, categories such as "Groceries" yield lower profits due to thin profit margins.
**Top Locations:**
Profitability is highest in urban locations where high-margin categories are popular and customer spending capacity is greater.
**3. Customer Behavior**
**Ratings Trends:**
Customers give consistently high ratings for Electronics, Clothing, and Appliances. However, lower ratings are observed in categories such as "Groceries" in some cities, indicating potential issues with quality or service.
**Payment Preferences:**
While "Credit Card" is the dominant payment method, "E-Wallet" adoption is growing, particularly in Branch B, suggesting a shift toward digital payment methods.
Peak Shopping Hours:
Sales are highest during the Afternoon shift (12 PM - 5 PM), followed by the Evening shift (5 PM - closing). Morning sales are comparatively lower across all branches.
Actionable Recommendations
**Sales Strategies:**
Focus marketing campaigns on the highest-performing categories like "Electronics" and "Clothing."
Promote underperforming categories, such as Groceries, by addressing quality concerns and launching targeted discounts or offers.
**Operational Efficiency:**
Increase staffing and inventory during peak shopping hours (Afternoons and Evenings) to improve customer service and reduce wait times.
Reallocate resources during quieter periods for stock replenishment and preparation.
**Payment Method Incentives:**
Leverage the popularity of credit cards by introducing loyalty rewards or cashback offers.
Educate and promote the benefits of E-Wallets in branches with low adoption to further encourage digital payments.
**Profit Optimization:**
Focus on expanding high-margin categories like Electronics in branches with lower profitability.
Experiment with pricing and packaging strategies to increase margins in categories like Groceries.

## Future Enhancements

Possible extensions to this project:
- Integration with a dashboard tool (e.g., Power BI or Tableau) for interactive visualization.
- Additional data sources to enhance analysis depth.
- Automation of the data pipeline for real-time data ingestion and analysis.

---

## License

This project is licensed under the MIT License. 

---

## Acknowledgments

- **Data Source**: Kaggle’s Walmart Sales Dataset
- **Inspiration**: Walmart’s business case studies on sales and supply chain optimization.

---

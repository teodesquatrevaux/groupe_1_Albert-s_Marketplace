# Dimensional model

## Daily demand

1. **Business process:**  
   Measure product sales by day and by country.

2. **Grain:**  
   One row per product × country × day.

3. **Dimensions:**  
   - `dim_date`
   - `dim_product`
   - `dim_country`

4. **Facts:**  
   - `units_sold` — additive, can be summed across products, countries and days
   - `revenue_eur` — additive, can be summed across products, countries and days

```mermaid
erDiagram

    FCT_DAILY_DEMAND {
        int date_key FK
        int product_key FK
        int country_key FK
        int units_sold
        float revenue_eur
    }

    DIM_DATE {
        int date_key PK
        date full_date
        int iso_week
        int month
    }

    DIM_PRODUCT {
        int product_key PK
        string p_id
        string product_name
        string category
    }

    DIM_COUNTRY {
        int country_key PK
        string country_code
        string country_name
    }

    FCT_DAILY_DEMAND }o--|| DIM_DATE : "* to 1"
    FCT_DAILY_DEMAND }o--|| DIM_PRODUCT : "* to 1"
    FCT_DAILY_DEMAND }o--|| DIM_COUNTRY : "* to 1"
# Retail-store-analysis
End-to-end analysis of a global electronic retailer's store
# Global Electronics Retailer: Where to Invest, Fix, Watch or Cut

An end-to-end analysis of a global electronics retailer's stores, products and online channel, built in Excel and Power BI. The goal: tell the business which stores and products deserve more money, which need a diagnosis, and which to review.

## The business question

Which stores and products should the company **invest in, fix, watch or cut?**

## The answer

**Invest:** The online store grew 51.4% in 2019 and now makes 21.4% of sales, at the same margin as physical stores. Stores 66, 47 and 43 sell the most per square meter and keep growing. Computers and Cameras and camcorders are the strongest product categories.\
**Fix:** 15 stores grow well but sell little per square meter, and Cell phones and Games and Toys grow fast on thin margins. Find the cause (product mix, location) before spending.\
**Cut or review:** Store 20's sales fell 47.8% in 2019. Refrigerators fell 15.3% while the company grew 42.8%.
Read full the findings in [recommendations.md](https://github.com/kabirabdulsamad91/Retail-store-analysis/blob/main/recommendations.md)

## How I did it

**1.Understood the business problem and the data:** Define benchmarks and rules. Understood the tables and field to grasp why each exist, level of detail of each table (per order etc)\
**2.Data Validation:** Check completeness, duplicate, nulls and consistency\
**3.Prepared the data in Power Query and Power Pivot:** cleaned the tables, fix the inconsistence format of dates, standardize the data type for each field, transform the data and load it to power pivot to create relationships with tables.\
**4.Analysis:** Calculated the core retail KPIs including Total revenue, profit, units sold, profit margin etc. Trends( year-over-year sales growth), time intelligence. Store and Product performance with regards to the benchmarks for verdict. Regional segmentation\
**5.Interpretation:** Separate what the evidence I got from the data from what it does not\
**6.Visualization:** Rebuilt the model in Power BI and checked its numbers against my Excel results before designing four report pages.\
**7.Recommendation and Key Insight**

## Decisions I made

**1.Median, not average,** as the benchmark, because outliers pull the average up.\
**2.A Cut needs a real decline:** low on both measures and 2019 sales down 10% or more. Stores that are weak but not shrinking that fast are Watch.\
**3.Refrigerators are a Cut at the subcategory level,** after checking that the 2019 fall was not a company-wide pattern.

## Limits

- Profit is gross profit. The data has no rent, staff or other store costs.<br>
- Median splits put about a quarter of items in the weaker groups by design.<br>
- Store-by-product detail is too thin for growth rates.<br>
- The data cannot show online payment or checkout problems.

## Tools

- Excel (Power Query, Power Pivot, Pivot table, DAX, Formulas and functions)<br>
- Power BI (DAX, Data Modelling, Star schema, Time intelligence).

## Dataset

Maven Analytics, Global Electronics Retailer dataset, from the [Maven Analytics Data Playground](https://mavenanalytics.io/data-playground/global-electronics-retailer). The raw data is not copied here; download it from the source.

## About me

I'm Kabir Abdulsamad, a Computer science graduate in Nigeria building a career in data analytics. Connect with me on [LinkedIn](www.linkedin.com/in/kabir-abdulsamad-04b9bb3a5).

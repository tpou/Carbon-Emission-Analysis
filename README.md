# Carbon Emission Analysis
![Photo by Chris LeBoutillier (unsplash.com)]([https://github.com/RusAbk/Carbon-Emission-Analysis/blob/main/cover.jpg](https://github.com/tpou/Carbon-Emission-Analysis/blob/main/Database%20diagram.png)?raw=true)

Photo by Chris LeBoutillier (unsplash.com)

This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends. Carbon emissions play a crucial role in the environment, accounting for over 75% of global emissions and posing a significant environmental challenge. These emissions contribute to the accumulation of greenhouse gases in the atmosphere, leading to climate change, planetary warming, and involvement in various environmental disasters. Through this analysis, we hope to gain an understanding of the environmental impact of different industries and contribute to making informed decisions in sustainable development.

## Data Source: Where Our Data Comes From
Our dataset is compiled from publicly available data from [nature.com](https://nature.com) and encompasses the product carbon footprints (PCF) for various companies. PCFs represent the greenhouse gas emissions associated with specific products, quantified in CO2 (carbon dioxide equivalent).

## Data Structure
The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.

![Database diagram](https://github.com/RusAbk/Carbon-Emission-Analysis/blob/main/Database%20diagram.png?raw=true)

## Let's have a look at the data
 
There are 4 tables, let's take a look at all of them!

### Table `'product_emissions'`
```
SELECT * FROM product_emissions LIMIT 3;
```
| id           | company_id | country_id | industry_group_id | year | product_name                                                    | weight_kg | carbon_footprint_pcf | upstream_percent_total_pcf | operations_percent_total_pcf | downstream_percent_total_pcf | 
| -----------: | ---------: | ---------: | ----------------: | ---: | --------------------------------------------------------------: | --------: | -------------------: | -------------------------: | ---------------------------: | ---------------------------: | 
| 10056-1-2014 | 82         | 28         | 2                 | 2014 | Frosted Flakes(R) Cereal                                        | 0.7485    | 2                    | 57.50                      | 30.00                        | 12.50                        | 
| 10056-1-2015 | 82         | 28         | 15                | 2015 | "Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)" | 0.7485    | 2                    | 57.50                      | 30.00                        | 12.50                        | 
| 10222-1-2013 | 83         | 28         | 8                 | 2013 | Office Chair                                                    | 20.68     | 73                   | 80.63                      | 17.36                        | 2.01                         |         

### Table `'industry_groups'`
```
SELECT * FROM industry_groups LIMIT 3;
```
| id | industry_group                                                         | 
| -: | ---------------------------------------------------------------------: | 
| 1  | "Consumer Durables, Household and Personal Products"                   | 
| 2  | "Food, Beverage & Tobacco"                                             | 
| 3  | "Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber" | 
   
### Table `'companies'`
```
SELECT * FROM companies LIMIT 3;
```
| id | company_name               | 
| -: | -------------------------: | 
| 1  | "Autodesk, Inc."           | 
| 2  | "Casio Computer Co., Ltd." | 
| 3  | "Cisco Systems, Inc."      |   
      
### Table `'countries'`
```
SELECT * FROM countries LIMIT 3;
```
| id | country_name | 
| -: | -----------: | 
| 1  | Australia    | 
| 2  | Belgium      | 
| 3  | Brazil       |  
     
## Let's find out which products make the most contribution.
It would be interesting to see which products make the most significant contribution to the total amount of carbon emissions.

### Checking for product duplicates
Before doing this, let's try to find out if there are any duplicate products in our table. If there is, then we will group it by product.
```
SELECT 
	COUNT(product_name) as 'Total number of products', 
	COUNT(DISTINCT product_name) AS 'Number of unique products'
FROM 
	product_emissions;
```
| Total number of products | Number of unique products | 
| -----------------------: | ------------------------: | 
| 1037                     | 661                       |  

Yes, **we found a lot of duplicates**. So, it means this time is for grouping now :)

### Top 10 Products with the Highest Contribution to Carbon Emissions
```
SELECT 
	product_name,
	ROUND(AVG(carbon_footprint_pcf), 2) as 'Average PCF'
FROM 
	product_emissions
GROUP BY 
	product_name
ORDER BY ROUND(AVG(carbon_footprint_pcf), 2) DESC
LIMIT 10;
```

| product_name                                                                                                                       | Average PCF | 
| ---------------------------------------------------------------------------------------------------------------------------------: | ----------: | 
| Wind Turbine G128 5 Megawats                                                                                                       | 3718044.00  | 
| Wind Turbine G132 5 Megawats                                                                                                       | 3276187.00  | 
| Wind Turbine G114 2 Megawats                                                                                                       | 1532608.00  | 
| Wind Turbine G90 2 Megawats                                                                                                        | 1251625.00  | 
| Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.                                                                 | 191687.00   | 
| Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall | 167000.00   | 
| TCDE                                                                                                                               | 99075.00    | 
| Mercedes-Benz GLE (GLE 500 4MATIC)                                                                                                 | 91000.00    | 
| Mercedes-Benz S-Class (S 500)                                                                                                      | 85000.00    | 
| Mercedes-Benz SL (SL 350)                                                                                                          | 72000.00    |

Most likely, you won't be purchasing an industrial wind turbine. However, if you're considering buying a new car, now you know that the **Land Cruiser Prado**, **Mercedes-Benz GLA**, **Mercedes-Benz S-Class**, and **Mercedes-Benz SL** cause the most damage to our environment.It looks like all these goods are products of heavy industry. Let's check it out.

```
SELECT 
	ind_gr.industry_group AS 'Industry Group',
	prod_em.product_name AS 'Product name',
	ROUND(AVG(prod_em.carbon_footprint_pcf), 2) AS 'Average PCF'
FROM 
	product_emissions AS prod_em
LEFT JOIN
	industry_groups AS ind_gr ON prod_em.industry_group_id = ind_gr.id
GROUP BY prod_em.product_name
ORDER BY ROUND(AVG(prod_em.carbon_footprint_pcf), 2) DESC
LIMIT 10;
```

| Industry Group                     | Product name                                                                                                                       | Average PCF | 
| ---------------------------------: | ---------------------------------------------------------------------------------------------------------------------------------: | ----------: | 
| Electrical Equipment and Machinery | Wind Turbine G128 5 Megawats                                                                                                       | 3718044.00  | 
| Electrical Equipment and Machinery | Wind Turbine G132 5 Megawats                                                                                                       | 3276187.00  | 
| Electrical Equipment and Machinery | Wind Turbine G114 2 Megawats                                                                                                       | 1532608.00  | 
| Electrical Equipment and Machinery | Wind Turbine G90 2 Megawats                                                                                                        | 1251625.00  | 
| Automobiles & Components           | Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.                                                                 | 191687.00   | 
| Materials                          | Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall | 167000.00   | 
| Materials                          | TCDE                                                                                                                               | 99075.00    | 
| Automobiles & Components           | Mercedes-Benz GLE (GLE 500 4MATIC)                                                                                                 | 91000.00    | 
| Automobiles & Components           | Mercedes-Benz S-Class (S 500)                                                                                                      | 85000.00    | 
| Automobiles & Components           | Mercedes-Benz SL (SL 350)                                                                                                          | 72000.00    |  

Yes, the products with the highest PCF level are products of the following types of heavy industry: 
- Electrical Equipment and Machinery.
- Automobiles & Components
- Materials

Let's now try to check whether this pattern will persist if we calculate the average carbon emissions by industry.


### Top 10 Industries with the Highest Contribution to Carbon Emissions
In this section, we will be considering sums rather than averages. This is because averages depict the average level of emissions among industry products, which is more indicative of the environmental friendliness of a particular industry rather than its contribution.

```
SELECT 
	ind_gr.industry_group AS 'Industry Group',
	ROUND(SUM(prod_em.carbon_footprint_pcf), 2) AS 'Total PCF'
FROM 
	product_emissions AS prod_em
LEFT JOIN
	industry_groups AS ind_gr ON prod_em.industry_group_id = ind_gr.id
GROUP BY ind_gr.industry_group
ORDER BY ROUND(SUM(prod_em.carbon_footprint_pcf), 2) DESC
LIMIT 10;
```

| Industry Group                                   | Total PCF  | 
| -----------------------------------------------: | ---------: | 
| Electrical Equipment and Machinery               | 9801558.00 | 
| Automobiles & Components                         | 2582264.00 | 
| Materials                                        | 577595.00  | 
| Technology Hardware & Equipment                  | 363776.00  | 
| Capital Goods                                    | 258712.00  | 
| "Food, Beverage & Tobacco"                       | 111131.00  | 
| "Pharmaceuticals, Biotechnology & Life Sciences" | 72486.00   | 
| Chemicals                                        | 62369.00   | 
| Software & Services                              | 46544.00   | 
| Media                                            | 23017.00   |  
       
Yes, the picture the same! Our previous leaders are still leading the TOP.

It's also interesting to note that one of the leading industries (7th place) is **"Pharmaceuticals, Biotechnology & Life Sciences"**. Now we know what our health comes at.

### Top 10 Companies with the Highest Contribution to Carbon Emissions
```
SELECT 
	comp.company_name AS 'Company Name',
	ROUND(SUM(prod_em.carbon_footprint_pcf), 2) AS 'Total PCF'
FROM 
	product_emissions AS prod_em
LEFT JOIN
	companies AS comp ON prod_em.company_id = comp.id
GROUP BY comp.company_name
ORDER BY ROUND(SUM(prod_em.carbon_footprint_pcf), 2) DESC
LIMIT 10;
```

| Company Name                            | Total PCF  | 
| --------------------------------------: | ---------: | 
| "Gamesa Corporación Tecnológica, S.A."  | 9778464.00 | 
| Daimler AG                              | 1594300.00 | 
| Volkswagen AG                           | 655960.00  | 
| "Mitsubishi Gas Chemical Company, Inc." | 212016.00  | 
| "Hino Motors, Ltd."                     | 191687.00  | 
| Arcelor Mittal                          | 167007.00  | 
| Weg S/A                                 | 160655.00  | 
| General Motors Company                  | 137007.00  | 
| "Lexmark International, Inc."           | 132012.00  | 
| "Daikin Industries, Ltd."               | 105600.00  |  


### Top 10 Countries with the Highest Contribution to Carbon Emissions
```
SELECT 
	cntr.country_name AS 'Country name',
	ROUND(SUM(prod_em.carbon_footprint_pcf), 2) AS 'Average PCF'
FROM 
	product_emissions AS prod_em
LEFT JOIN
	countries AS cntr ON prod_em.country_id = cntr.id
GROUP BY cntr.country_name 
ORDER BY ROUND(SUM(prod_em.carbon_footprint_pcf), 2) DESC
LIMIT 10;
```

| Country name | Average PCF | 
| -----------: | ----------: | 
| Spain        | 9786130.00  | 
| Germany      | 2251225.00  | 
| Japan        | 653237.00   | 
| USA          | 518381.00   | 
| South Korea  | 186965.00   | 
| Brazil       | 169337.00   | 
| Luxembourg   | 167007.00   | 
| Netherlands  | 70417.00    | 
| Taiwan       | 62875.00    | 
| India        | 24574.00    |  

**Most of these countries are located on the European continent**, indicating that, on average, air quality in Europe is lower than in other continents.

### Analyzing the Trend of Carbon Footprints (PCFs) Over Time
By analyzing the "carbon_footprint_pcf" values across various years, we can determine whether emissions are on the rise, declining, or staying consistent. This analysis enables you to discern trends and potentially identify influencing factors.

```
SELECT 
	year,
	ROUND(AVG(carbon_footprint_pcf),2) as 'Average Emission',
    ROUND(SUM(carbon_footprint_pcf),2) as 'Total Emission'
FROM 
	product_emissions
GROUP BY 
	year 
ORDER BY 
	year ASC;
```
| year | Average Emission | Total Emission | 
| ---: | ---------------: | -------------: | 
| 2013 | 2399.32          | 503857.00      | 
| 2014 | 2457.58          | 624226.00      | 
| 2015 | 43188.90         | 10840415.00    | 
| 2016 | 6891.52          | 1640182.00     | 
| 2017 | 4050.85          | 340271.00      |

Two interesting observations stand out, prompting further investigation using additional data to uncover influencing factors:
* According to the data, there was a significant spike in carbon emissions levels in the year 2015.
* Following 2015, we observe a downward trend, albeit with emissions levels remaining higher than those prior to 2015.

To provide a clearer illustration of the points mentioned above, the following trend line charts were created using Google Sheets.
![image](https://github.com/RusAbk/Carbon-Emission-Analysis/blob/main/trendline.png?raw=true)


### Which industry group has demonstrated the most notable decrease in carbon footprints (PCFs) over time?

Through an analysis of emissions trends over time within each industry group, we can pinpoint sectors that have made significant strides in reducing their carbon footprints, highlighting commendable environmental initiatives.

```
SELECT 
	pr_em.year AS 'Year',
	ind_gr.industry_group AS 'Industry Group',
	ROUND(SUM(pr_em.carbon_footprint_pcf),2) AS 'Total Emission'
FROM 
	product_emissions AS pr_em
LEFT JOIN
	industry_groups AS ind_gr ON ind_gr.id = pr_em.industry_group_id
GROUP BY 
	pr_em.year,
	ind_gr.industry_group
ORDER BY 
	industry_group ASC,
	year ASC,
	ROUND(SUM(carbon_footprint_pcf),2) ASC
```

| Year | Industry Group                                                         | Total Emission | 
| ---: | ---------------------------------------------------------------------: | -------------: | 
| 2015 | "Consumer Durables, Household and Personal Products"                   | 931.00         | 
| 2013 | "Food, Beverage & Tobacco"                                             | 4995.00        | 
| 2014 | "Food, Beverage & Tobacco"                                             | 2685.00        | 
| 2015 | "Food, Beverage & Tobacco"                                             | 0.00           | 
| 2016 | "Food, Beverage & Tobacco"                                             | 100289.00      | 
| 2017 | "Food, Beverage & Tobacco"                                             | 3162.00        | 
| 2015 | "Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber" | 8909.00        | 
| 2015 | "Mining - Iron, Aluminum, Other Metals"                                | 8181.00        | 
| 2013 | "Pharmaceuticals, Biotechnology & Life Sciences"                       | 32271.00       | 
| 2014 | "Pharmaceuticals, Biotechnology & Life Sciences"                       | 40215.00       | 
| 2015 | "Textiles, Apparel, Footwear and Luxury Goods"                         | 387.00         | 
| 2013 | Automobiles & Components                                               | 130189.00      | 
| 2014 | Automobiles & Components                                               | 230015.00      | 
| 2015 | Automobiles & Components                                               | 817227.00      | 
| 2016 | Automobiles & Components                                               | 1404833.00     | 
| 2013 | Capital Goods                                                          | 60190.00       | 
| 2014 | Capital Goods                                                          | 93699.00       | 
| 2015 | Capital Goods                                                          | 3505.00        | 
| 2016 | Capital Goods                                                          | 6369.00        | 
| 2017 | Capital Goods                                                          | 94949.00       | 
| 2015 | Chemicals                                                              | 62369.00       | 
| 2013 | Commercial & Professional Services                                     | 1157.00        | 
| 2014 | Commercial & Professional Services                                     | 477.00         | 
| 2016 | Commercial & Professional Services                                     | 2890.00        | 
| 2017 | Commercial & Professional Services                                     | 741.00         | 
| 2013 | Consumer Durables & Apparel                                            | 2867.00        | 
| 2014 | Consumer Durables & Apparel                                            | 3280.00        | 
| 2016 | Consumer Durables & Apparel                                            | 1162.00        | 
| 2015 | Containers & Packaging                                                 | 2988.00        | 
| 2015 | Electrical Equipment and Machinery                                     | 9801558.00     | 
| 2013 | Energy                                                                 | 750.00         | 
| 2016 | Energy                                                                 | 10024.00       | 
| 2015 | Food & Beverage Processing                                             | 141.00         | 
| 2014 | Food & Staples Retailing                                               | 773.00         | 
| 2015 | Food & Staples Retailing                                               | 706.00         | 
| 2016 | Food & Staples Retailing                                               | 2.00           | 
| 2015 | Gas Utilities                                                          | 122.00         | 
| 2013 | Household & Personal Products                                          | 0.00           | 
| 2013 | Materials                                                              | 200513.00      | 
| 2014 | Materials                                                              | 75678.00       | 
| 2016 | Materials                                                              | 88267.00       | 
| 2017 | Materials                                                              | 213137.00      | 
| 2013 | Media                                                                  | 9645.00        | 
| 2014 | Media                                                                  | 9645.00        | 
| 2015 | Media                                                                  | 1919.00        | 
| 2016 | Media                                                                  | 1808.00        | 
| 2014 | Retailing                                                              | 19.00          | 
| 2015 | Retailing                                                              | 11.00          | 
| 2014 | Semiconductors & Semiconductor Equipment                               | 50.00          | 
| 2016 | Semiconductors & Semiconductor Equipment                               | 4.00           | 
| 2015 | Semiconductors & Semiconductors Equipment                              | 3.00           | 
| 2013 | Software & Services                                                    | 6.00           | 
| 2014 | Software & Services                                                    | 146.00         | 
| 2015 | Software & Services                                                    | 22856.00       | 
| 2016 | Software & Services                                                    | 22846.00       | 
| 2017 | Software & Services                                                    | 690.00         | 
| 2013 | Technology Hardware & Equipment                                        | 61100.00       | 
| 2014 | Technology Hardware & Equipment                                        | 167361.00      | 
| 2015 | Technology Hardware & Equipment                                        | 106157.00      | 
| 2016 | Technology Hardware & Equipment                                        | 1566.00        | 
| 2017 | Technology Hardware & Equipment                                        | 27592.00       | 
| 2013 | Telecommunication Services                                             | 52.00          | 
| 2014 | Telecommunication Services                                             | 183.00         | 
| 2015 | Telecommunication Services                                             | 183.00         | 
| 2015 | Tires                                                                  | 2022.00        | 
| 2015 | Tobacco                                                                | 1.00           | 
| 2015 | Trading Companies & Distributors and Commercial Services & Supplies    | 239.00         | 
| 2013 | Utilities                                                              | 122.00         | 
| 2016 | Utilities                                                              | 122.00         | 

Based on the data obtained, four main categories of industries can be identified:
* Those with **increasing emissions**
* Those with **decreasing emissions**
* Those with **stable levels**
* Those with **no discernible patterns**

Among the industries with decreasing emissions levels, the following can be highlighted:
* **Commercial & Professional Services**
* **Media**
* **Food & Staples Retailing** (Suspiciously sharp decrease, potential data error)

### Insights and patterns
During the dataset exploration, the following facts and patterns were identified:

* The products with the highest levels of carbon emissions are **typically associated with heavy industry**.
* The products with the highest levels of carbon emissions come **from the following industrial sectors**:
  1. Electrical Equipment and Machinery.
  2. Automobiles & Components
  3. Materials
* The following car models are leading in carbon emissions during production: **Land Cruiser Prado**, **Mercedes-Benz GLA**, **Mercedes-Benz S-Class**, and **Mercedes-Benz SL**
* One of the leading industries (7th place) is **"Pharmaceuticals, Biotechnology & Life Sciences"**. Now we know what our health comes at.
* Most of the countries with the highest contribution to carbon emissions are **located on the European continent**, indicating that, on average, air quality in Europe is lower than in other continents.
* According to the data, there was a **significant spike in carbon emissions levels in the year 2015**.
* Following 2015, we observe **a downward trend**, albeit with emissions levels **remaining higher than those prior to 2015**.
* Among the industries with **decreasing emissions levels**, the following can be highlighted:
  1. Commercial & Professional Services
  2. Media
  3. Food & Staples Retailing (Suspiciously sharp decrease, potential data error)

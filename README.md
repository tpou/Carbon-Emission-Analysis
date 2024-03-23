# Carbon-Emission-Analysis

## Let's have a look at the data

In this dataset we have 4 tables, that contain information about carbon emissions that occur during the production of goods. 

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

Before doing this, let's try to find out if there are any duplicate products in our table. If there is, then we will group it by product.

### Checking product duplicates
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

### TOP-10 Products Contributing Amount of Carbon Emission

```
SELECT 
	product_name,
	AVG(carbon_footprint_pcf) as 'Average PCF'
FROM 
	product_emissions
GROUP BY 
	product_name
ORDER BY AVG(carbon_footprint_pcf) DESC
LIMIT 10;
```
| product_name                                                                                                                       | Average PCF  | 
| ---------------------------------------------------------------------------------------------------------------------------------: | -----------: | 
| Wind Turbine G128 5 Megawats                                                                                                       | 3718044.0000 | 
| Wind Turbine G132 5 Megawats                                                                                                       | 3276187.0000 | 
| Wind Turbine G114 2 Megawats                                                                                                       | 1532608.0000 | 
| Wind Turbine G90 2 Megawats                                                                                                        | 1251625.0000 | 
| Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.                                                                 | 191687.0000  | 
| Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall | 167000.0000  | 
| TCDE                                                                                                                               | 99075.0000   | 
| Mercedes-Benz GLE (GLE 500 4MATIC)                                                                                                 | 91000.0000   | 
| Mercedes-Benz S-Class (S 500)                                                                                                      | 85000.0000   | 
| Mercedes-Benz SL (SL 350)                                                                                                          | 72000.0000   |  



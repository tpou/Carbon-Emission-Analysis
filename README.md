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

Most likely, you will not buy an industrial wind turbine. However, if you are thinking about buying a new car, then now you know that the Land Cruiser Prado, Mercedes-Benz GLA, Mercedes-Benz S-Class and Mercedes-Benz SL cause the greatest damage to our nature.

It looks like all these goods are products of heavy industry. Let's check it out.

```
SELECT 
	ind_gr.industry_group AS 'Industry Group',
	prod_em.product_name AS 'Product name',
	AVG(prod_em.carbon_footprint_pcf) AS 'Average PCF'
FROM 
	product_emissions AS prod_em
INNER JOIN
	industry_groups AS ind_gr ON prod_em.industry_group_id = ind_gr.id
GROUP BY prod_em.product_name
ORDER BY prod_em.carbon_footprint_pcf DESC
LIMIT 10;
```

| Industry Group                     | Product name                                                                                                                       | Average PCF  | 
| ---------------------------------: | ---------------------------------------------------------------------------------------------------------------------------------: | -----------: | 
| Electrical Equipment and Machinery | Wind Turbine G128 5 Megawats                                                                                                       | 3718044.0000 | 
| Electrical Equipment and Machinery | Wind Turbine G132 5 Megawats                                                                                                       | 3276187.0000 | 
| Electrical Equipment and Machinery | Wind Turbine G114 2 Megawats                                                                                                       | 1532608.0000 | 
| Electrical Equipment and Machinery | Wind Turbine G90 2 Megawats                                                                                                        | 1251625.0000 | 
| Automobiles & Components           | Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.                                                                 | 191687.0000  | 
| Materials                          | Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall | 167000.0000  | 
| Materials                          | TCDE                                                                                                                               | 99075.0000   | 
| Automobiles & Components           | Mercedes-Benz GLE (GLE 500 4MATIC)                                                                                                 | 91000.0000   | 
| Automobiles & Components           | Mercedes-Benz S-Class (S 500)                                                                                                      | 85000.0000   | 
| Automobiles & Components           | Mercedes-Benz SL (SL 350)                                                                                                          | 72000.0000   |         

Yes, the products with the highest PCF level are products of the following types of heavy industry: 
- Electrical Equipment and Machinery.
- Automobiles & Components
- Materials

Let's now try to check whether this pattern will persist if we calculate the average carbon emissions by industry.

### TOP-10 Industries Contributing Amount of Carbon Emission

```
SELECT 
	ind_gr.industry_group AS 'Industry Group',
	AVG(prod_em.carbon_footprint_pcf) AS 'Average PCF'
FROM 
	product_emissions AS prod_em
INNER JOIN
	industry_groups AS ind_gr ON prod_em.industry_group_id = ind_gr.id
GROUP BY ind_gr.industry_group
ORDER BY prod_em.carbon_footprint_pcf DESC
LIMIT 10;
```

| Industry Group                                                         | Average PCF | 
| ---------------------------------------------------------------------: | ----------: | 
| Automobiles & Components                                               | 35373.4795  | 
| "Pharmaceuticals, Biotechnology & Life Sciences"                       | 24162.0000  | 
| Chemicals                                                              | 1949.0313   | 
| Materials                                                              | 3208.8611   | 
| Media                                                                  | 1534.4667   | 
| "Mining - Iron, Aluminum, Other Metals"                                | 2727.0000   | 
| Technology Hardware & Equipment                                        | 1362.4569   | 
| Containers & Packaging                                                 | 373.5000    | 
| "Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber" | 685.3077    | 
| Commercial & Professional Services                                     | 119.6591    |         

No, the picture has changed a bit.

The **Automobile & Components** and **Materials** industries continue to occupy some of the leading positions in the TOP leaders in carbon emissions. However, the **Electrical Equipment and Machinery** industry has given way to new leaders.

Interesting, that one of the leading industries (2nd place) is **Pharmaceuticals, Biotechnology & Life Sciences**. Now we know what our health costs us.


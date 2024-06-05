# Carbon-Emission-Analysis
![](https://assets.terrapass.com/wp-content/uploads/2022/08/carbon-dioxide-emissions-from-electricity-photo-of-factory-smoke.jpg)

Source: Kamil Petran 

This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.

## Data Description
### 1. product_emissions
| id            | company_id | country_id | industry_group_id | year | product_name                                                    | weight_kg | carbon_footprint_pcf | upstream_percent_total_pcf                       | operations_percent_total_pcf                     | downstream_percent_total_pcf                     | 
| ------------: | ---------: | ---------: | ----------------: | ---: | --------------------------------------------------------------: | --------: | -------------------: | -----------------------------------------------: | -----------------------------------------------: | -----------------------------------------------: | 
| 10056-1-2014  | 82         | 28         | 2                 | 2014 | Frosted Flakes(R) Cereal                                        | 0.7485    | 2                    | 57.50                                            | 30.00                                            | 12.50                                            | 
| 10056-1-2015  | 82         | 28         | 15                | 2015 | "Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)" | 0.7485    | 2                    | 57.50                                            | 30.00                                            | 12.50                                            | 
| 10222-1-2013  | 83         | 28         | 8                 | 2013 | Office Chair                                                    | 20.68     | 73                   | 80.63                                            | 17.36                                            | 2.01                                             | 
| 10261-1-2017  | 14         | 16         | 25                | 2017 | Multifunction Printers                                          | 110       | 1488                 | 30.65                                    

### 2. companies
| id | company_name                  | 
| -: | ----------------------------: | 
| 1  | "Autodesk, Inc."              | 
| 2  | "Casio Computer Co., Ltd."    | 
| 3  | "Cisco Systems, Inc."         | 
| 4  | "CNX Coal Resources, LP"      | 
| 5  | "Coca-Cola Enterprises, Inc." | 

### 3. countries
| id | country_name | 
| -: | -----------: | 
| 1  | Australia    | 
| 2  | Belgium      | 
| 3  | Brazil       | 
| 4  | Canada       | 
| 5  | Chile        | 

### 4. industry_groups
| id | industry_group                                                         | 
| -: | ---------------------------------------------------------------------: | 
| 1  | "Consumer Durables, Household and Personal Products"                   | 
| 2  | "Food, Beverage & Tobacco"                                             | 
| 3  | "Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber" | 
| 4  | "Mining - Iron, Aluminum, Other Metals"                                | 
| 5  | "Pharmaceuticals, Biotechnology & Life Sciences"                       | 

## Analysis
### 1. Top 5 products contribute the most to carbon emissions

#### Code
   
    Select company_id, country_id, product_name, carbon_footprint_pcf
    from product_emissions
    order by carbon_footprint_pcf desc
    limit 5


#### Result

| product_name                                                       | carbon_footprint_pcf | 
| -----------------------------------------------------------------: | -------------------: | 
| Wind Turbine G128 5 Megawats                                       | 3718044              | 
| Wind Turbine G132 5 Megawats                                       | 3276187              | 
| Wind Turbine G114 2 Megawats                                       | 1532608              | 
| Wind Turbine G90 2 Megawats                                        | 1251625              | 
| Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit. | 191687               | 

### 2. The industry groups of these products

#### Code

	select industry_group, product_name
	from
		(select i.industry_group, p.product_name, p.company_id, p.year, p.country_id, p.carbon_footprint_pcf as total
		from industry_groups as i
		left join product_emissions as p 
		on i.id = p.industry_group_id	
		group by p.industry_group_id, p.carbon_footprint_pcf
		order by p.carbon_footprint_pcf desc) as temp
		order by total desc
	limit 5

#### Result

| industry_group                     | product_name                                                       | 
| ---------------------------------: | -----------------------------------------------------------------: | 
| Electrical Equipment and Machinery | Wind Turbine G128 5 Megawats                                       | 
| Electrical Equipment and Machinery | Wind Turbine G132 5 Megawats                                       | 
| Electrical Equipment and Machinery | Wind Turbine G114 2 Megawats                                       | 
| Electrical Equipment and Machinery | Wind Turbine G90 2 Megawats                                        | 
| Automobiles & Components           | Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit. | 

### 3. The industries with the highest contribution to carbon emissions

#### Code

	select industry_group, sum(total) as total_carbon
	from
		(select i.industry_group, p.industry_group_id, p.company_id, p.year, p.country_id, p.carbon_footprint_pcf as total
		from industry_groups as i
		left join product_emissions as p 
		on i.id = p.industry_group_id	
		group by p.industry_group_id, p.carbon_footprint_pcf
		order by p.carbon_footprint_pcf desc) as temp
	group by industry_group
	order by total_carbon desc
	limit 10

 #### Result 

| industry_group                                   | total_carbon | 
| -----------------------------------------------: | -----------: | 
| Electrical Equipment and Machinery               | 9801558      | 
| Automobiles & Components                         | 2076022      | 
| Materials                                        | 416860       | 
| Capital Goods                                    | 255104       | 
| Technology Hardware & Equipment                  | 204307       | 
| "Food, Beverage & Tobacco"                       | 107653       | 
| "Pharmaceuticals, Biotechnology & Life Sciences" | 72486        | 
| Chemicals                                        | 44933        | 
| Software & Services                              | 23679        | 
| Media                                            | 11451        | 

### 4. The companies with the highest contribution to carbon emissions

#### Code

	select company_name, sum(total) as total_carbon
	from
		  (select c.company_name, p.company_id, p.year, p.country_id, p.carbon_footprint_pcf as total
		  from companies as c
		  left join product_emissions as p 
		  on c.id = p.company_id	
		  group by p.company_id, p.carbon_footprint_pcf
		  order by p.carbon_footprint_pcf desc) as temp
	group by company_name
	order by total_carbon desc
	limit 5

#### Result

| company_name                           | total_carbon | 
| -------------------------------------: | -----------: | 
| "Gamesa Corporación Tecnológica, S.A." | 9778464      | 
| Daimler AG                             | 1478300      | 
| Volkswagen AG                          | 265718       | 
| "Hino Motors, Ltd."                    | 191687       | 
| Arcelor Mittal                         | 167007       | 

### 5. The countries with the highest contribution to carbon emissions

#### Code

	select country_name, sum(total) as total_carbon
	from
		(select cn.country_name, p.company_id, p.year, p.country_id, p.carbon_footprint_pcf as total
		from countries as cn
		left join product_emissions as p 
		on cn.id = p.country_id	
		group by p.country_id, p.carbon_footprint_pcf
		order by p.carbon_footprint_pcf desc) as temp
	group by country_name
	order by total_carbon desc
	limit 5

 #### Result

| country_name | total_carbon | 
| -----------: | -----------: | 
| Spain        | 9785971      | 
| Germany      | 1744983      | 
| Japan        | 500392       | 
| USA          | 380132       | 
| Brazil       | 167578       | 

### 6. The trend of carbon footprints (PCFs) over the years

#### Code

	select year, sum(carbon_footprint_pcf) as total_carbon
	from product_emissions
	group by year

#### Result

| year | total_carbon | 
| ---: | -----------: | 
| 2013 | 503857       | 
| 2014 | 624226       | 
| 2015 | 10840415     | 
| 2016 | 1640182      | 
| 2017 | 340271       | 

### 7. Industry groups has demonstrated the most notable decrease in carbon footprints (PCFs) over time

#### Code

	select industry_group, year, sum(carbon_footprint_pcf) as total_carbon
	from
		(select i.industry_group, p.year, p.carbon_footprint_pcf
		from product_emissions as p
		left join industry_groups as i
		on p.industry_group_id = i.id) as trial
	group by industry_group, year
	order by industry_group, year, total_carbon desc

## Conclusion

The most industry group which contribute the most to carbon emissions is **Electrical Equipment and Machinery**. In top 5 of products, Electrical Equipment and Machinery has 4 products contribute the most to carbon emissions (Wind Turbine). After that is **Automobiles & Components** with Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit. Although Automobiles & Components in the second, the total carbon is 2,076,022 while Electrical Equipment and Machinery is 9,801,558 (4.7 times higher), even higher than the total carbon of all industry groups on top 10

# Carbon-Emission-Analysis
![](https://assets.terrapass.com/wp-content/uploads/2022/08/carbon-dioxide-emissions-from-electricity-photo-of-factory-smoke.jpg)

Source: Kamil Petran 

This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.

## Mô tả dữ liệu
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

    select industry_group
	from
		(select i.industry_group, p.industry_group_id, p.company_id, p.year, p.country_id, p.carbon_footprint_pcf as total
		from industry_groups as i
		left join product_emissions as p 
		on i.id = p.industry_group_id	
		group by p.industry_group_id, p.carbon_footprint_pcf
		order by p.carbon_footprint_pcf desc) as temp
	limit 5

#### Result

| industry_group                     | 
| ---------------------------------: | 
| Electrical Equipment and Machinery | 
| Electrical Equipment and Machinery | 
| Electrical Equipment and Machinery | 
| Electrical Equipment and Machinery | 
| Automobiles & Components           | 

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
	limit 5

 #### Result 

| industry_group                     | total_carbon | 
| ---------------------------------: | -----------: | 
| Electrical Equipment and Machinery | 9801558      | 
| Automobiles & Components           | 2076022      | 
| Materials                          | 416860       | 
| Capital Goods                      | 255104       | 
| Technology Hardware & Equipment    | 204307       | 

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

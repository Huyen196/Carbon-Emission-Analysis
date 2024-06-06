![image](https://github.com/Huyen196/Carbon-Emission-Analysis/assets/170104292/99e718c2-7681-42cf-802b-faa9ec2a1df9)# Carbon-Emission-Analysis
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

	select company_name, industry_group, sum(total) as total_carbon
	from
		(select c.company_name, p.company_id, p.year, p.country_id, i.industry_group, p.carbon_footprint_pcf as total
		from product_emissions as p
		left join companies as c
		on p.company_id = c.id
		left join industry_groups as i
		on p.industry_group_id = i.id
		group by p.company_id, p.carbon_footprint_pcf
		order by p.carbon_footprint_pcf desc) as temp
	group by company_name
	order by total_carbon desc
	limit 5

#### Result

| company_name                           | industry_group                     | total_carbon | 
| -------------------------------------: | ---------------------------------: | -----------: | 
| "Gamesa Corporación Tecnológica, S.A." | Electrical Equipment and Machinery | 9778464      | 
| Daimler AG                             | Automobiles & Components           | 1478300      | 
| Volkswagen AG                          | Automobiles & Components           | 265718       | 
| "Hino Motors, Ltd."                    | Automobiles & Components           | 191687       | 
| Arcelor Mittal                         | Materials                          | 167007       | 


### 5. The countries with the highest contribution to carbon emissions

#### Code

	select country_name, industry_group, sum(total) as total_carbon
		from
			(select cn.country_name, p.company_id, i.industry_group, p.year, p.country_id, p.carbon_footprint_pcf as total
			from product_emissions as p
			left join countries as cn
			on p.country_id = cn.id
			left join industry_groups as i
			on p.industry_group_id = i.id
			group by p.country_id, p.carbon_footprint_pcf
			order by p.carbon_footprint_pcf desc) as temp
		group by country_name
		order by total_carbon desc
		limit 10

 #### Result

| country_name | industry_group                                   | total_carbon | 
| -----------: | -----------------------------------------------: | -----------: | 
| Spain        | Electrical Equipment and Machinery               | 9785971      | 
| Germany      | Automobiles & Components                         | 1744983      | 
| Japan        | Automobiles & Components                         | 500392       | 
| USA          | "Pharmaceuticals, Biotechnology & Life Sciences" | 380132       | 
| Brazil       | Capital Goods                                    | 167578       | 
| Luxembourg   | Materials                                        | 167007       | 
| South Korea  | "Food, Beverage & Tobacco"                       | 140993       | 
| Netherlands  | Chemicals                                        | 60806        | 
| India        | "Mining - Iron, Aluminum, Other Metals"          | 23873        | 
| Taiwan       | Technology Hardware & Equipment                  | 19367        | 

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

Gamesa Corporación Tecnológica, S.A company in Electrical Equipment and Machinery industry is the company with the highest contribution to carbon emissions. After that, Automobiles & Components industry has 3 companies in top 5. Clearly, most of products which contribute a lot of carbon emissions are from heavy industry.

In general, half of countries in top 10 countries with the highest contribution to carbon emissions in Europe and they also focus on heavy industry (Electrical Equipment and Machinery ,  Automobiles & Components, Materials, Chemicals) with Spain is the frist one. Beside that, Asia developed countries also on the list including Japan (Automobiles & Components), South Korea ("Food, Beverage & Tobacco"), India ("Mining - Iron, Aluminum, Other Metals") and Taiwan (Technology Hardware & Equipment. However, Electrical Equipment and Machinery and Automobiles & Components in Spain and Germany accounted for the largest proportion compared to the remaining industries (Spain: 9785971, Germany: 1744983)

Although the carbon emissions is really high, in the positive way, all countries are trying to reduce through each years. 2015 is the year had the highest contribution to carbon emissions with 10840415. However, after 1 year, in 2016, this number reduced dramatically about 9200000 (from 10840415 to 1640182) and continusly reduce until 2017 (only 340271). This proves that industries are paying more attention to the environment and trying to minimize the amount of carbon emissions released into the environment.


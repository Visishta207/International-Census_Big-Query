# International Census Queries

## Overview of the dataset

The United States Census Bureau’s international dataset provides estimates of country populations since 1950 and projections through 2050. Additionally, time-series data is provided for attributes including fertility rates, birth rates, death rates, and migration rates.

## 1. Number of countries included in the International Census

```sql
SELECT count(distinct country_code) AS countries FROM `bigquery-public-data.census_bureau_international.age_specific_fertility_rates`
```

*Query Result:*

![image](https://user-images.githubusercontent.com/89730283/131255257-bb848fb6-6a3d-4897-9179-aba40068204c.png)

228 countries' data was taken by the United States Census Bureau.

## 2. From 1995 to 2005, list the top 10 countries with highest average more male births per female births (sex ratio).

```sql
SELECT country_name,country_code, avg(sex_ratio_at_birth) AS average_sex_ratio
FROM bigquery-public-data.census_bureau_international.age_specific_fertility_rates
WHERE year >= 1995 AND year < 2005
GROUP BY country_name, country_code
ORDER BY avg(sex_ratio_at_birth) DESC 
LIMIT 10
```

*Query Result*

![image](https://user-images.githubusercontent.com/89730283/131255525-47e595c4-adf5-47ee-95a2-3d61419e8d36.png)

Northern Marina Islands had the highest male births per female births in the time period 1995 - 2005 with a sex ratio of 1.1711. It is closely followed by Armenia and China.

## 3. In 2010, which countries had more female infant mortality rate than male?

```sql
SELECT country_name, infant_mortality_male, infant_mortality_female
FROM bigquery-public-data.census_bureau_international.mortality_life_expectancy
WHERE year = 2010 AND infant_mortality_female > infant_mortality_male
ORDER BY infant_mortality_female DESC 
LIMIT 10
```

*Query Result*

![image](https://user-images.githubusercontent.com/89730283/131255846-d8fc7a8e-6210-4f00-a47e-cd2d78fab084.png)

In 2010, India ranked the highest wrt to more female infant mortality rate compared to male infant mortality rate.

## 4. In 2010, which countries had highest net migration rate?

```sql
SELECT country_name, country_code, net_migration
FROM bigquery-public-data.census_bureau_international.birth_death_growth_rates
WHERE year = 2010
ORDER BY net_migration DESC
LIMIT 10
```

*Query Result*

![image](https://user-images.githubusercontent.com/89730283/131255753-d4dac6c6-e416-47d2-9d75-95c5736ac1d9.png)

In 2010, Qatar had the highest net migration rate, followed by South Sudan with a large differenence in the net migration rate.

### Queries using JOIN

## 5. In 2000, which 10 countries with area more than 50,000 square kms have highest life expentacy

```sql
SELECT a.country_name,s.country_area, a.life_expectancy 
FROM (
SELECT country_name, life_expectancy
FROM `bigquery-public-data.census_bureau_international.mortality_life_expectancy`
WHERE year = 2000) a
INNER JOIN (
SELECT country_name,country_area
FROM `bigquery-public-data.census_bureau_international.country_names_area` 
WHERE country_area >= 50000) s
ON
a.country_name = s.country_name
ORDER BY life_expectancy DESC
LIMIT 10
```

*Query Result*

![image](https://user-images.githubusercontent.com/89730283/131255954-cfccee93-cde0-4eb9-a19b-a936a0e82248.png)

In 2000, among the countries with area more than 50k sq. kms. Japan had the highest life expectancy rate closely followed by Canada and Sweden

## 6. In the year 2005, which top 10 countries had more proportion of old age population (above 65 years)

```sql
SELECT a.country_name,SUM(a.population) AS Above_65,p.midyear_population AS Total,SUM(a.population) / p.midyear_population * 100 AS Percentage_above_65
FROM (
SELECT country_code,country_name,population  
FROM `bigquery-public-data.census_bureau_international.midyear_population_agespecific`
WHERE year =2005 AND age > 65) a
INNER JOIN (
SELECT midyear_population, country_code
FROM `bigquery-public-data.census_bureau_international.midyear_population`
WHERE year = 2005) p
ON
a.country_code = p.country_code
GROUP BY country_name, Total
ORDER BY Percentage_above_65 DESC 
LIMIT 10
```

*Query Result*

![image](https://user-images.githubusercontent.com/89730283/131256061-fbc2ddad-537f-4457-83fb-ea7c011eae0f.png)

In 2005, Monaco had the highest proportion of old aged people followed by Japan and Italy.

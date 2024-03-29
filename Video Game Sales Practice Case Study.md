# Video Game Sales Practice Case Study
Citation: Gregory Smith. (2022). Video Games Sales [Data set]. Zenodo. https://doi.org/10.5281/zenodo.5898311


This dataset contains a list of video games with sales greater than 100,000 copies. It was generated by a scrape of vgchartz.com. 

 Platform Key: 
  * 2600 - Atari 2600 
  * GB - Game Boy   
  * GBA - Game Boy Advance   
  * GC - GameCube
  * GEN - Sega Genesis       
  * N64 - Nintendo   
  * NES - Nintendo Entertainment System         
  * PS - PlayStation        
  * PS2 - PlayStation 2  
  * PS3  - PlayStation 3     PS4 - PlayStation 4
  * PSP - PlayStation Portable        
  * SNES - Super Nintendo Entertainment System
  * X360 - XBox 360       
  * XB - XBox        
  * XOne - XBox One
           
Main Areas/ Continents: 
  * na - North America        
  * eu - Europe       
  *  jp - Japan


Sales - Sales are per 100,000
  ex: 1 sale  = 100,000 sales OR .5 sale = 50,000 sales


Stats:
* Data spans 1980 to 2016 (36 years)
* 17 total genres
* 500 games


Cleaning Steps:


1. Reduced the data to the top 500 ranked games
2. Reviewed for missing/incomplete data and filled it in 
   1. Genre, Year
      1. Friend Collection is actually Tomodachi Collection 
3. Standardized formatting to be uniform
   1. i.e. Operating System 2600 is not a number it is text


Limitations:
* This dataset does include all country sales and instead groups them as “other_sales”, making it difficult to verify the validity of the sales
* I limited the the dataset to the top 500 sales, which limited the year range to 2016




Questions: 
1. Which area consumes/ buys the most video games?
2. What are the 5 most popular genres in each listed region (North America, Europe, Japan)?
3. What is the most popular game in North America, Europe and Japan by sales?
4. What is the most popular game by sales?
5. What are the top 5 consoles?
6. Which console produced the most sales?
7. Total video game sales per year?
8. How many video games were sold per genre?



## SQL Data Results

***1. Which area consumes/ buys the most video games?***
  - I used the SUM function to look at the total sales for each listed region, instead of identifying the top consumer of video games. I rounded the total_na_sales as it was a trailing float. 
```
SELECT  
  ROUND(SUM(na_sales),2) AS total_na_sales,
  SUM(eu_sales) AS total_eu_sales,
  SUM(jp_sales) AS total_jp_sales
FROM 
  `portfolio-projects-365917.sql_case_studies.video_game_sales`;
  ```
*Answer: North America bought the most video games total*


 ![question_1](https://github.com/codewars/discord-bot/assets/122945929/6844904b-153f-4bab-a93b-8b8b1bf27f1f)



***2. What are the 5 most popular genres in each listed region (North America, Europe, Japan)?***
  - I used the SUM and GROUP BY functions on each regions sales to find the total sales for each genre listed  


**North America**
```
SELECT  
  Genre,
  ROUND(SUM(na_sales),2) AS na_genre_sales,
FROM
  `portfolio-projects-365917.sql_case_studies.video_game_sales`
GROUP BY 
  Genre
ORDER BY 
  na_genre_sales DESC
LIMIT 5;
```
![question_2_na](https://github.com/codewars/discord-bot/assets/122945929/d4ab2081-31b3-4a9b-aa20-f147197dd702)



**Europe**
```
SELECT  
  Genre,
  ROUND(SUM(eu_sales),2) AS eu_genre_sales,
FROM
  `portfolio-projects-365917.sql_case_studies.video_game_sales`
GROUP BY 
  Genre
ORDER BY 
  eu_genre_sales DESC
LIMIT 5;
```
![question_2_eu](https://github.com/codewars/discord-bot/assets/122945929/fd385a74-cd3b-41ae-a7c8-e39ad92b53b6)







**Japan**
```
SELECT  
  Genre,
  ROUND(SUM(jp_sales),2) AS jp_genre_sales,
FROM
  `portfolio-projects-365917.sql_case_studies.video_game_sales`
GROUP BY 
  Genre
ORDER BY 
  jp_genre_sales DESC
LIMIT 5;
```
![question_2_jp](https://github.com/codewars/discord-bot/assets/122945929/00c474cb-d92d-431f-8851-50b60743941f)

|Answer| North America | Europe | Japan |
|---|---|---|---|
|1. | Shooter | Action | Role-Playing |
|2. | Action| Sports | Platform |
|3. | Platform | Shooter | Action
|4. | Sports | Platform | Action
|5. | Role-Playing | Role-Playing | Sports








***3. What is the most popular game in North America, Europe and Japan by sales?***        
  - I created a subquery to find the name of the most popular game. 


**North America**
```
SELECT 
  Name,
  na_sales
FROM 
  `portfolio-projects-365917.sql_case_studies.video_game_sales` 
WHERE 
  na_sales IN (
    SELECT MAX(na_sales)
    FROM `portfolio-projects-365917.sql_case_studies.video_game_sales`
  );
  ```
![question_3_na](https://github.com/codewars/discord-bot/assets/122945929/00e23bd0-eaa9-4b43-813c-a57444ba979c)



**Europe**
```
SELECT 
  Name,
  eu_sales
FROM 
  `portfolio-projects-365917.sql_case_studies.video_game_sales` 
WHERE 
  eu_sales IN (
    SELECT MAX(eu_sales)
    FROM `portfolio-projects-365917.sql_case_studies.video_game_sales`
  );
```  
![question_3_eu](https://github.com/codewars/discord-bot/assets/122945929/2cf392a5-50b4-495e-99d3-f9529dc6a85d)



**Japan**
```
SELECT 
  Name,
  jp_sales
FROM 
  `portfolio-projects-365917.sql_case_studies.video_game_sales` 
WHERE 
  jp_sales IN (
    SELECT MAX(jp_sales)
    FROM `portfolio-projects-365917.sql_case_studies.video_game_sales`
  );
```  
![question_3_jp](https://github.com/codewars/discord-bot/assets/122945929/f988695e-486f-4e32-a58b-815ead43f482)


*Answer:*
  - *North America: Wii Sports*
  - *Europe: Wii Sports*
  - *Japan: Pokemon Red/Pokemon Blue*




***4. What is the most popular game by sales?***
  - This is a variation of the subquery from question 3. The included column global sales is the sum of all 4 regions (North America, Europe, Japan, and Other). All that was left was to isolate the name of the most and least popular games. 

**Most Popular**
```
SELECT 
  Name,
  global_sales
FROM 
  `portfolio-projects-365917.sql_case_studies.video_game_sales` 
WHERE 
  global_sales IN (
    SELECT MAX (global_sales)
    FROM `portfolio-projects-365917.sql_case_studies.video_game_sales`
  );
```  
![question_4_most](https://github.com/codewars/discord-bot/assets/122945929/ce4a8693-799d-42b5-a94e-9a68832c46cd)


**Least Popular**
```
SELECT 
  Name,
  global_sales
FROM 
  `portfolio-projects-365917.sql_case_studies.video_game_sales` 
WHERE 
  global_sales IN (
    SELECT MIN (global_sales)
    FROM `portfolio-projects-365917.sql_case_studies.video_game_sales`
  );
```
![question_4_least](https://github.com/codewars/discord-bot/assets/122945929/08489bc9-2298-4d1d-a23d-8efac3b1079c)

*Answer:*
|Most Popular|                                 Least Popular|
|---|---|
|Wii Sports|  Need for Speed: Most Wanted|
| |Hot Shots Golf 3|


***5. What are the top 5 consoles?***
```
SELECT 
  Platform,
FROM 
  `portfolio-projects-365917.sql_case_studies.video_game_sales` 
GROUP BY
  Platform
ORDER BY
  SUM(global_sales) DESC
LIMIT 5;
```
![question_5](https://github.com/codewars/discord-bot/assets/122945929/5eddaf61-7df9-4d76-8446-94888f21d4ef)







***6. Which console produced the most sales?***
```
SELECT 
  Platform,
  ROUND(SUM(global_sales),2) as total_sales
FROM 
  `portfolio-projects-365917.sql_case_studies.video_game_sales` 
GROUP BY
  Platform
ORDER BY
  total_sales DESC
LIMIT 5;
 ```
 *Answer: Wii with 44,446,000 sales*
 
 
![question_6](https://github.com/codewars/discord-bot/assets/122945929/7231e953-42fb-4c67-9383-6bf34e0ddf19)





***7. Total video game sales per year?***
```
SELECT 
  Year,
  ROUND(SUM(global_sales),2) AS total_sales
FROM 
  `portfolio-projects-365917.sql_case_studies.video_game_sales`
GROUP BY
  YEAR
ORDER BY
  Year;
 ```
  ![question_7.1](https://github.com/codewars/discord-bot/assets/122945929/5c579ac3-5f74-4914-8d63-d46b67022758)
  ![question_7.2](https://github.com/codewars/discord-bot/assets/122945929/6d4f7dd2-2534-4e41-bc70-b7270c4a2be7)







***8. How many video games were sold per genre?***
```
SELECT 
  Genre,
  COUNT(Name) AS num_sold
FROM 
  `portfolio-projects-365917.sql_case_studies.video_game_sales`
GROUP BY
  Genre
ORDER BY
  num_sold;
  ```
![question_8](https://github.com/codewars/discord-bot/assets/122945929/36ec8993-4683-4fa9-b7be-647b74ce5a5b)

# Academy Award Winners - Box Office Success and IMDb Rating

The purpose of this project is to see if a movie's box office success and IMDb rating has any correlation with Oscar nominations and winners. I also included studio and genre information to find any additional relationships. Using the [IMDb non-commercial datasets](https://datasets.imdbws.com/), I downloaded the title.basics.tsv.gz and title.ratings.tsv.gz from IMDb's Non-Commercial dataset. I also downloaded a domestic lifetime gross film from Kaggle [here](https://www.kaggle.com/datasets/thedevastator/hollywood-movies-domestic-lifetime-gross-and-ran) and an Oscar award nomination and winner file from Kaggle [here](https://www.kaggle.com/datasets/unanimad/the-oscar-award). The first file has a list of movies with their release year and box office amount; the second file has a list of all nominations and winners from 1927 to 2023, with release year and Oscar year. The year of the Oscar award is usually one year after the movie was released, i.e. the 2023 Oscars include all movies from the 2022 calendar year. For this project, I only wanted to look at movies released from 2000 to 2018 and the only Oscar categories I considered were from the "Big Five" categories of: Best Picture, Best Director, Best Writing (both Original and Adapted Screenplays), Best Actor, and Best Actress.

## Data Collection
Joining the two IMDb datasets on <b>tconst</b> (a unique identifier for each movie), I collected <b>primaryTitle</b>, <b>originalTitle</b> (if it is a foreign movie, this is the title in the original language), <b>startYear</b> (year the movie was released), movie length, genres, and IMDb rating. There are also several qualifications for an Academy Award but for the purposes of this project, I could only use one of those qualifications, which is that the movie is at least 40 minutes long. I also excluded adult movies, ensured I had a movie length and start year, and only ran the query for movies from 2000 to 2018. The query and partial results below:

### SQL
````sql
select movies.tconst
     , primaryTitle
	 , originalTitle
	 , startYear
	 , runtimeMinutes
	 , genres
	 , averageRating
  from [MovieProject].[dbo].[movies] as movies
  join [MovieProject].[dbo].[ratings] as ratings
    on movies.tconst = ratings.tconst
 where 1=1
   and titleType = 'movie'
   and isAdult = 0
   and runtimeMinutes <> '\N'
   and startYear <> '\N'
   and runtimeMinutes >= 40
   and (startYear between 2000 and 2018)
````

### Partial Results

![image](https://github.com/mraibon/AcademyAwards/blob/main/Images/Partial%20SQL%20Results.png?raw=true)

Next, I extracted these results into an Excel workbook. I copied the data from the other two Excel workbooks I downloaded from Kaggle and pasted them into this workbook. With all the data in one place, I would need a way to join the data together. In order to do this, I relied upon Excel's Fuzzy Lookup to match movies together. I concatenated the title and release year columns in each table as my initial unique identifier. Then, I would use the <b>tconst</b> column from the IMDb data as my unique identifier across all three tables. I have included screenshots of the Fuzzy Lookup below:

### Converting the datasets into Excel "tables" to use Fuzzy Lookup

![image](https://github.com/mraibon/AcademyAwards/blob/main/Images/Fuzzy%20Lookup%201.png?raw=true)

### Fuzzy Lookup between IMDb and Oscar's tables

![image](https://github.com/mraibon/AcademyAwards/blob/main/Images/Fuzzy%20Lookup%202.png?raw=true)

### Results with Similarity Number

![image](https://github.com/mraibon/AcademyAwards/blob/main/Images/Fuzzy%20Lookup%203.png?raw=true)

The similarity number provides a decimal number from 0 to 1 that shows how closely matched the concatenation is: a match of 1.0 means a perfect match. Because my concatenation of title and release year isn't perfect, there were unfortunately many movies that didn't have a 1.0. I decided that I would only include movies that had a 0.9 or greater match and I would manually check to ensure accuracy. Although this isn't ideal and not recommended, it was the best way to approach this project given the datasets and resources I had available. After using Fuzzy Lookup, I was able to use the <b>tconst</b> column in each of the three tables now and could join them in Tableau for my visualizations. 

![image](https://github.com/mraibon/AcademyAwards/blob/main/Images/Academy%20Awards%20Dashboard.png?raw=true)

## Conclusion

Because my data is not perfect and because I only used two metrics (box office and IMDb rating), it is difficult to draw any solid conclusions on whether or not generating a ton of success by way of revenue or viewer feedback will result in success at the Oscar's. However, there are surprises and interesting conclusions I found in the data:
* In 2000, <b>Gladiator</b> oddly stands out as a data point amongst the other movies released that year - and it also won Best Picture and Best Actor!
* In 2010, both Toy Story 3 and Inception exceeded the Best Picture winner (The King's Speech) in both revenue and IMDb rating. However, as a fan of all three movies, I understand the difficulty in making the decision by the Academy.
* I observed nine movies that won both Best Picture and Best Director, three movies that won both Best Picture and Best Actor, and only one movie that won both Best Picture and Best Actress.

## Dataset Issues
* Data could only be accurately joined between the IMDb datasets; in order to join the remaining datasets, I had to use Microsoft Excel's "Fuzzy Lookup" functionality.
* Not all movies were included in the timeframe, due to the above issue.

## References
* https://datasets.imdbws.com/
* https://www.kaggle.com/datasets/thedevastator/hollywood-movies-domestic-lifetime-gross-and-ran
* https://www.kaggle.com/datasets/unanimad/the-oscar-award

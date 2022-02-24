---
title: 'Weekly Exercises #3'
author: "Jenny Li"
output: 
  html_document:
    keep_md: TRUE
    toc: TRUE
    toc_float: TRUE
    df_print: paged
    code_download: true
---





```r
library(tidyverse)     # for graphing and data cleaning
library(gardenR)       # for Lisa's garden data
library(lubridate)     # for date manipulation
library(ggthemes)      # for even more plotting themes
library(geofacet)      # for special faceting with US map layout
library(dplyr)
Sys.setlocale("LC_TIME", "English")
```

```
## [1] "English_United States.1252"
```

```r
theme_set(theme_minimal())       # My favorite ggplot() theme :)
```


```r
# Lisa's garden data
data("garden_harvest")

# Seeds/plants (and other garden supply) costs
data("garden_spending")

# Planting dates and locations
data("garden_planting")

# Tidy Tuesday dog breed data
breed_traits <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2022/2022-02-01/breed_traits.csv')
trait_description <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2022/2022-02-01/trait_description.csv')
breed_rank_all <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2022/2022-02-01/breed_rank.csv')

# Tidy Tuesday data for challenge problem
kids <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-09-15/kids.csv')
```

## Setting up on GitHub!

Before starting your assignment, you need to get yourself set up on GitHub and make sure GitHub is connected to R Studio. To do that, you should read the instruction (through the "Cloning a repo" section) and watch the video [here](https://github.com/llendway/github_for_collaboration/blob/master/github_for_collaboration.md). Then, do the following (if you get stuck on a step, don't worry, I will help! You can always get started on the homework and we can figure out the GitHub piece later):

* Create a repository on GitHub, giving it a nice name so you know it is for the 3rd weekly exercise assignment (follow the instructions in the document/video).  
* Copy the repo name so you can clone it to your computer. In R Studio, go to file --> New project --> Version control --> Git and follow the instructions from the document/video.  
* Download the code from this document and save it in the repository folder/project on your computer.  
* In R Studio, you should then see the .Rmd file in the upper right corner in the Git tab (along with the .Rproj file and probably .gitignore).  
* Check all the boxes of the files in the Git tab and choose commit.  
* In the commit window, write a commit message, something like "Initial upload" would be appropriate, and commit the files.  
* Either click the green up arrow in the commit window or close the commit window and click the green up arrow in the Git tab to push your changes to GitHub.  
* Refresh your GitHub page (online) and make sure the new documents have been pushed out.  
* Back in R Studio, knit the .Rmd file. When you do that, you should have two (as long as you didn't make any changes to the .Rmd file, in which case you might have three) files show up in the Git tab - an .html file and an .md file. The .md file is something we haven't seen before and is here because I included `keep_md: TRUE` in the YAML heading. The .md file is a markdown (NOT R Markdown) file that is an interim step to creating the html file. They are displayed fairly nicely in GitHub, so we want to keep it and look at it there. Click the boxes next to these two files, commit changes (remember to include a commit message), and push them (green up arrow).  
* As you work through your homework, save and commit often, push changes occasionally (maybe after you feel finished with an exercise?), and go check to see what the .md file looks like on GitHub.  
* If you have issues, let me know! This is new to many of you and may not be intuitive at first. But, I promise, you'll get the hang of it! 



## Instructions

* Put your name at the top of the document. 

* **For ALL graphs, you should include appropriate labels.** 

* Feel free to change the default theme, which I currently have set to `theme_minimal()`. 

* Use good coding practice. Read the short sections on good code with [pipes](https://style.tidyverse.org/pipes.html) and [ggplot2](https://style.tidyverse.org/ggplot2.html). **This is part of your grade!**

* When you are finished with ALL the exercises, uncomment the options at the top so your document looks nicer. Don't do it before then, or else you might miss some important warnings and messages.


## Warm-up exercises with garden data

These exercises will reiterate what you learned in the "Expanding the data wrangling toolkit" tutorial. If you haven't gone through the tutorial yet, you should do that first.

  1. Summarize the `garden_harvest` data to find the total harvest weight in pounds for each vegetable and day of week (HINT: use the `wday()` function from `lubridate`). Display the results so that the vegetables are rows but the days of the week are columns.


```r
totalGardenHarvest <- garden_harvest %>%
  mutate(wt_lbs = weight * 0.00220462) %>%
  mutate(weekday = wday(
    ymd(date),
    label = TRUE,
    abbr = TRUE,
    week_start = 7
  )) %>%
  group_by(vegetable, weekday) %>%
  summarise(weekday_wt_lbs = sum(wt_lbs)) %>%
  mutate(total_wt_lbs = sum(weekday_wt_lbs)) %>%
  pivot_wider(names_from = weekday, values_from = weekday_wt_lbs) %>%
  select("vegetable",
         "Mon",
         "Tue",
         "Wed",
         "Thu",
         "Fri",
         "Sat",
         "Sun",
         "total_wt_lbs") 

totalGardenHarvest
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["vegetable"],"name":[1],"type":["chr"],"align":["left"]},{"label":["Mon"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["Tue"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["Wed"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["Thu"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["Fri"],"name":[6],"type":["dbl"],"align":["right"]},{"label":["Sat"],"name":[7],"type":["dbl"],"align":["right"]},{"label":["Sun"],"name":[8],"type":["dbl"],"align":["right"]},{"label":["total_wt_lbs"],"name":[9],"type":["dbl"],"align":["right"]}],"data":[{"1":"apple","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA","7":"0.34392072","8":"NA","9":"0.34392072"},{"1":"asparagus","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA","7":"0.04409240","8":"NA","9":"0.04409240"},{"1":"basil","2":"0.0661386","3":"0.11023100","4":"NA","5":"0.02645544","6":"0.46737944","7":"0.41005932","8":"NA","9":"1.08026380"},{"1":"beans","2":"6.5080382","3":"4.38719380","4":"4.08295624","5":"3.39291018","6":"1.52559704","7":"4.70906832","8":"1.91361016","9":"26.51937398"},{"1":"beets","2":"0.6724091","3":"0.15873264","4":"0.18298346","5":"11.89172028","6":"0.02425082","7":"0.37919464","8":"0.32187452","9":"13.63116546"},{"1":"broccoli","2":"0.8201186","3":"NA","4":"0.70768302","5":"NA","6":"0.16534650","7":"NA","8":"1.25883802","9":"2.95198618"},{"1":"carrots","2":"0.8708249","3":"0.35273920","4":"5.56225626","5":"2.67420406","6":"2.13848140","7":"2.33028334","8":"2.93655384","9":"16.86534300"},{"1":"chives","2":"NA","3":"NA","4":"0.01763696","5":"NA","6":"NA","7":"NA","8":"NA","9":"0.01763696"},{"1":"cilantro","2":"NA","3":"0.00440924","4":"NA","5":"NA","6":"0.07275246","7":"0.03747854","8":"NA","9":"0.11464024"},{"1":"corn","2":"0.7583893","3":"0.72752460","4":"5.30211110","5":"NA","6":"3.44802568","7":"1.31615814","8":"1.45725382","9":"13.00946262"},{"1":"cucumbers","2":"4.7752069","3":"10.04645334","4":"5.30652034","5":"3.30693000","6":"7.42956940","7":"9.64080326","8":"3.10410496","9":"43.60958822"},{"1":"edamame","2":"NA","3":"1.40213832","4":"NA","5":"NA","6":"NA","7":"4.68922674","8":"NA","9":"6.09136506"},{"1":"hot peppers","2":"1.2588380","3":"0.14109568","4":"0.06834322","5":"NA","6":"NA","7":"NA","8":"NA","9":"1.46827692"},{"1":"jalape<U+00F1>o","2":"5.5534378","3":"0.54895038","4":"0.48060716","5":"0.22487124","6":"1.29411194","7":"1.50796008","8":"0.26234978","9":"9.87228836"},{"1":"kale","2":"2.0679336","3":"0.28219136","4":"0.61729360","5":"0.27998674","6":"0.38139926","7":"1.49032312","8":"0.82673250","9":"5.94586014"},{"1":"kohlrabi","2":"NA","3":"NA","4":"NA","5":"0.42108242","6":"NA","7":"NA","8":"NA","9":"0.42108242"},{"1":"lettuce","2":"2.4581513","3":"0.91712192","4":"1.18608556","5":"2.45153744","6":"1.80117454","7":"1.31615814","8":"1.46607230","9":"11.59630120"},{"1":"onions","2":"0.5092672","3":"0.70768302","4":"NA","5":"0.60186126","6":"0.07275246","7":"1.91361016","8":"0.26014516","9":"4.06531928"},{"1":"peas","2":"4.6341112","3":"2.06793356","4":"1.08026380","5":"3.39731942","6":"0.93696350","7":"2.85277828","8":"2.05691046","9":"17.02628026"},{"1":"peppers","2":"2.5264945","3":"1.44402610","4":"2.44271896","5":"0.70988764","6":"0.33510224","7":"1.38229674","8":"0.50265336","9":"9.34317956"},{"1":"potatoes","2":"0.9700328","3":"NA","4":"4.57017726","5":"11.85203712","6":"3.74124014","7":"2.80207202","8":"NA","9":"23.93555934"},{"1":"pumpkins","2":"30.1195184","3":"31.85675900","4":"NA","5":"NA","6":"NA","7":"92.68883866","8":"NA","9":"154.66511610"},{"1":"radish","2":"0.1962112","3":"0.09479866","4":"NA","5":"0.14770954","6":"0.19400656","7":"0.23148510","8":"0.08157094","9":"0.94578198"},{"1":"raspberries","2":"0.1300726","3":"0.33510224","4":"NA","5":"0.28880522","6":"0.57099658","7":"0.53351804","8":"NA","9":"1.85849466"},{"1":"rutabaga","2":"NA","3":"NA","4":"NA","5":"NA","6":"3.57809826","7":"6.89825598","8":"19.26396956","9":"29.74032380"},{"1":"spinach","2":"0.1477095","3":"0.49603950","4":"0.21384814","5":"0.23368972","6":"0.19621118","7":"0.26014516","8":"0.48722102","9":"2.03486426"},{"1":"squash","2":"24.3345956","3":"18.46810174","4":"NA","5":"NA","6":"NA","7":"56.22221924","8":"NA","9":"99.02491654"},{"1":"strawberries","2":"0.4784025","3":"NA","4":"NA","5":"0.08818480","6":"0.48722102","7":"0.16975574","8":"0.08157094","9":"1.30513504"},{"1":"Swiss chard","2":"1.0736499","3":"0.07054784","4":"0.90830344","5":"2.23107544","6":"0.61729360","7":"0.73413846","8":"1.24781492","9":"6.88282364"},{"1":"tomatoes","2":"11.4926841","3":"48.75076206","4":"58.26590198","5":"34.51773534","6":"85.07628580","7":"35.12621046","8":"75.60964752","9":"348.83922722"},{"1":"zucchini","2":"12.1959578","3":"16.46851140","4":"2.04147812","5":"34.63017096","6":"18.72163304","7":"3.41495638","8":"12.23564100","9":"99.70834874"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

  2. Summarize the `garden_harvest` data to find the total harvest in pound for each vegetable variety and then try adding the plot from the `garden_planting` table. This will not turn out perfectly. What is the problem? How might you fix it?


```r
varGardenHarvest <- garden_harvest %>%
  mutate(wt_lbs = weight * 0.00220462) %>%
  group_by(vegetable, variety) %>%
  summarise(to_varwt_lbs = sum(wt_lbs)) %>%
  left_join(garden_planting,
            by = c("vegetable", "variety"))
```
>  Some vegetables, although the harvest date and weight are all the same, there are more than one different plots. This will be a problem because there will be a row for each plot. This will happen every time that vegetable variety is harvested. There were also vegetable varieties that were planted on multiple dates. This will lead to a similar problem. It is hard to decide how to fix the problem at this point since we haven’t figured out which plots the weights belong to. However, one possible way to fix it is to put all the plots into one row and sum up all the seeds planted in different plots into one number as well. For example ,

![Example](C:\Users\Owner\OneDrive\Documents\stat112\GitHub\exercise\stat112-exercise3\exercise3 2 EG.png)

  3. I would like to understand how much money I "saved" by gardening, for each vegetable type. Describe how I could use the `garden_harvest` and `garden_spending` datasets, along with data from somewhere like [this](https://products.wholefoodsmarket.com/search?sort=relevance&store=10542) to answer this question. You can answer this in words, referencing various join functions. You don't need R code but could provide some if it's helpful.
  
>
1.In garden_harvest, group_by(vegetable, variety) and then summarize the weight of them.(calculate the total harvest weight of each variety in each vegetable)
2.join the two dataset 
3.joining the data from the grocery store to get the corresponding price for the vegetable
4.compute the gross saving by times the grams with the price in the grocery shop of a variety of vegetable
5.use the gross saving to minus the corresponding spending of the variety to get the actually saving for each variety
6.sum the actually saving to get the overall saving

  4. Subset the data to tomatoes. Reorder the tomato varieties from smallest to largest first harvest date. Create a barplot of total harvest in pounds for each variety, in the new order.CHALLENGE: add the date near the end of the bar. (This is probably not a super useful graph because it's difficult to read. This is more an exercise in using some of the functions you just learned.)


```r
tGardenHarvest <- garden_harvest %>%
  filter(vegetable == "tomatoes") %>%
  mutate(wt_lbs = weight * 0.00220462) %>%
  group_by(variety) %>%
  summarise(total_weight = sum(weight),
            first_plant_date = min(date)) %>%
  arrange(ymd(first_plant_date)) 

tGardenHarvest %>% 
 ggplot(aes(x=total_weight,y = fct_reorder(variety,first_plant_date)))+
  labs(title = "Harvest In Pounds for Each Variety of Tomatoes",x=NULL, y="Variety") +
  geom_bar(color = "white",fill="deepskyblue4",stat = "identity")
```

![](03_exercises_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

  5. In the `garden_harvest` data, create two new variables: one that makes the varieties lowercase and another that finds the length of the variety name. Arrange the data by vegetable and length of variety name (smallest to largest), with one row for each vegetable variety. HINT: use `str_to_lower()`, `str_length()`, and `distinct()`.
  

```r
orderGardenHarvest<-garden_harvest %>% 
  mutate(low_case_var=str_to_lower(variety)) %>% 
  mutate(length_var=str_length(variety)) %>% 
  group_by(low_case_var) %>% 
  arrange(vegetable,length_var) %>% 
  distinct(vegetable,low_case_var, .keep_all = TRUE) %>% 
  select(vegetable,low_case_var,length_var )
```

  6. In the `garden_harvest` data, find all distinct vegetable varieties that have "er" or "ar" in their name. HINT: `str_detect()` with an "or" statement (use the | for "or") and `distinct()`.


```r
filGardenHarvest<-garden_harvest %>% 
  mutate(var_name=str_detect(variety,"er|ar")) %>% 
  filter(var_name==TRUE) %>% 
  distinct(vegetable, variety)
```


## Bicycle-Use Patterns

In this activity, you'll examine some factors that may influence the use of bicycles in a bike-renting program.  The data come from Washington, DC and cover the last quarter of 2014.

<center>

![A typical Capital Bikeshare station. This one is at Florida and California, next to Pleasant Pops.](https://www.macalester.edu/~dshuman1/data/112/bike_station.jpg){width="30%"}


![One of the vans used to redistribute bicycles to different stations.](https://www.macalester.edu/~dshuman1/data/112/bike_van.jpg){width="30%"}

</center>

Two data tables are available:

- `Trips` contains records of individual rentals
- `Stations` gives the locations of the bike rental stations

Here is the code to read in the data. We do this a little differently than usual, which is why it is included here rather than at the top of this file. To avoid repeatedly re-reading the files, start the data import chunk with `{r cache = TRUE}` rather than the usual `{r}`.


```r
data_site <- 
  "https://www.macalester.edu/~dshuman1/data/112/2014-Q4-Trips-History-Data-Small.rds" 
Trips <- readRDS(gzcon(url(data_site)))
Stations<-read_csv("http://www.macalester.edu/~dshuman1/data/112/DC-Stations.csv")
```

**NOTE:** The `Trips` data table is a random subset of 10,000 trips from the full quarterly data. Start with this small data table to develop your analysis commands. **When you have this working well, you should access the full data set of more than 600,000 events by removing `-Small` from the name of the `data_site`.**

### Temporal patterns

It's natural to expect that bikes are rented more at some times of day, some days of the week, some months of the year than others. The variable `sdate` gives the time (including the date) that the rental started. Make the following plots and interpret them:

  7. A density plot, which is a smoothed out histogram, of the events versus `sdate`. Use `geom_density()`.
  

```r
Trips %>%
  ggplot() +
  geom_density(
    aes(x = sdate),
    fill = "deepskyblue4",
    color = "#e9ecef",
    alpha = 0.9,
    adjust = 0.5
  ) +
  labs(title = "Distrubution of Rental Started Time(Date) For Events", x = NULL, y =
         NULL)
```

![](03_exercises_files/figure-html/unnamed-chunk-7-1.png)<!-- -->
> The density plot shows a multimodal right-skewed distribution of the rental started time of the events. It covers the events which the rental started time is between Oct and Jan. In addition since the graph is right-skewed, it seems that the more events prefer to start the rental time during Oct till Dec, instead of Dec till Jan.
  
  8. A density plot of the events versus time of day.  You can use `mutate()` with `lubridate`'s  `hour()` and `minute()` functions to extract the hour of the day and minute within the hour from `sdate`. Hint: A minute is 1/60 of an hour, so create a variable where 3:30 is 3.5 and 3:45 is 3.75.
  

```r
hMTrip <- Trips %>%
  mutate(times = hour(sdate) + minute(sdate) / 60)

hMTrip %>%
  ggplot() +
  geom_density(
    aes(x = times),
    fill = "deepskyblue4",
    color = "#e9ecef",
    alpha = 0.9,
    adjust = 0.5
  ) +
  labs(title = "Distrubution of Rental Started Time For Events", x = "Rental Started Time(Hour)", y =
         NULL)
```

![](03_exercises_files/figure-html/unnamed-chunk-8-1.png)<!-- -->
>  This density plot shows a multimodal slightly left-skewed distribution of started rental time for events in a day. Since there are only 24 hours in a day, it covers from 0:00 till 24:00. The graph shows that it is relatively rare for the events to start their rental time between 0:00 to 5：00.
  
  9. A bar graph of the events versus day of the week. Put day on the y-axis.
  

```r
wTrip<-Trips %>% 
   mutate(weekday = wday(
    ymd(sdate),
    label = TRUE,
    abbr = TRUE,
    week_start = 7
  ))
```
  
  10. Facet your graph from exercise 8. by day of the week. Is there a pattern?
  

  
The variable `client` describes whether the renter is a regular user (level `Registered`) or has not joined the bike-rental organization (`Causal`). The next set of exercises investigate whether these two different categories of users show different rental behavior and how `client` interacts with the patterns you found in the previous exercises. 

  11. Change the graph from exercise 10 to set the `fill` aesthetic for `geom_density()` to the `client` variable. You should also set `alpha = .5` for transparency and `color=NA` to suppress the outline of the density function.
  


  12. Change the previous graph by adding the argument `position = position_stack()` to `geom_density()`. In your opinion, is this better or worse in terms of telling a story? What are the advantages/disadvantages of each?
  

  
  13. In this graph, go back to using the regular density plot (without `position = position_stack()`). Add a new variable to the dataset called `weekend` which will be "weekend" if the day is Saturday or Sunday and  "weekday" otherwise (HINT: use the `ifelse()` function and the `wday()` function from `lubridate`). Then, update the graph from the previous problem by faceting on the new `weekend` variable. 
  

  
  14. Change the graph from the previous problem to facet on `client` and fill with `weekday`. What information does this graph tell you that the previous didn't? Is one graph better than the other?
  

  
### Spatial patterns

  15. Use the latitude and longitude variables in `Stations` to make a visualization of the total number of departures from each station in the `Trips` data. Use either color or size to show the variation in number of departures. We will improve this plot next week when we learn about maps!
  

  
  16. Only 14.4% of the trips in our data are carried out by casual users. Create a plot that shows which area(s) have stations with a much higher percentage of departures by casual users. What patterns do you notice? (Again, we'll improve this next week when we learn about maps).
  

  
**DID YOU REMEMBER TO GO BACK AND CHANGE THIS SET OF EXERCISES TO THE LARGER DATASET? IF NOT, DO THAT NOW.**

## Dogs!

In this section, we'll use the data from 2022-02-01 Tidy Tuesday. If you didn't use that data or need a little refresher on it, see the [website](https://github.com/rfordatascience/tidytuesday/blob/master/data/2022/2022-02-01/readme.md).

  17. The final product of this exercise will be a graph that has breed on the y-axis and the sum of the numeric ratings in the `breed_traits` dataset on the x-axis, with a dot for each rating. First, create a new dataset called `breed_traits_total` that has two variables -- `Breed` and `total_rating`. The `total_rating` variable is the sum of the numeric ratings in the `breed_traits` dataset (we'll use this dataset again in the next problem). Then, create the graph just described. Omit Breeds with a `total_rating` of 0 and order the Breeds from highest to lowest ranked. You may want to adjust the `fig.height` and `fig.width` arguments inside the code chunk options (eg. `{r, fig.height=8, fig.width=4}`) so you can see things more clearly - check this after you knit the file to assure it looks like what you expected.


```r
breed_traits_total<-breed_traits %>% 
 select(-c("Coat Type", "Coat Length")) %>% 
  pivot_longer(!Breed, names_to = "level", values_to = "rating") %>% 
  group_by(Breed) %>% 
  summarise(total_rating=sum(rating))

breed_traits_total %>%
  filter(total_rating != 0) %>%
  arrange(desc(total_rating)) %>%
  ggplot(aes(y = fct_reorder(Breed, total_rating), x = total_rating)) +
  geom_point() +
  labs(title = "Breeds Ranked BY Their Ratings", x = "Rating", y =
         "Breeds") +
  theme(
    plot.title = element_text(hjust = 0.1, face = "bold", size = 15),
    axis.text.y = element_text(
      face = "bold",
      hjust = 1,
      vjust = 1
    ),
    panel.spacing.x = unit(0.75, "cm")
  )
```

![](03_exercises_files/figure-html/unnamed-chunk-17-1.png)<!-- -->

  18. The final product of this exercise will be a graph with the top-20 dogs in total ratings (from previous problem) on the y-axis, year on the x-axis, and points colored by each breed's ranking for that year (from the `breed_rank_all` dataset). The points within each breed will be connected by a line, and the breeds should be arranged from the highest median rank to lowest median rank ("highest" is actually the smallest numer, eg. 1 = best). After you're finished, think of AT LEAST one thing you could you do to make this graph better. HINTS: 1. Start with the `breed_rank_all` dataset and pivot it so year is a variable. 2. Use the `separate()` function to get year alone, and there's an extra argument in that function that can make it numeric. 3. For both datasets used, you'll need to `str_squish()` Breed before joining. 
  

```r
newb_breed_traits_total <- breed_traits_total %>%
  mutate(breedR = str_squish(Breed)) %>%
  slice_max(n = 20, order_by = total_rating)

new_breed_rank_all <- breed_rank_all %>%
  pivot_longer(cols = `2013 Rank`:`2020 Rank`,
               names_to = "year",
               values_to = "rank") %>%
  separate(
    col = year,
    into = c("years", NULL),
    sep = " ",
    convert = TRUE
  ) %>%
  mutate(breedR = str_squish(Breed)) %>%
  inner_join(newb_breed_traits_total,
             by = "breedR")

new_breed_rank_all %>% 
  ggplot(aes(y= fct_rev(fct_reorder(breedR, rank, median)), x=years))+
  geom_point(aes(color=rank)) +
  geom_line()+
  labs(title = "Breeds Ranked By Years", x = NULL, y =
         "Breeds")
```

![](03_exercises_files/figure-html/unnamed-chunk-18-1.png)<!-- -->
> Assigned different sizes of dots for each rank . So that the rank could be more explicit and easier for the viewer to locate. For example, the largest dot for the highest rank, the second largest one for the second highest.

  19. Create your own! Requirements: use a `join` or `pivot` function (or both, if you'd like), a `str_XXX()` function, and a `fct_XXX()` function to create a graph using any of the dog datasets. One suggestion is to try to improve the graph you created for the Tidy Tuesday assignment. If you want an extra challenge, find a way to use the dog images in the `breed_rank_all` file - check out the `ggimage` library and [this resource](https://wilkelab.org/ggtext/) for putting images as labels.
  

  
## GitHub link

  20. Below, provide a link to your GitHub page with this set of Weekly Exercises. Specifically, if the name of the file is 03_exercises.Rmd, provide a link to the 03_exercises.md file, which is the one that will be most readable on GitHub.

## Challenge problem! 

This problem uses the data from the Tidy Tuesday competition this week, `kids`. If you need to refresh your memory on the data, read about it [here](https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-09-15/readme.md). 

  21. In this exercise, you are going to try to replicate the graph below, created by Georgios Karamanis. I'm sure you can find the exact code on GitHub somewhere, but **DON'T DO THAT!** You will only be graded for putting an effort into this problem. So, give it a try and see how far you can get without doing too much googling. HINT: use `facet_geo()`. The graphic won't load below since it came from a location on my computer. So, you'll have to reference the original html on the moodle page to see it.
  


**DID YOU REMEMBER TO UNCOMMENT THE OPTIONS AT THE TOP?**

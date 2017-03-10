# BundestagswahlProphet
# Predicting Election Results using facebook's new modelling package, prophet


With the upcoming election in Germany, uncertainty about election results is high.
This small project aims to make use of facebook's latest package, prophet ( https://facebookincubator.github.io/prophet/ )


### First Steps - Get the data and clean them

First, we need to collect data. Therefore, we jump to Wahlrecht.de (www.wahlrecht.de) and scrape the historic survey results from 
different survey institutes listed there. 
```
require(prophet)
require(rvest)
require(stringr)
url <- "http://www.wahlrecht.de/umfragen/politbarometer/stimmung.htm"


mv <- read_html(url)

tab <- html_table(mv, header = NA, trim = TRUE, fill = TRUE)

tab <- tab[[2]]
tab <- tab[4:nrow(tab),]
colnames(tab) <- c("date", "leer", "CDUCSU",  "SPD", "Gruene", "FDP","Linke" ,"AfD", "Rest","leer2", "N", "Frame")


#transform the relevant variable:

tab$CDUCSU <- str_split_fixed(tab$CDUCSU, " ", 2)[,1]
tab$SPD <- str_split_fixed(tab$SPD, " ", 2)[,1]
tab$AfD <- str_split_fixed(tab$AfD, " ", 2)[,1] 
``` 
### Next step - use prophet
```
require(lubridate)

df <- data.frame(tab$date, tab$AfD)
colnames(df) <- c("ds", "y")
df$ds <- dmy(as.character(df$ds))
df$y <- as.numeric(as.character(df$y))

m <- prophet(df)

future <- make_future_dataframe(m, periods = 10, freq = "month")
```
![alt tag](https://github.com/heck1/BundestagswahlProphet/blob/master/Rplot.png?raw=true
)
As we can see, the AfD is projected to score around 12-15% in the upcoming elections...

```
forecast <- predict(m, future)
plot(m, forecast)
prophet_plot_components(m, forecast)
```

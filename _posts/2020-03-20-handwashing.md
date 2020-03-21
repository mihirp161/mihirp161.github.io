---
title: "Recognizing Dr. Ignaz Semmelweis & Handwashing"
date: 2020-03-20
tags: [Miscellaneous, R]
header:
excerpt: "Investigative Analysis, Web-scraping, ggplot2"
mathjax: "true"
---
Oh, hello there! I hope you're having a lovely day in your confines of your viscinty (thanks to the COVID-19), and maintaing social distacing. Furthermore, please also wash you hands for 20 seconds and don't touch your face! Handwashing is more effective and is recommended than applying a hand sanitizer on your hands. Anyways, have you seen the new Google Doodle yet? Today's doodle looks like this:     

<p align="center"> 
   <img src="{{ site.url }}{{ site.baseurl }}/images/handwashing/doodle.png" alt="">
</p>

You might be wondering, "So who is this person wearing two ties?" Well, he is Dr. Ignaz Semmelweis, and history blesses him as the "father of the handwashing movement."      

Dr. Ignaz Semmelweis worked at the Vienna General Hospital's maternity clinic on a 3-year contract from 1846–1849. There, as elsewhere in European and North American hospitals, puerperal fever, or childbed fever, was rampant, sometimes climbing to 40 percent of admitted patients. He was disturbed by these mortality rates, and eventually developed a theory of infection, in which he theorized that decaying matter on the hands of doctors, who had recently conducted autopsies, was brought into contact with the genitals of birthgiving women during the medical examinations at the maternity clinic. He proposed a radical hand washing system using chlorinated lime, now a known disinfectant.     

At the time however, the germ theory of infection had not been developed and Semmelweis' ideas ran contrary to key medical beliefs and practices. His ideas were rejected and ridiculed. Quite unusually, his contract was not renewed, effectively expelling him in 1849 from the medical community in Vienna. He died as an outcast in a mental institution.    

Please note that, dataset which I directly scapred from the wikipedia page havevarious inconsistencies. Inconsistencies exist for instance in reported yearly rates, and monthly rates (if aggregated to yearly basis). One of the causes may be that Dr. Semmelweis used different sources. He points out several times that actual mortality rates were higher than reported ones, because during childbed fever epidemics, the maternity ward was overwhelmed with dying women, who were then transferred to the general hospital, and therefore not registered at the maternity ward, when dying. Some women were also released from the maternity ward, either healthy or not so healthy, only to be readmitted to the general hospital when symptoms appeared or worsened.    

There were two maternity clinics at the Vienna General Hospital. Semmelweis is not always specific, if the numbers are for both clinics, or for his own clinic only. The graph presented below are exactly as reported in (the 1983 translation by Carter of) Semmelweis' 1861 publication and focuses on one clicninc where he is to primarliy work.

Let me begin with how I scraped my dataset from the wikipage. It will also include how I organized and formatted the data. For these please see the code chunk below:

````r
webpage <- xml2::read_html("https://en.wikipedia.org/wiki/Historical_mortality_rates_of_puerperal_fever#Monthly_mortality_rates_for_birthgiving_women_1841%E2%80%931849")

#get all the classes with tables
tables <- rvest::html_nodes(webpage, "table")

#get the first table of Vienna Hospital where Handwashing was started. This  table is from the Dr. Semmelweis's primary clinic
vienna_hospital <- rvest::html_table(tables[grep("first clinic at the Vienna General Hospital 1841–1849",
                          tables,ignore.case = T)],fill = T)[[1]]

#clean the data by removing empty columns and chaning na in one row
vienna_hospital %<>% dplyr::select(-c(1,6)) %>% filter(!grepl('na', Births))

#change datastypes of everything but the "months"
vienna_hospital %<>% dplyr::mutate_at(vars(Births, Deaths,`Rate (%)`), dplyr::funs(as.numeric))

#changing to date is pretty tricky because we have both %B and %b
a <- readr::parse_date(vienna_hospital$Month, "%b %Y") # Produces NA when format is not "%B %Y"
b <- readr::parse_date(vienna_hospital$Month, "%B %Y") # Produces NA when format is not "%d.%m.%Y"

a[is.na(a)] <- b[!is.na(b)] # Combine both while keeping their ranks, ignore the warning

vienna_hospital$Month <- lubridate::ymd(a) # Put it back in your dataframe

colnames(vienna_hospital) <- c("Date", "Births", "Deaths", "Rate(%)")
`````

So that gets us the dataset into a nice dataframe. which looks like this:

<p align="center"> 
   <img src="{{ site.url }}{{ site.baseurl }}/images/handwashing/dataset_snippet.png" alt="">
</p>

No, inorder to visualize it, I have used `ggplot2` but since we are working with a history, I gave the graph an old-school theme which I imported from `ggpomological` package. Following code chunk draws the graph from a cleaned dataset:

````r
#Apparently, from this date handwashing was made mandatory according to the Wikipedia Page
mandatory_handwashing_date = as.Date('1847-06-01')

#Added a TRUE/FALSE column to monthly called handwashing_started
vienna_hospital <- vienna_hospital%>% mutate(handwashing_started = (Date >= mandatory_handwashing_date))

#plot
line_plot <- ggplot2::ggplot(vienna_hospital, aes(x= Date, y = `Rate(%)`/100, color = handwashing_started))+
            geom_line(show.legend = F, size= 1) +
            labs(x = 'Year', y = '% monthly deaths', title = "Monthly deaths following the mandatory handwashing",
                 subtitle = "at the Vienna Hospital for the years 1841 to 1849",
                 caption = "*arrowhead & annotation were added with paint") +
            scale_y_continuous(label = scales::percent) +
            scale_color_pomological()+ theme(plot.caption = element_text(size=8, face="italic", color="lightpink2"))


#Apply the theme & save
ggpomological::paint_pomological(
                  line_plot + theme_pomological("Harrington", 16),
                  res = 110)%>% 
magick::image_write("handwashing_graph.png")

`````
 This produces the image where we see how mortality rates of puerperal fever drops after handwashing became mandaroty in the Veienna Hospital in June of 1847:
 
 <p align="center"> 
   <img src="{{ site.url }}{{ site.baseurl }}/images/handwashing/handwashing_graph.png" alt="">
</p>

If you would like to see my **source code**, you can find it [here](https://github.com/opendatasurgeon/HandwashingAnalysis_r)!    
That's all for this post, and if you want to learn more about Dr. Ignaz Semmelweis, go [here](https://en.wikipedia.org/wiki/Historical_mortality_rates_of_puerperal_fever#Monthly_mortality_rates_for_birthgiving_women_1841%E2%80%931849). And if you find the Google doodle intresting, you can go [here](https://www.google.com/doodles/recognizing-ignaz-semmelweis-and-handwashing) to play with it. 

Please take care of yourself and your family! Maintain the social distancing for awhile. We can play an important role and our contribution will allow hospitals to focus on the ills and those who are in danger. [#flattenthecurve](https://twitter.com/hashtag/flattenthecurve?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Ehashtag) & STOP BUYING BLODDY TOILET PAPERS!!

**NOTE**: Thank you very much for reading! If you discover any mistakes or want to offer any feedback, please feel free to email me.
{: .notice--success}

---
layout: post
title: "Visualizing Sleep Deprivation in the US"
tags: Visualization Spatial
---

## Overview

Approximately 1/3 of the population of the United States reports having less sleep on average than the recommended 7 hours a night (CDC, 2011; Liu, 2016). Chronic sleep deprivation is considered a serious public health issue, as insufficient sleep is associated with increased workplace related accidents (Dinges, 1995; Rosekind et al., 2010), obesity (Gangwisch, Malaspina, Boden-Albala, & Heymsfield, 2004; Knutson, Spiegel, Penev, & Van Cauter, 2004), drowsy driving (J. Horne & Louise, 1995; Howard et al., 2004), cardiovascular disease (Ayas et al., 2003; Mullington, Hacch, Toth, Serrador, & Meier-Ewert, 2009), and a variety of other risks/conditions. Sleep deprivation is also correlated with decreased decision making (Harrison & Horne, 2000) and sustained attention (Lim & Dinges, 2000), which may provide one explanation for some of the associated behavioral detriments.

There have been sizable efforts on the part of the Centers for Disease Control (CDC) and National Sleep Foundation (NSF) to better understand the prevalance and costs of sleep deprivation through large-scale surveys across the United States. These surveys provide critical information for future policy and planning targeted interventions, and have led to some preliminary suggestions including employers tailoring shift-systems design (CDC, 2012), greater involvement by health-care providers (CDC, 2009), limiting active technology use before sleep (Gradisar et al., 2013), and general increased public awareness of sleep deprivation.

This post shows a few visualizations of sleep deprivation across two years and metrics, and briefly discusses some of the implications.

## Data

In this post we will use the Behavioral Risk Factor Surveillance System (BRFSS) results collected by the CDC. The BRFSS is a yearly survey intended to measure health related behaviors 1and demographics such as tobacco use, seatbelt use, sleep related habits, etc. In this post we use the 2009 BRFSS for a country wide analysis of the number of days in a month that people are sleep deprived, and the 2016 BRFSS for the average number of hours slept in a day. The 2009 BRFSS was collected through landlines only, with cellphones being added in 2011 to the survey sample. There are some large differences between the way in which data was collected for the two surveys, the specific sleep related questions asked, and the granularity of the sample involved (2009 included county information, 2016 did not), so one should be cautious in making direct comparisons. The sleep related questions that will be used are presented in their respective sections.

## BRFSS 2009: County Level

- "During the past 30 days, for about how many days have you felt you did not get enough rest or sleep?"
    - Response Options:
        - 0-30 days
        - Refuse to respond
        - Don't know
        - Missing / Not asked

This question was dichotomized into sleep deprived or not sleep deprived based on if the individual had less than 14 days of poor sleep reported. This cutoff was chosen based on its use in previous research (Grandner et al., 2015; Strine & Chapman, 2015) and the practical significance of it found in Edinger et al. (2011). Dichotomizing the variable
provides an easier interpretation, and helps circumspect the issue of nonlinearity between hours of sleep and impact on behavior. The cost of dichitomization is a loss of information through simplifying the continuous measure, resulting in a lower statistical power, and the selection of the cutoff point is often arbitrary and can impact the final results. Comparing the analysis across a range of cutoff points can help illuminate whether the final result is sensitive to modest changes in the cutoff points, providing context to the reader on how confident to be in the interpretation of the results.

The high granulaity of a county level perspective involves segmenting the data across approximately 2200 groupings; this results in many low sample size and completely missing counties. We see that this is particularly an issue in the central regions of the US. This is important to consider when interpreting the sleep deprivation estimates, as lower sample size regions will be more volatile with a larger standard error compared to larger sample size regions.

<img src="/images/sleepCDC/county_miss_plot.png" width="100%" style="padding:0px"/>

The proportion of sleep deprivation appears to have some regional variations, with a larger rate in the Appalachian region, as would be expected. In general there appears to be small differences across the different counties excluding a few extreme observations, most of which likely were a result of low sample size.

<img src="/images/sleepCDC/county_cont_plot.png" width="100%" style="padding:0px"/>

When visualizing continuous data across a large amount of space, such as a county level analysis of the United States, it can often become challenging to see differences between regions due to the density of the space and range of the data. Under these circumstances, quintiles are often used to break the data into separate equally sized ordered bins in order to make patterns more visible. The cost of using quintiles is that it comes at a loss of information and interpretability. As with dichotomization, the ranges and distribution of data within the bins disappears, and the bins can make differences look larger than they are if the viewer isn’t attentive to the range for each bin.

<img src="/images/sleepCDC/county_quint_plot.png" width="100%" style="padding:0px"/>

## 2016 BRFSS: State Level

- "On average, how many hours of sleep do you get in a 24-hour period?"
    - Response Options:
        - 1-24
        - Don't know / Not sure
        - Refused
        - Missing

This question is aiming to ask the same question as the previous one, however, as it is asking for an average it is easier for the participant to produce compared to remembering X days in the last month. A number of studies have reported that a minimum of 7 hours of sleep a night is needed, with a larger number required for younger individuals. The proportion sleep deprived was created by classifying people with less than 7 hours on average as chronically sleep deprived, and others as not sleep deprived.

There is a spike in the sample size for florida and maine, but all states have a large sample size. The proportion of individuals asked to participate that responded appears to be approximately the same across regions as well, with a maximum difference of ~2%

<img src="/images/sleepCDC/state_freq_plot.png" width="49%" style="padding:0px"/>
<img src="/images/sleepCDC/state_resp_plot.png" width="49%" style="padding:0px"/>

The Appalachian region appears to have increased sleep deprivation compared to the rest of the US, with another hotspot in California/Nevada. The central US appears well rested comparatively, though the rates of sleep deprivation are startlingly high across the country.

<img src="/images/sleepCDC/state_cont_plot.png" width="49%" style="padding:0px"/>
<img src="/images/sleepCDC/state_dichot_plot.png" width="49%" style="padding:0px"/>

## Discussion

The 2009 survey showed that there was local variation on the county-level that would not be accurately captured in state-level aggregation. While these differences may have appeared small, it is important to consider the general spread of this data was notably smaller than the 2016, and that with the different survey question, sampling characteristics, and year, those county level variations may be more prominent. Both surveys displayed hotspots in the Appalachian region, with the 2016 survey showing an additional hotspot around California/Nevada.

Given the prevalence of sleep deprivation across the United State and the many negative health costs associated with it, it is critical to gain a better understanding of why it occurs and how it can be prevented on a societal level.  

## Code

Everything was done in R.

### Extracting

I made this focusing on the 2009 data for the purpose of a personal project, and cannot be held accountable if it doesn't work on the other years (though it is intended to). Under professional conditions I would have written unit tests to ensure it is behaving properly.

```r
# Only extracts 1 variable
# Latlong: If you want the latitude and longitude coordinates
# Reformat: Recode extraction variables BEFORE applying the function
# Function: Whatever you want to apply by the relevant grouping
BRFSS_geoextraction <- function(filepath, id = "county", position,
                                extraction, contiguous=TRUE,latlong=TRUE,
                                reformat = function(x) {x}, # Recode as NA for these values in extract
                                func = function(x) {mean(x, na.rm=T)}) {
    # Error Catching
    if(!grepl("state", tolower(position[1]))){stop("State must be first position.")}
    if(!is.character(filepath)) {stop("Filepath must be character")}
    if(!is.character(position)) {stop("Position must be character")}
    if(!is.character(extraction)) {stop("Extraction variable must be character name.")}
    if(length(extraction)!=1) {stop("Function currently only accepts one extracted var.")}
    if(!id %in% c("county", "state")) {stop("ID must be county or state.")}
    if(!is.function(func)){stop("Func must be a function.")}
    if(!is.function(reformat)) {stop("Reformat must be a function.")}
    if(id == "county" & length(position) != 2) {
        stop("County level requires position to have State and County varialbe names. ")
    }
    # Setup
    require(SASxport)
    require(dplyr)
    require(maps)
    require(stringr)
    require(ggplot2)
    strsplit2 <- function(x, char, index){
        unlist(strsplit(x, char))[index]
    }
    clear_labels <- function(x) {
        if(is.list(x)) {
            for(i in 1 : length(x)) class(x[[i]]) <- setdiff(class(x[[i]]), 'labelled')
            for(i in 1 : length(x)) attr(x[[i]],"label") <- NULL
        }
        else {
            class(x) <- setdiff(class(x), "labelled")
            attr(x, "label") <- NULL
        }
        return(x)
    }
    # Read Data
    data <- read.xport(filepath) %>%
        clear_labels(.)
    # Subset
    keep <- c(position, extraction)
    remove.states <- if(contiguous) {
        c(15, 2, 66, 72, 78)
    } else {c()}
    # Remove Excess
    data2 <- data %>%
        select(one_of(keep)) %>% # Select columns of interest
        filter(!(!!rlang::sym(position[1])) %in% remove.states) %>% # Select States
        filter(!(is.na(!!rlang::sym(position[1])))) # remove missing states
    if(id == "county"){
        data2 <- data2 %>%
            filter(!(!!rlang::sym(position[2])) %in% c(777,999)) %>%
            filter(!(is.na(!!rlang::sym(position[2])))) %>%
            mutate(FIPS = as.numeric(paste0(
                !!rlang::sym(position[1]), str_pad(!!rlang::sym(position[2]),3, pad = "0"), sep = "")
            ))
    } else {
        data2 <- data2 %>%
            mutate(FIPS = !!rlang::sym(position[1]))
    }
    # Get FIPS Codes
    if(id == "county") {
        data(county.fips)
        county.fips$polyname <- sapply(county.fips$polyname, function(x) {
            unlist(strsplit(x, ":"))[1]
        })
        county.fips <- unique(county.fips)
        fips.codes <- county.fips
    } else {
        data(state.fips)
        state.fips$polyname <- sapply(state.fips$polyname, function(x) {
            unlist(strsplit(x, ":"))[1]
        })
        state.fips <- unique(state.fips)
        fips.codes <- as.data.frame(state.fips)
    }
    varname <- paste0(extraction, ".f", sep = "")
    # Merge By FIPS
    data3 <- data2 %>%
        left_join(fips.codes, by = c("FIPS" = "fips")) %>% # Get location string
        mutate(temp = reformat(!!rlang::sym(extraction))) %>%
        group_by(polyname,FIPS) %>% # State/County-wise operations
        summarize(!!varname := func(temp), # Proportion of sleep deficiency
                  n = sum(!is.na(temp)), # Frequency of response
                  prop.responded = sum(!is.na(temp))/n()) %>% # Proportion of Responses
        filter(!is.na(!!rlang::sym(varname))) %>% # Remove Missing
        mutate(state = strsplit2(polyname, ",", 1)) # Extract state name
    if(id == "county") {
        data3$county <- strsplit2(data3$polyname, ",",2)
    }
    # Merge for coordinates
    if(id == "county"){
        map <- map_data('county') %>%
            mutate(polyname = paste0(region, ",", subregion, sep = "")) %>%
            select(-c(group, order, region, subregion))
    } else {
        map <- map_data('state') %>%
            mutate(polyname = region) %>%
            select(-c(group, order, region, subregion))
    }
    if(latlong) {data3 <- left_join(data3, map, by = "polyname")}
    as.data.frame(data3)
}
```

Example of its use.

```r
filepath <- "data/raw/CDBRFS09.XPT"
position <- c("X.STATE", "CTYCODE")
extraction <- c("QLREST2")
QLREST2_format <- function(x) {
    x[x==88] <- 0
    x[x>30] <- NA
    x <- ifelse(x>14, 1, 0)
    x
}
sleep_2009_county <- BRFSS_geoextraction(filepath = filepath,
                            position = position,
                            extraction = extraction,
                            id = "county",
                            reformat = QLREST2_format)
```

### General Plotting

```r
# Theme
theme_map <- theme_bw() +
    theme(axis.text = element_blank(),
          axis.line = element_blank(),
          axis.ticks = element_blank(),
          panel.border = element_blank(),
          panel.grid = element_blank(),
          axis.title = element_blank(),
          legend.position = c(.95, .2),
          legend.title = element_blank(),
          legend.text = element_text(size = 8),
          text = element_text(family = "Georgia", size = 12),
          axis.title = element_blank(),
          axis.ticks.length = unit(0,"pt"),
          plot.margin = unit(c(0.1,0,0.1,0.1), "lines"))
# Country level plots
ggplot(sleep_2009, aes(x=long, y=lat, group=polyname, fill=prop)) +
    geom_polygon(color = "grey44", size = .4)+
    geom_polygon(data = state.df, aes(x=long, y = lat, group = group, fill = NA),
                 color = "black", size = .6, fill = NA)+
    scale_fill_distiller(palette = "Spectral") +
    coord_map("albers", lat0=39, lat1=45,
               xlim = c(-120, -73),
               ylim = c(25.12993,49.38323)) +
    ggtitle("Continuous Proportion of Sleep Deprivation") +
    theme_map
# State level plots
ggplot() +
    geom_map(data=us_map, map=us_map,
             aes(long, lat, map_id=region),
             color="#b2b2b277", size=0.1, fill=NA) +
    geom_map(data=sleep_2016_cont, map=us_map,
             aes(fill=n, map_id=polyname),
             color="#b2b2b277", size=0.4) +
    ggtitle("Sample Size") +
    scale_fill_distiller(name="Prevalence", palette="BrBG",
                         breaks=c(10000,20000,30000),
                         labels=c("10k","20k","30k")) +
    coord_map("polyconic") +
    scale_x_continuous(limits = c(-118,-75)) +
    scale_y_continuous(limits = c(25.5,49.38),expand = c(0,0)) +
    theme(axis.title = element_blank())+
    theme_map
```

## References

- Ayas, N., White, D., Manson, J., Stampfer, M., Speizer, F., Malhotra, A., & Hu, F. (2003). *Archives of internal medicine. Progress in Cardiovascular Diseases*, 2 (163), 205–2009.
- CDC. (2009). Perceived insufficient rest or sleep among adults - united states, 2008. *Morbidity and Mortality Weekly Report*, 42 (58), 1175–1179.
- CDC. (2011). Effect of short sleep duration on daily activities - united states, 2005-2008. *Morbidity and Mortality Weekly Report*, 8 (60), 239.
- CDC. (2012). Short sleep duration among workers - united states, 2010. *Morbidity and Mortality Weekly Report*, 16 (61), 281–285.
- Dinges, D. (1995). An overview of sleepiness and accidents. *Journal of Sleep Research*, 14 (4), 4–14.
- Edinger, J., Wyatt, J., Stepanski, E., Olsen, M., Stechuchak, K., Carney, C., . . . Krystal, A. (2011). Testing the reliability and validity of dsm-iv-tr and icsd-2 insomnia diagnoses: Results of a multitrait-multimethod analysis. *Arch Gen Psychiatry*, 10 (68), 992–1002.
- Gangwisch, J., Malaspina, D., Boden-Albala, B., & Heymsfield, S. (2004). Inadequate sleep as a risk factor for obesity; analysis of the nhanes 1. *Sleep*, 10 (28), 1289–1296.
- Gradisar, M., Wolfson, A., Harvey, A., Hlae, L., Rosenberg, R., & Czeisier, C. (2013). The sleep and technology use of americans: Findings from the national sleep foundation’s 2011 sleep in america poll. *Journal of Clinical Sleep Medicine*, 12 (9), 1291–1299.
- Grandner, M., Smith, M., Jackson, N., Jackson, T., Burgard, S., & Branas, C. (2015). Geographic distribution of unsufficient sleep across the united state: A county-level hotspot analysis. *Sleep Health*, 3 (1), 158–165.
- Harrison, Y., & Horne, J. (2000). The impact of sleep deprivatino on decision making: A review. *Journal of Experimental Psychology: Applied*, 6 (3), 236–249.
- Horne, J., & Louise, R. (1995). Sleep related vehicle accidents. *British Medical Journal*, 6979 (310), 565–567.
- Howard, M., Desai, A., Grunstein, R., Hukins, C., Armstrong, J., Joffe, D., . . . Pierce, R. (2004).
- Sleepiness, sleep-disordered breathing, and accident risk factors in commercial vehicle drivers. *American Journal of Respiratory and Critical Care Medicine*, 9 (170), 1014–1021. Knutson, K., Spiegel, K., Penev, P., & Van Cauter, E. (2004). The metabolic consequences of sleep deprivation. *Sleep Medicine Reviews*, 3 (11), 163–178.
- Lim, J., & Dinges, D. (2000). Sleep deprivation and vigilant attention. *Annals of the New York Academy of Sciences*, 1 (1129), 305–322.
- Liu, Y. (2016). Prevalence of healthy sleep duration among adults- united states, 2014. *Morbidity and Mortality Weekly Report*, (65), 1025–1033.
- Mullington, J., Hacch, M., Toth, M., Serrador, J., & Meier-Ewert, H. (2009). Progress in cardiovascular diseases. *Progress in Cardiovascular Diseases*, 4 (51), 294–302.
- Rosekind, M., Gregoy, K., Mallis, Melissa, Brandt, S., Seal, B., & Lerner, D. (2010). An overview of sleepiness and accidents. *American College of Occupational and Environmental Medicine*, 52 (1), 1091–98.
- Strine, T., & Chapman, D. (2015). Associations of frequent sleep insufficiency with health-related quality of life and health behaviors. *Sleep Medicine*, 1 (6), 23–27.

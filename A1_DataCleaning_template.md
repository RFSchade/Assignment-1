Assignment 1, Language development in Autism Spectrum Disorder (ASD) - Brushing up your code skills
===================================================================================================

In this first part of the assignment we will brush up your programming skills, and make you familiar with the data sets you will be analysing for the next parts of the assignment.

In this warm-up assignment you will: 1) Create a Github (or gitlab) account, link it to your RStudio, and create a new repository/project 2) Use small nifty lines of code to transform several data sets into just one. The final data set will contain only the variables that are needed for the analysis in the next parts of the assignment 3) Warm up your tidyverse skills (especially the sub-packages stringr and dplyr), which you will find handy for later assignments.

N.B: Usually you'll also have to doc/pdf with a text. Not for Assignment 1.

Learning objectives:
--------------------

-   Become comfortable with tidyverse (and R in general)
-   Test out the git integration with RStudio
-   Build expertise in data wrangling (which will be used in future assignments)

0. First an introduction on the data
------------------------------------

Language development in Autism Spectrum Disorder (ASD)
======================================================

Reference to the study: <https://www.ncbi.nlm.nih.gov/pubmed/30396129>

Background: Autism Spectrum Disorder (ASD) is often related to language impairment, and language impairment strongly affects the patients ability to function socially (maintaining a social network, thriving at work, etc.). It is therefore crucial to understand how language abilities develop in children with ASD, and which factors affect them (to figure out e.g. how a child will develop in the future and whether there is a need for language therapy). However, language impairment is always quantified by relying on the parent, teacher or clinician subjective judgment of the child, and measured very sparcely (e.g. at 3 years of age and again at 6).

In this study we videotaped circa 30 kids with ASD and circa 30 comparison kids (matched by linguistic performance at visit 1) for ca. 30 minutes of naturalistic interactions with a parent. We repeated the data collection 6 times per kid, with 4 months between each visit. We transcribed the data and counted: i) the amount of words that each kid uses in each video. Same for the parent. ii) the amount of unique words that each kid uses in each video. Same for the parent. iii) the amount of morphemes per utterance (Mean Length of Utterance) displayed by each child in each video. Same for the parent.

Different researchers involved in the project provide you with different datasets: 1) demographic and clinical data about the children (recorded by a clinical psychologist) 2) length of utterance data (calculated by a linguist) 3) amount of unique and total words used (calculated by a fumbling jack-of-all-trade, let's call him RF)

Your job in this assignment is to double check the data and make sure that it is ready for the analysis proper (Assignment 2), in which we will try to understand how the children's language develops as they grow as a function of cognitive and social factors and which are the "cues" suggesting a likely future language impairment.

1. Let's get started on GitHub
------------------------------

In the assignments you will be asked to upload your code on Github and the GitHub repositories will be part of the portfolio, therefore all students must make an account and link it to their RStudio (you'll thank us later for this!).

Follow the link to one of the tutorials indicated in the syllabus: \* Recommended: <https://happygitwithr.com/> \* Alternative (if the previous doesn't work): <https://support.rstudio.com/hc/en-us/articles/200532077-Version-Control-with-Git-and-SVN> \* Alternative (if the previous doesn't work): <https://docs.google.com/document/d/1WvApy4ayQcZaLRpD6bvAqhWncUaPmmRimT016-PrLBk/mobilebasic>

N.B. Create a GitHub repository for the Assignment 1 and link it to a project on your RStudio.

2. Now let's take dirty dirty data sets and make them into a tidy one
---------------------------------------------------------------------

If you're not in a project in Rstudio, make sure to set your working directory here. If you created an RStudio project, then your working directory (the directory with your data and code for these assignments) is the project directory.

``` r
pacman::p_load(tidyverse,janitor, plyr)
```

Load the three data sets, after downloading them from dropbox and saving them in your working directory: \* Demographic data for the participants: <https://www.dropbox.com/s/w15pou9wstgc8fe/demo_train.csv?dl=0> \* Length of utterance data: <https://www.dropbox.com/s/usyauqm37a76of6/LU_train.csv?dl=0> \* Word data: <https://www.dropbox.com/s/8ng1civpl2aux58/token_train.csv?dl=0>

``` r
# Loading the three files:
demog <- read_csv("demo_train.csv") # Demographic data 
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_integer(),
    ##   Child.ID = col_character(),
    ##   Ethnicity = col_character(),
    ##   Diagnosis = col_character(),
    ##   Birthdate = col_character(),
    ##   Age = col_double(),
    ##   Age2 = col_double(),
    ##   CARS = col_double()
    ## )

    ## See spec(...) for full column specifications.

``` r
length_u <- read_csv("LU_train.csv") # Length of utterance data
```

    ## Parsed with column specification:
    ## cols(
    ##   SUBJ = col_character(),
    ##   VISIT = col_character(),
    ##   MOT_MLU = col_double(),
    ##   MOT_LUstd = col_double(),
    ##   MOT_LU_q1 = col_double(),
    ##   MOT_LU_q2 = col_double(),
    ##   MOT_LU_q3 = col_double(),
    ##   CHI_MLU = col_double(),
    ##   CHI_LUstd = col_double(),
    ##   CHI_LU_q1 = col_double(),
    ##   CHI_LU_q2 = col_double(),
    ##   CHI_LU_q3 = col_double()
    ## )

``` r
word_d <- read_csv("token_train.csv") # Word data 
```

    ## Parsed with column specification:
    ## cols(
    ##   SUBJ = col_character(),
    ##   VISIT = col_character(),
    ##   types_MOT = col_integer(),
    ##   types_CHI = col_integer(),
    ##   types_shared = col_integer(),
    ##   tokens_MOT = col_integer(),
    ##   tokens_CHI = col_integer(),
    ##   X = col_character()
    ## )

``` r
#-----> Exploring data <-----#
# Looking at the head of each dataset
head(demog)
```

    ## # A tibble: 6 x 36
    ##   Child.ID Visit Ethnicity Diagnosis ASD_check  ASD2 Gender Birthdate   Age
    ##   <chr>    <int> <chr>     <chr>         <int> <int>  <int> <chr>     <dbl>
    ## 1 A.A.         1 White     B                 0     0      1 18/02/09   18.1
    ## 2 A.D.         1 White     B                 0     0      1 20/12/04   19.8
    ## 3 A.D.         2 White     B                 0     0      1 20/12/04   23.9
    ## 4 A.D.         3 White     B                 0     0      1 20/12/04   27.7
    ## 5 A.D.         4 White     B                 0     0      1 20/12/04   32.9
    ## 6 A.D.         5 White     B                 0     0      1 20/12/04   35.9
    ## # ... with 27 more variables: Total..Understands...Says. <int>,
    ## #   Total..Understands. <int>, Total.of.Both <int>, Age2 <dbl>,
    ## #   ADOS <int>, CARS <dbl>, CDI1 <int>, VinelandStandardScore <int>,
    ## #   VinelandReceptive <int>, VinelandExpressive <int>,
    ## #   VinelandWritten <int>, DailyLivingSkills <int>, Socialization <int>,
    ## #   MotorSkills <int>, MullenRaw <int>, MullenTScore <int>,
    ## #   MullenAge <int>, FineMotorRaw <int>, FineMotorTScore <int>,
    ## #   FIneMotorAge <int>, ReceptiveLanguageRaw <int>,
    ## #   ReceptiveLanguageTScore <int>, ReceptiveLanguageAge <int>,
    ## #   ExpressiveLangRaw <int>, ExpressiveLangTScore <int>,
    ## #   ExpressiveLangAge <int>, EarlyLearningComposite <int>

``` r
head(length_u)
```

    ## # A tibble: 6 x 12
    ##   SUBJ  VISIT MOT_MLU MOT_LUstd MOT_LU_q1 MOT_LU_q2 MOT_LU_q3 CHI_MLU
    ##   <chr> <chr>   <dbl>     <dbl>     <dbl>     <dbl>     <dbl>   <dbl>
    ## 1 A.D.  visi~    3.62      2.16         2         4      5       1.25
    ## 2 A.D.  Visi~    3.86      2.42         2         4      6       1.01
    ## 3 A.D.  visi~    4.32      2.52         2         4      6       1.56
    ## 4 A.D.  visi~    4.42      2.45         2         4      6       2.25
    ## 5 A.D.  visi~    5.21      2.81         3         5      7.25    3.24
    ## 6 A.D.  visi~    4.66      2.77         2         5      7       2.87
    ## # ... with 4 more variables: CHI_LUstd <dbl>, CHI_LU_q1 <dbl>,
    ## #   CHI_LU_q2 <dbl>, CHI_LU_q3 <dbl>

``` r
head(word_d)
```

    ## # A tibble: 6 x 8
    ##   SUBJ  VISIT types_MOT types_CHI types_shared tokens_MOT tokens_CHI X    
    ##   <chr> <chr>     <int>     <int>        <int>      <int>      <int> <chr>
    ## 1 A.D.  visi~       378        14            9       1835        139 <NA> 
    ## 2 A.D.  Visi~       403        18           15       2160        148 <NA> 
    ## 3 A.D.  visi~       455        97           82       2149        255 <NA> 
    ## 4 A.D.  visi~       533       133          113       2260        321 <NA> 
    ## 5 A.D.  visi~       601       182          156       2553        472 <NA> 
    ## 6 A.D.  visi~       595       210          181       2586        686 <NA>

Explore the 3 datasets (e.g. visualize them, summarize them, etc.). You will see that the data is messy, since the psychologist collected the demographic data, the linguist analyzed the length of utterance in May 2014 and the fumbling jack-of-all-trades analyzed the words several months later. In particular: - the same variables might have different names (e.g. participant and visit identifiers) - the same variables might report the values in different ways (e.g. participant and visit IDs) Welcome to real world of messy data :-)

Before being able to combine the data sets we need to make sure the relevant variables have the same names and the same kind of values.

So:

2a. Identify which variable names do not match (that is are spelled differently) and find a way to transform variable names. Pay particular attention to the variables indicating participant and visit.

Tip: look through the chapter on data transformation in R for data science (<http://r4ds.had.co.nz>). Alternatively you can look into the package dplyr (part of tidyverse), or google "how to rename variables in R". Or check the janitor R package. There are always multiple ways of solving any problem and no absolute best method.

``` r
#-----> Finding problematic variables <-----#
# > In the Word Data, participants are called SUBJ - the observations are characters. The visits are called VISIT - and the observations are characters.
# > in the Length of Utterance, participants are called SUBJ . the observations are charactrs. The visits are called VISIT - and the observations are characters.
# > In the demography data, participants are called Child.ID - the observations are characters. the visits are called Visit, and the observations are integers. Also, the ethnicities are weird. 

# > Some participants are called by their names, others are given participant names.
# > Some of the values for the visits have capitalised letters, some have not. 
```

2b. Find a way to homogeneize the way "visit" is reported (visit1 vs. 1).

Tip: The stringr package is what you need. str\_extract () will allow you to extract only the digit (number) from a string, by using the regular expression \\d.

``` r
#-----> Homegenizing how "visit" is reported using str_extract() <-----#
? str_extract() # What does this do?
```

    ## starting httpd help server ... done

``` r
# Word Data
word_d$VISIT <- word_d$VISIT %>% str_extract("\\d") %>% as.integer() # Extracting and transforming the values into an integer.
# Length of utterances
length_u$VISIT <- length_u$VISIT %>% str_extract("\\d") %>% as.integer() # Extracting and transforming the values into an integer.
# > We do not need to change the demography data
```

2c. We also need to make a small adjustment to the content of the Child.ID coloumn in the demographic data. Within this column, names that are not abbreviations do not end with "." (i.e. Adam), which is the case in the other two data sets (i.e. Adam.). If The content of the two variables isn't identical the rows will not be merged. A neat way to solve the problem is simply to remove all "." in all datasets.

Tip: stringr is helpful again. Look up str\_replace\_all Tip: You can either have one line of code for each child name that is to be changed (easier, more typing) or specify the pattern that you want to match (more complicated: look up "regular expressions", but less typing)

``` r
#-----> Homegenizing how the participants is reported using str_replace_all() <-----#
? str_replace_all() # What does this do?
# Demography
demog$Child.ID <- demog$Child.ID %>% str_replace_all("[:punct:]", "")
# Length of utterances 
length_u$SUBJ <- length_u$SUBJ %>% str_replace_all("[:punct:]", "")
# Word data 
word_d$SUBJ <- word_d$SUBJ %>% str_replace_all("[:punct:]", "")
```

2d. Now that the nitty gritty details of the different data sets are fixed, we want to make a subset of each data set only containig the variables that we wish to use in the final data set. For this we use the tidyverse package dplyr, which contains the function select().

The variables we need are: \* Child.ID, \* Visit, \* Diagnosis, \* Ethnicity, \* Gender, \* Age, \* ADOS,
\* MullenRaw, \* ExpressiveLangRaw, \* Socialization \* MOT\_MLU, \* CHI\_MLU, \* types\_MOT, \* types\_CHI, \* tokens\_MOT, \* tokens\_CHI.

Most variables should make sense, here the less intuitive ones. \* ADOS (Autism Diagnostic Observation Schedule) indicates the severity of the autistic symptoms (the higher the score, the worse the symptoms). Ref: <https://link.springer.com/article/10.1023/A:1005592401947> \* MLU stands for mean length of utterance (usually a proxy for syntactic complexity) \* types stands for unique words (e.g. even if "doggie" is used 100 times it only counts for 1) \* tokens stands for overall amount of words (if "doggie" is used 100 times it counts for 100) \* MullenRaw indicates non verbal IQ, as measured by Mullen Scales of Early Learning (MSEL <https://link.springer.com/referenceworkentry/10.1007%2F978-1-4419-1698-3_596>) \* ExpressiveLangRaw indicates verbal IQ, as measured by MSEL \* Socialization indicates social interaction skills and social responsiveness, as measured by Vineland (<https://cloudfront.ualberta.ca/-/media/ualberta/faculties-and-programs/centres-institutes/community-university-partnership/resources/tools---assessment/vinelandjune-2012.pdf>)

Feel free to rename the variables into something you can remember (i.e. nonVerbalIQ, verbalIQ)

``` r
#-----> Making subsets of dataframes with lots of columns <-----#
# Demography data 
sub_demo <- demog %>% select(Child.ID, Visit, Diagnosis, Ethnicity, Gender, Age, ADOS, MullenRaw, ExpressiveLangRaw, Socialization)
# Lenght of utterances
sub_length <- length_u %>% select(SUBJ, VISIT, MOT_MLU, CHI_MLU)
# Word data 
sub_word <- word_d %>% select(SUBJ, VISIT, types_MOT, types_CHI, tokens_MOT, tokens_CHI)
```

2e. Finally we are ready to merge all the data sets into just one.

Some things to pay attention to: \* make sure to check that the merge has included all relevant data (e.g. by comparing the number of rows) \* make sure to understand whether (and if so why) there are NAs in the dataset (e.g. some measures were not taken at all visits, some recordings were lost or permission to use was withdrawn)

``` r
#-----> Merging all relevant colums into one giant dataframe <-----#

# filter_demo <- group_demo %>% filter(group_demo$Visit < 6)

#group_demo <- sub_demo %>% group_by(Child.ID) %>% summarise(Visit = n())

#group_word <- word_d %>% group_by(SUBJ) %>% summarise(VISIT = n())

#filter_word <- group_word %>% filter(VISIT < 6)

#group_utt <- length_u %>% group_by(SUBJ) %>% summarise(VISIT = n())

#filter_utt <- group_utt %>% filter(VISIT < 6)

#group_utt <- group_utt %>% arrange(SUBJ)

#group_word <- group_word %>% arrange(SUBJ)

#group_word$SUBJ == group_utt$SUBJ

#group_word$VISIT == group_utt$VISIT

#unique(group_demo$Child.ID[! group_demo$Child.ID %in% group_utt$SUBJ])

# > Changing the name of Child.ID and Visit
# SUBJ
colnames(sub_demo)[1] <- "SUBJ"
# VISIT
colnames(sub_demo)[2] <- "VISIT"

# > Merging the three dataframes
merge_word_utt <- merge(sub_length, sub_word)
merged <- merge(merge_word_utt, sub_demo)
```

2f. Only using clinical measures from Visit 1 In order for our models to be useful, we want to miimize the need to actually test children as they develop. In other words, we would like to be able to understand and predict the children's linguistic development after only having tested them once. Therefore we need to make sure that our ADOS, MullenRaw, ExpressiveLangRaw and Socialization variables are reporting (for all visits) only the scores from visit 1.

A possible way to do so: \* create a new dataset with only visit 1, child id and the 4 relevant clinical variables to be merged with the old dataset \* rename the clinical variables (e.g. ADOS to ADOS1) and remove the visit (so that the new clinical variables are reported for all 6 visits) \* merge the new dataset with the old

``` r
#---> Creating a new dataset with only visit 1 and the imprtan vairables
visit1 <- merged %>% filter(VISIT == 1)
# Removing VISIT an irrelevant columns
drops <- c("SUBJ","ADOS","MullenRaw", "ExpressiveLangRaw", "Socialization")
visit1 <- visit1[ , (names(visit1) %in% drops)]
# you can also remove VISIT like this
#visit1$VISIT <- NULL

#---> Renameing the clinical columns
# ADOS
colnames(visit1)[2] <- "ADOS1"
# MullenRaw
colnames(visit1)[3] <- "MullenRaw1"
# ExpressiveLangRaw
colnames(visit1)[4] <- "ExpressiveLangRaw1"
# Socialization
colnames(visit1)[5] <- "Socialization1"

#---> Merging the new dataset with the old
v1_merged <- merge(visit1, merged)
```

2g. Final touches

Now we want to \* anonymize our participants (they are real children!). \* make sure the variables have sensible values. E.g. right now gender is marked 1 and 2, but in two weeks you will not be able to remember, which gender were connected to which number, so change the values from 1 and 2 to F and M in the gender variable. For the same reason, you should also change the values of Diagnosis from A and B to ASD (autism spectrum disorder) and TD (typically developing). Tip: Try taking a look at ifelse(), or google "how to rename levels in R". \* Save the data set using into a csv file. Hint: look into write.csv()

``` r
#---> Anonymizing participants
# Making the participants into a factor
v1_merged$SUBJ <- v1_merged$SUBJ %>% as.factor()
# checking the levels
levels(v1_merged$SUBJ)
```

    ##  [1] "AD"      "Adam"    "AH"      "Albert"  "Alfie"   "Allison" "Annie"  
    ##  [8] "AR"      "AS"      "AS2"     "AZ"      "BC"      "CC"      "CD"     
    ## [15] "CD2"     "CH"      "Charles" "DH"      "Dirk"    "Eduardo" "Frankie"
    ## [22] "HG"      "Jack"    "Jason"   "JB"      "Jerry"   "JG"      "JGII"   
    ## [29] "Johan"   "John"    "JP"      "Judson"  "Kara"    "Kevin"   "KM"     
    ## [36] "LD"      "Lester"  "LL"      "Luis"    "Marius"  "MC"      "MD"     
    ## [43] "Milo"    "MJ"      "MM"      "Omar"    "RB"      "Rory"    "RR"     
    ## [50] "Ryder"   "SA"      "SB"      "SE"      "ST"      "TC"      "Tim"    
    ## [57] "Tina"    "Todd"    "VC"      "Vick"    "Witt"

``` r
# Relevelling the factor
levels(v1_merged$SUBJ)[1:61] <- 1:61
# unfactoring the factor
v1_merged$SUBJ <- v1_merged$SUBJ %>% as.integer()

#---> naming the genders
# Factoring it
v1_merged$Gender <- as.factor(v1_merged$Gender)
# Relevelling
v1_merged$Gender <- revalue(v1_merged$Gender, c("1"="M", "2"="F"))

#---> Naming the diagnosis
# Factoring it
v1_merged$Diagnosis <- as.factor(v1_merged$Diagnosis)
# What are the levels?
levels(v1_merged$Diagnosis)
```

    ## [1] "A" "B"

``` r
# Relevelling it
v1_merged$Diagnosis <- revalue(v1_merged$Diagnosis, c("A"="ASD", "B"="TD"))

#---> Saving to a csv file
write.csv(v1_merged, file = "dataset.csv")
```

1.  BONUS QUESTIONS The aim of this last section is to make sure you are fully fluent in the tidyverse. Here's the link to a very helpful book, which explains each function: <http://r4ds.had.co.nz/index.html>

2.  USING FILTER List all kids who:

<!-- -->

1.  have a mean length of utterance (across all visits) of more than 2.7 morphemes.
2.  have a mean length of utterance of less than 1.5 morphemes at the first visit
3.  have not completed all trials. Tip: Use pipes to solve this

USING ARRANGE

1.  Sort kids to find the kid who produced the most words on the 6th visit
2.  Sort kids to find the kid who produced the least amount of words on the 1st visit.

USING SELECT

1.  Make a subset of the data including only kids with ASD, mlu and word tokens
2.  What happens if you include the name of a variable multiple times in a select() call?

USING MUTATE, SUMMARISE and PIPES 1. Add a column to the data set that represents the mean number of words spoken during all visits. 2. Use the summarise function and pipes to add an column in the data set containing the mean amount of words produced by each trial across all visits. HINT: group by Child.ID 3. The solution to task above enables us to assess the average amount of words produced by each child. Why don't we just use these average values to describe the language production of the children? What is the advantage of keeping all the data?

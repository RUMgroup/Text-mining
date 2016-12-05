---
title: "TextMining 101"
author: "Paul Johnston"
date: "21 November 2016"
output: pdf_document
---

#What Is Text Mining?

One of the main lines of research in the field of computational linguistics and increasingly other disciplines is exploiting corpora.  

The idea is to collect a set of documents which have some common theme and by looking at them gain some insight. Since we have a collection we can look at things which are common across them all or look at differences between certain subsets. 

In R we make use of the "tm" package and the "NLP" package which is installed with it.  

##Example Research Question  
Is the G-Test replacing the $\chi^2$ test?  
If we had a corpus which had a large set of publications which we believe would use statistical tests and in each it explicitly said which test was used we could attempt to see the differences in the useage of either the G-Test or the $\chi^2$ test.  



##What is a Corpus?

In linguistics, a corpus (plural corpora) is a large and structured set of texts (nowadays usually electronically stored and processed). They can be used for many things including but not limited to statistical analysis and hypothesis testing, checking occurrences of terms or validating linguistic rules  such as spelling or grammar checking. One of the first major uses of corpora was the building of the Collins English disctionary in the 1980s.

What Do I Need To Do (Some maybe optional)

1. Collect some texts and join into a corpus
2. Clean up the texts
3. Add markup external to texts
4. Add markup internally to texts
5. Analyse


###Part 1 Creation

Simply get a collection of texts, either in plain or perhaps pdfs and place them into a single directory.

In this example we have plain text files which are stored in "c:\\work\\rstudio\\TM\\texts"  
Note: In the actual R code we use "/" as in  Unix not the "\" as used by Windows. 

```{r setting_wd}
print(list.files(path = "c:/work/rstudio/TM/texts"))
```

And pdf files in "c:\\work\\rstudio\\TM\\pdfs"

```{r}
print(list.files(path = "c:/work/rstudio/TM/pdfs"))
```

The key word in the definition above is set, while the corpus has many elements we treat it as a single entity and therefore our first task is to join the separate texts into a corpus.  
For this we use the VCorpus command in the tm package.

```{r load_corpus}
library(tm)
setwd("c:/work/rstudio/TM/texts")
(text_corpus <- VCorpus(DirSource((getwd())),readerControl = list(languages = "eng")))

setwd("c:/work/rstudio/TM/pdfs")
(pdf_corpus <- VCorpus(DirSource((getwd())),readerControl = list(languages = "eng")))
```

As we see the first corpus contains 10 documents and the second 4.  
Using the Enviroment section in RStudio we can see lots of information about our corpus.

###Part 2 Cleaning Up

One thing to bear in mind is that our texts are often messy and  preprocessing is required before we can start analysing. 

###Punctuation
If we assume a word is a string of letters separated by white space using a computer to generate word frequencies we would differentiate between "end", "end.", "end!" "End" and so on. Therefore a naive approach would be to simply remove and punctuation and capitalisation.  
Be aware however punctuation is sometimes use in unexpected ways...   
Simple tasks for humans such as deciding if a full stop marks the end of a sentence are second nature but a computer needs to be told the sentence "Dr. Watson looked out over the street." whilst having two full stops is in fact one sentence. Also if we wanted to count the number of times the word doctor was in a text here the fullstop is used as a shortening. Another example if we wanted to analyse first person pronouns any acronyms which use fullstops as separaters and contained "I" would have to be dealt with.  
Going back to our research question is it "G-Test", "G Test", "G test", you get the idea!  

The tm package comes with many functions to help us with such tasks, then normally are used with the tm_map function and it's associated transformations both provided or custom creations.  
Apology in order to show some of the effects we have jumped a bit forward and will be using some techniques disussed more in Part 5.  
Suffice to say if we remove punctuation we normally get higher frequencies for words. 

```{r}
first_pass_text_corpus <- tm_map(text_corpus,removePunctuation)
```
Similarly we can remove numbers and capital letters in the same fashion  

```{r}
n <-25
second_pass_text_corpus <- tm_map(first_pass_text_corpus,removeNumbers)
third_pass_text_corpus <- tm_map(second_pass_text_corpus,content_transformer(tolower))

findFreqTerms(DocumentTermMatrix(text_corpus),n)
findFreqTerms(DocumentTermMatrix(third_pass_text_corpus),n)
writeLines(as.character(third_pass_text_corpus[[1]]))
```
### Custom Transformations  

We can write our own transformations.
A practical example would be to change all instances of postcodes into a single consistent string, perhaps "postcode".  
The key bit is the flag perl = TRUE, if you can write a perl regular expression which will match a postcode and put that as the pattern and put the string "postcode" where replacement is all matches will be replaced by the string "postcode".  


###Part 3 Internal Markup

Internal markup is someting which is very valuable to linguists but I'm not sure if it is used in other fields with the possible exception of Social Sciences.  
Take for example if you want to look at the word "house", the exact same string can be used as a noun (a physical object in this case) or as a form of the verb "to house". In a plain document the sentences "The council seek to house people who the market deny access to buying their own house." The same string of letters mean something totally different. So if you want to use "Part of Speech"  tagging you add, often in the form of some XML, a "tag" which might say in our example firstly <w=house pos=verb> and in the second case <w=house pos=noun>.  
In linguistics this can often be taken to extremes.  
For this I suggest you look at ??NLP


###Part 4 External Markup

External markup is used to attach information to individual texts which make up a corpus and to the corpus as a whole.
Perhaps if you downloaded a collection of research papers about CRISPR you might want to have something indicating the quality of the journal which published each paper. Then you could give higher ratings to articles which came from higher rated journals.
If you want to know more about this search for ?? dublin within R.

###Part 5 Analysis



We will look at three different methods of looking at our data

1. Corpus Statistics

The main tool used in actually analysing our corpus is the use of DocumentTermMatrices.
This is a function which takes our corpus and creates an often rather sparse matrix which the rows are individual texts in the corpus and the columns represent the "words" in them.  
Below we look at our first corpus and look at the first four columns of the first four rows.

```{r create_dtm}
dtm_text <- DocumentTermMatrix(third_pass_text_corpus)
inspect(dtm_text[,100:105])
```
One of the many functions is the findFreqTerms() function which returns those terms which occur at least "n" times, here we use n = 20
```{r}
findFreqTerms(dtm_text, 20)

```
If we want to find the number of times a particular word occurs it's a case of some basic R matrix manipulation.  

```{r}
word_freq <- colSums(as.matrix(dtm_text))
ord <- order(word_freq)
word_freq[tail(ord)]

word_freq[head(ord)]
```



2. N-Grams

"You shall know a word by the company it keeps (Firth, J. R. 1957:11)"
click here -> [Firth](https://en.wikipedia.org/wiki/John_Rupert_Firth) <-.  

In isolation words can be quite ambiguous, however going back to choosing what the string "house" represents it should be clear if it is preceeded by "to" it is most likely to be a verbal form whereas if it is preceeded by and adjective "big", "expensive" or "run-down" it is a noun.  

To be able to investigate these N-grams we use the package "nlp".

To make things clearer we will write a function which takes a corpus and generates information about Bigrams.

```{r}
BigramTokenizer <-
function(x)
unlist(lapply(ngrams(words(x), 2), paste, collapse = " "), use.names = FALSE)
```

Now lets create a Term Document Matrix 

```{r}
tdm_2 <- TermDocumentMatrix(third_pass_text_corpus, control = list(tokenize = BigramTokenizer))

```
Now look at this data

```{r}
inspect(removeSparseTerms(tdm_2[, 1:4], 0.5))
```
We can use the methods we saw previously on this data  
```{r}
findFreqTerms(tdm_2,20)
```

3. Word Clouds


Finally a pretty picture

```{r first_wordcloud}
require(SnowballC)
require(wordcloud)
wordcloud(third_pass_text_corpus)

```
Aside:  
Here I have removed the stop words and all of a sudden I get a square cloud!  
Click here -> https://en.wikipedia.org/wiki/Stop_words <-


```{r second_wordcloud, warning=FALSE, message=FALSE}
new_corpus <- tm_map(third_pass_text_corpus,removeWords, stopwords("english"))
wordcloud(new_corpus)
```

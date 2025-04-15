# Japanese Reading Analyzer

A machine learning model that identifies patterns between Japanese words and the kanji features that compose the words.
---

## Data Summary

- The first dataset utilizes a dataset that can be found on Kaggle called JLPT vocabulary by level. This dataset is a comprehensive list of unofficial Japanese Language Proficiency Test vocabulary, that’s includes words from the JLPT’s beginner level (N5) to advanced (N1).
- Then I collected data from the Jōyō Kanji list from Wikipedia, which is the official list of kanji taught to Japanese students from elementary school to secondary school.
- The next dataset that I downloaded is called KanjiVG, which is an xml that maps the decomposition of kanji. Kanji is made up of many components, and those components can be made up of multiple components.
- The final dataset that I utilized is called Kradfile. This is the dataset that contains every possible kanji reading.

---

## Data Fields
### Kanji Count
Using the kanji from the Jōyō Kanji list, I identified each individual kanji for every word in the JLPT vocabulary list, and counted each occurrence.
### Component Count
Using the data from the Jōyō Kanji list, I joined the kanji with the KanjiVG xml data, and discovered the component count for each kanji from the Joyo Kanji list. This component count was the sum of each component count per kanji count.
### Stroke Count
The Jōyō Kanji has the stroke count for each kanji, and was the sum of each kanji per word in the JLPT list.
### JLPT
The JLPT rank, N5 through N1, was already a feature in the JLPT dataset.
### Kanji Grade Level
I took the grade level for each kanji in the JLPT record and averaged it, rounding up. The Joyo has grade levels 1 through 6 and “secondary”, meaning the grades 7-12. I used the value 10 as a rounded-up average for the “secondary” values.
### Reading
I used the Kradfile to join an array of every reading for each Kanji used in the JLPT dataset. This was an array per kanji per JLPT record. I created a lookup formula in excel to estimate the reading value, but ultimately, coded the data manually with the assistance of the estimation indicators. For example, 0-4 was the likelihood of a reading array matching the JLPT kana reading. This made the manual labelling easier to identify.

---

## Machine Learning
Using the Pandas Python library, my initial step was to create a scatter plot matrix for each independent variable while using the readings as a category to identify any potential clusterings.

![screenshot](/capstone%20screenshot%201.JPG)
Intuitively, kanji count had a positive correlation with component count and stroke count, however, features like average grade and JLPT didn’t appear to have any kind of correlation between the other features. In terms of clustering, each feature did have some identifiable separation. The challenge with the clustering is that there’s a lot of overlap. Kun plots wouldn’t be shown if they were plotted first, which lead me to believe that on overlaps most instances of kun, but not vice versa. So, I made sure to order the readings column so that the on was plotted first, to get a better idea of where the overlaps end. My hope is that there’s enough feature data to make up for the overlapping clusters.



Then I used a Scikit-learn’s decision tree model to better identify which features work best. It showed me that kanji count was dominating every other featuring.

![screenshot](/capstone%20screenshot%202.JPG)
This was a problem because this variable was overshadowing every other one. I made the decision to exclude the kanji count, and got a
10 slightly lower accuracy score of 78%. I was satisfied with the score, because I want to find patterns using a more balanced approach between the features, not just a single feature dominating the rest.

Speaking of balanced approaches, after selecting the features I wanted to use, I used Scikit-learn’s voting classifier, which weighs 3 training models at once, decision tree, k-neighbors, and SVC. Using this model I achieved a slightly higher accuracy level.

## Validation
I created an 80/20 split between the dataset, creating a training and testing dataset. Both sets are split between the dependent and independent variables, with an accuracy score between 0 and 1. My highest score using the voting classifier is .7859

I used a confusion matrix to analyze the predictions. It appears that there were considerably more incorrect on reading predictions than kun reading predictions.
User Guide
![screenshot](/capstone%20screenshot%203.JPG)


## User Guide
The app is an analysis tool that breaks down the true and false predictions by JLTP Rank.
## Enivornmental Setup
- Download Anaconda Navigator https://www.anaconda.com/products/navigator
- After installing and launching the app, navigate to Jupyter Notebooks
- Create a new note book and install the following libraries
- pip install pandas
- pip install seaborn
- pip install -U scikit-learn --user

![screenshot](/capstone%20screenshot%204.JPG)

## Application User Guide
- Download the Jupyter Notebook source file here https://github.com/MikeGillotti/capstone-project/blob/main/capstone%20project.ipynb
- Within Jupyter Notebook, navigate to the project file and open it.
- First, we’ll need to rerun it: Kernel -> Restart & Run All
- Navigate to the bottom of the notebook.
- Move the JLPT slider between values 1 and 5, representing the JLPT ranks.
- Hit the False Predictions button to generate what the model incorrectly predicted along with its associated data.
- Hit the True Predictions button to generate what the model correctly predicted along with its associated data.
- The generated data includes the following
  - Accuracy by JLPT rank
  - A chart measuring the mode of each feature
  - A table showing each corresponding record, including kanji, kana, meaning, and actual reading.

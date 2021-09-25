# Webpage Word Counter
My 6-year-old son has been continuing with his strong interest in political geography and recently discovered a book by Ian Wright titled 'Brilliant Maps'. One of those maps  was about the most occuring word in each country's English Wikipedia page. 
<br><br>
As I have been recently delving into the world of Natural Language Processing, I though it would be fun to recreate it by coding a simple Python application that returns the top occuring words from a Wikipedia page and plot them on a map.
## The code
The code utilises ```BeautifulSoup``` for pulling data out of a webpage and ```NLTK``` library for a list of stopwords as well as to tokenise the page, and ```GeoPandas``` to get polygons in order to plot the countries.<br>
I first extracted the paragraphs from the website:<br>
```Python
# make a soup 
soup = BeautifulSoup(source,'lxml')
# extract the plain text content from paragraphs
text = ''
for paragraph in soup.find_all('p'):
    text += paragraph.text
```
Tokenised the words:<br>
```Python
# tokenize the words
tokens = word_tokenize(text, language="english")
```
Removed stopwords and punctuation:<br>
```Python
# remove stopwords
tokens = [word for word in tokens if word.lower() not in stop_words]
# remove punctuation
tokens = [word for word in tokens if word.isalpha()]
```
Vectorised the tokens (count the occurance of each token):<br>
```Python
# vectorize tokens - count occurance of each word
vectors = vectorize(tokens)
```
Only kept the ones with occurance over a certain level (e.g. 20 for country pages on Wikipedia):<br>
```Python
# only keep the ones with counts over 20
vectors_reduced = {key:value for (key,value) in vectors.items() if value >= word_limit}
```
And finally sorted the resulting dictionary by value in descending order:<br>
```Python
# sort the result in descending order
vectors_sorted =dict(sorted(vectors_reduced.items(), key=operator.itemgetter(1),reverse=True))
```
## Running the application
There are a few ways of running the application:
### Running for a single country
Either hard coding the URL (copy and paste from a website) or only entering the search term, which would be appended to the rest of the URL. <br>
For example, a search term 'United Kingdom' would be replaced with 'United_Kingdom' and then appended to create URL [https://en.wikipedia.org/wiki/United_Kingdom](https://en.wikipedia.org/wiki/United_Kingdom)
```Python
# ask the user for input
user_input = input('Please input country: ')
# convert spaces to underscores
user_input_cleaned = user_input.replace(' ', '_')
# set up a full path for URL
url = 'https://en.wikipedia.org/wiki/' + user_input_cleaned
# run the function to extract the top words
top_words(url)
```
As the term could be entered incorrectly, for example 'Unite Kingdom', I added a helper function that checks the URL and returns relevant error if the page is not returning a 200 OK response.
### Running for multiple countries
Alternatively, I used a list of countries and created a dictionary of words for each country. I also created a global dictionary of words in order to remove some of the most popular ones (such as 'population', 'government' or 'country'). I also removed words that contain some part of the name of the country or the capital city.
```Python
# iterate through all countries
for index, row in countries.iterrows():
  # convert spaces to underscores and dots to nothing
  country_input_cleaned = row['name'].replace(' ', '_')
  country_input_cleaned = country_input_cleaned.replace('.', '')
  print(row['name'])
  # set up a full path for URL
  url = 'https://en.wikipedia.org/wiki/' + country_input_cleaned
  # run the function to extract the top words
  words = top_words(url)
  # append the words to both dictionaries
  country_dict[row['name']] = words
  word_dict = {k: word_dict.get(k, 0) + words.get(k, 0) for k in set(word_dict) | set(words)}
```
## The resulting map
The resulting map contains 162 countries. Some have been lost, due to GeoPandas not being able to match up with the other dataset, however most of them were smaller countries and overseas territories.
<img  src="https://github.com/maciejtarsa/Webpage-Word-Counter/blob/main/top_words.png">
<div align='center'><i>Created using Matplotlib in Python</i></div>

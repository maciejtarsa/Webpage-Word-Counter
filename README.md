# Webpage Word Counter
An application that extracts the content of a website and displays the most frequently occurring words on that webpage.
<br><br>
My 6-year-old son has been continuing with his strong interest in political geography and recently discovered a book by Ian Wright titled 'Brilliant Maps'. One of those maps  was about the most recurring word in each country's English Wikipedia page. 
<br><br>
Seeing as recently I have been learning about Natural Language Processing, I though it would be fun to recreate it by coding a simple Python application that returns the top occuring words from a Wikipedia page so that my son can use it and recreate that map. He loves creating maps with tools such as [mapchart.net](https://mapchart.net) or [simplemaps.com](https://simplemaps.com). A win-win situation - NLP practice for me, a fun activity for him.
## The code
The code utilises ```BeautifulSoup``` for pulling data out of a webpage and ```NLTK``` library for a list of stopwords as well as to tokenise the page.<br>
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
There are two ways of running the application. Either hard coding the URL (copy and paste from a website) or only entering the search term, which would be appended to the rest of the URL. <br>
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
## The resulting map
At the moment, the resulting map is still in progress. However, the iterim result is displayed below:
<img  src="https://github.com/maciejtarsa/Webpage-Word-Counter/blob/main/world.png">
<p align="center"><i>Created by my son using simplemaps.com</i></p>
Some words have been excluded even though they can have high frequencies - linking words, demonyms, 'country', 'world', 'government' and the name of the country the page is about.<br>
For example, 'largest' has been chosen for China, while the application returns the following ('word":count):<br>
> 'China': 367, 'Chinese': 153, 'world': 111, 'country': 55; 'largest': 55, 'dynasty': 45, 'million': 44, 'population': 39, 'also': 36, 'needed': 36, [...]

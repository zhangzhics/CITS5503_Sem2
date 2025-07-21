# Practical Worksheet 9

Version: 1.0 Date: 4/10/2021 Author: Camilo Pestana

Date: 29/07/2024 Updated by Zhi Zhang

## Learning Objectives

1. Learn more about AI and Machine Learning services available on AWS.
2. Use boto3 to get hands-on experience on using useful AI services in AWS for Natural Language Processing (NLP)/ Natural Language Understanding (NLU) and Computer Vision.

## Technologies Covered

* Ubuntu
* AWS Comprehend
* AWS Rekognition
* boto3
* Python

**NOTE**: please use your Linux environment – if you do it from any other OS (e.g., Windows, Mac – some unknow issues might occur)

## Background

The aim of this lab is to write a series of scripts that will test the main features of AWS Comprehend and AWS Rekognition.

## AWS Comprehend

AWS Comprehend offers different services to analyse text using machine learning. With Comprehend API, you will be able to perform common NLP tasks such as sentiment analysis, or simply detecting the language from the text.

"Amazon Comprehend can discover the meaning and relationships in text from customer support incidents, product reviews, social media feeds, news articles, documents, and other sources. For example, you can identify the feature that's most often mentioned when customers are happy or unhappy about your product."

For example, to detect the language used in a given text using boto3 you can use the following code:
```python
import boto3
client = boto3.client('comprehend')

# Detect Entities
response = client.detect_dominant_language(
    Text="The French Revolution was a period of social and political upheaval in France and its colonies beginning in 1789 and ending in 1799.",
)

print(response['Languages'])
```

By executing the code above, we will get something like this:
```
[{'LanguageCode': 'en', 'Score': 0.9961233139038086}]
```
This means that the detected language is 'en' (English) and has a confidence in the prediction greater than 0.99. 

**NOTE**: remember that often in machine learning the confidence score is expressed as a value in the range [0,1] where 0 indicates the lack of certainty and 1 means totally certain of the prediction.

### Detect Languages from text

#### [1] Modify the code above
Based on the previous code, write a python script that can detect different languages. Besides, instead of language code (e.g., 'en' for English or 'it' for Italian), the script should be return the message "<predicted_language> detected with <xx> confidence" where <predicted_language> correspond to the name of the language in English and <xx> is given as a percentage. For the previous example, the result should look like this:

```
English detected with 99% confidence
```

**NOTE**: The relevant APIs are available [here](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/comprehend.html).

#### [2] Test your code with other languages

Test your code using the following texts in different languages:

**English:**
"The French Revolution was a period of social and political upheaval in France and its colonies beginning in 1789 and ending in 1799."


**Spanish:**
"El Quijote es la obra más conocida de Miguel de Cervantes Saavedra. Publicada su primera parte con el título de El ingenioso hidalgo don Quijote de la Mancha a comienzos de 1605, es una de las obras más destacadas de la literatura española y la literatura universal, y una de las más traducidas. En 1615 aparecería la segunda parte del Quijote de Cervantes con el título de El ingenioso caballero don Quijote de la Mancha."

**French:**
"Moi je n'étais rien Et voilà qu'aujourd'hui Je suis le gardien Du sommeil de ses nuits Je l'aime à mourir Vous pouvez détruire Tout ce qu'il vous plaira Elle n'a qu'à ouvrir L'espace de ses bras Pour tout reconstruire Pour tout reconstruire Je l'aime à mourir"
[From the Song: "Je l'Aime a Mourir" - Francis Cabrel ]

**Italian:**
"L'amor che move il sole e l'altre stelle."
[Quote from "Divine Comedy" - Dante Alighieri]

### Analyze sentiment 

Sentiment analysis (or opinion mining) uses NLP to determine whether data is positive, negative or neutral. Sentiment analysis is often performed on textual data to help businesses monitor brand and product sentiment in customer feedback, and understand customer needs.

Use boto3 and AWS comprehend to create a python script for sentiment analysis and apply the previous 4 texts to test the script.

### Detect entities

Use boto3 and AWS comprehend to create a python script for entities detection and apply the previous 4 texts to test the script.

Answer this question: describe what entities are in your own words.

### Detect keyphrases

Use boto3 and AWS comprehend to create a python script for keyphrases detection and apply the previous 4 texts to test the script.

Answer this question: describe what keyphrases are in your own words.

### Detect syntaxes

Use boto3 and AWS Comprehend to create a python script for syntax detection and apply the previous 4 texts to test the script.

Answer this question: describe what syntaxes are in your own words.

## AWS Rekognition

AWS Rekognition is the service of AWS that allows you to perform machine learning tasks on images.

Currently, given an image, AWS Rekognition allows:
1. **Label Recognition**: automatically label objects, concepts, scenes, and actions in your images, and provide a confidence score.
2. **Image Moderation**: automatically detect explicit or suggestive adult content, or violent content in your images, and provide confidence scores.
3. **Facial Analysis**: get a complete analysis of facial attributes, including confidence scores.
4. **Extract Text from an image**: automatically detect and extract text in your images.

### Add images

Create a python script: create an S3 bucket named as <studentid>-lab9 in the region you are mapped to. Add the 4 following images into the bucket:

1. Add an image of an urban setting (named as urban.jpg).

2. Add an image of a person on the beach (named as beach.jpg).

3. Add an image with people showing their faces (named as faces.jpg).

4. Add an image with texts (named as text.jpg).

### Test AWS rekognition

Update the python script above by using boto3 and AWS rekognition to test label recognition, image moderation, facial analysis and text extraction from images.

**NOTE**: The relevant APIs are available [here](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/rekognition.html).

Lab Assessment:

A structured presentation (15%). A clear step-by-step with detailed descriptions (85%). 

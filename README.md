# Advanced-Tourist-Guide-System
A web based app designed and developed using Django framework of Python with the power of Deep learning for sentiment analysis. Aim of the project is to provide travelers a solution to make their travels more easy &amp; convenient.

- A web based application designed and developed using Django framework of python. Aim of the project is to make a tool using which, users can get information about the places very easily. 


Features of the project : 
	- User can search the place via, place name as well as via location name.
	- Home screen of the portal will display the top most rated places from the database.
	- User will also get the similar places & also other places nearby his search.
	- Reviews will be stored as a comment for the place and also, sentiment analysis will be done using Deep Learning and ratings will be stored.
	- User can also give feedback to the system
	- User can get direction from his current location to the place.


TOOLS AND TECHNOLOGIES USED :
	- Frontend : HTML, JS, CSS
	- Backend : Python, DJango framework
	- Sentiment Analysis : CNN-LSTM-MAXPOOL


DATASET FOR MODEL TRAINING :
	- GoogleNews-vectors-negative300.bin.gz
		https://s3.amazonaws.com/dl4j-distribution/GoogleNews-vectors-negative300.bin.gz

	- Sentiment-Analysis-Dataset.zip
		http://thinknook.com/wp-content/uploads/2012/09/Sentiment-Analysis-Dataset.zip

ALGORITHM USED :
 	- CNN with LSTM & MAXPOOL

METHODOLOGY :
	- Firstly, we downloaded the datasets from the above given links.
	
	1) importing the required modules :

		import gensim.models.keyedvectors as word2vec 
		from nltk.tokenize import RegexpTokenizer
		import keras
		import numpy as np
		import pandas as pd
		import matplotlib.pyplot as plt
		import sklearn



	2) PreProcessing  : 

	- read CSV file containing tweets and labels, using Pandas , to get a dataframe : 
		tweetsData = pd.read_csv('Sentiment Analysis Dataset.csv', skiprows=[8835],nrows=40000) 

	- Dividing the dataset into features and lables :
		tweets = tweetsData['SentimentText']
		labels = tweetsData['Sentiment']

	- Lower and split the dialog and use regular expression to keep only letters we will use nltk Regular expression package :
		tkr = RegexpTokenizer('[a-zA-Z@]+')

	- Use pretrained Word2Vec model from google but trim the word list to 50,000 compared to 3,000,000 in the originaL Google pretrained model :
		w2vModel = word2vec.KeyedVectors.load_word2vec_format('GoogleNews-vectors-negative300.bin')

	- Convert words to integers :
		tokenizer = Tokenizer(num_words=len(tweets_split)+1)
		tokenizer.fit_on_texts(tweets_split)
		X = tokenizer.texts_to_sequences(tweets_split)

	- create a embedding layer using Google pre triained word2vec (50000 words) :
		embedding_layer = Embedding(input_dim, output_dim, weights, input_length)



	3) Building Model Architecture :

		- Create model combining LSTM with 1D Convonutional layer and MaxPool layer

			model = Sequential()
			model.add(embedding_layer)
			model.add(Conv1D(filters=64, kernel_size=5, activation='relu', padding='causal'))
			model.add(MaxPooling1D(pool_size=2))
			model.add(Dropout(0.7))
			model.add(LSTM(units=lstm_out))
			model.add(Dropout(0.7))
			model.add(Dense(1, activation='sigmoid'))
			model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy'])




	4) Fitting training data to model :
		- split dataset
			X_train, X_test, Y_train, Y_test = train_test_split(X, labels, test_size= 0.1)

		- fit model
			batch_size = 32
			training = model.fit(X_train, Y_train, epochs=5, verbose=1, batch_size=batch_size)



	5) Evaluatiing Model :

		- analyze the results :
			score, acc = model.evaluate(X_test, Y_test, verbose = 2, batch_size=batch_size)
			y_pred = model.predict(X_test)

		- Other accuracy metrices :
			y_pred = (y_pred > 0.5)

		- confusion metrix :
			cm = confusion_matrix(Y_test, y_pred)
			print(cm)




	6) Visualizing the model :
		- ROC AUC curve
			rocAuc = roc_auc_score(Y_test, y_pred)

			falsePositiveRate, truePositiveRate, _ = roc_curve(Y_test, y_pred)

			plt.figure()

			plt.plot(falsePositiveRate, truePositiveRate, color='green')
			plt.plot([0, 1], [0, 1], color='navy', lw=3, linestyle='--')
			plt.xlim([0.0, 1.0])
			plt.ylim([0.0, 1.05])
			plt.xlabel('False Positive Rate')
			plt.ylabel('True Positive Rate')
			plt.title('Receiver Operating Characteristic of Sentiiment Analysis Model')
			plt.legend(loc="lower right")
			plt.show()

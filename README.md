# Simple Spam Classifier

This a system for simple spam classifier using python
(theoritically it could be used for any binary classifier, but it would only return SPAM/HAM label).
Currently this system define SPAM as 0 and HAM as 1.
The classifier are using scikit-learn package.The classifier are using scikit-learn package.
It has 5 available classifier, and each time its trained, it will search the best parameter for the model.
For NGram, it use both unigram and bigram.
If it necessary you could change the search parameter.
A single MultinomialNB classifier model with ~10000 training data and test data, resulting to ~28mb model file(compressed .gz).

The system is can be accessed via API, as is based on Flask.
The system can be built as docker container, or running it as Flask app normally.
For simple purpose, you should use the docker container.
For default, the API can be accessed at port 8000.

### App structure
```
# Minimal Project tree
 * [additional_data](./additional_data)
 * [migrations](./migrations)
 * [logs](./logs)
 * [spam_classifier.py](./spam_classifier.py)
 * [seeds](./seeds)
   * [test_mails](./seeds/test_mails)
     * [ham](./seeds/test_mails/ham)
     * [spam](./seeds/test_mails/spam)
   * [train_mails](./seeds/train_mails)
     * [ham](./seeds/train_mails/ham)
     * [spam](./seeds/train_mails/spam)
 * [requirements.txt](./requirements.txt)
 * [app](./app)
 * [config.py](./config.py)
 * [spam_model](./spam_model)
 * [Dockerfile](./Dockerfile)
 * [boot.sh](./boot.sh)
 * [EmailProcessing](./EmailProcessing)
```

### App configuration

```dotenv
FLASK_APP="spam_classifier.py" #don't change
FLASK_DEBUG=0 
FLASK_ENV="production"
FLASK_RUN_PORT=8000 #used when running as flask app 
FLASK_RUN_HOST=0.0.0.0 #used when running as flask app
SECRET_KEY=123456 # required for session, security, if not defined, it should use random generated
# see http://flask-sqlalchemy.pocoo.org/2.3/config/ for more info
# other than the key defined here, it will use default
SQLALCHEMY_TRACK_MODIFICATIONS=0
SQLALCHEMY_DATABASE_URI=sqlite:///home/spam_classifier/app.db
SQLALCHEMY_RECORD_QUERIES=0
SQLALCHEMY_POOL_RECYCLE=300
SQLALCHEMY_POOL_SIZE=10
SQLALCHEMY_MAX_OVERFLOW=10
# logging
LOG_LEVEL=ERROR 
LOG_TO_STDOUT=1 # if False, then it will write to file
# celery, necessary for running the training, if not provided, the container will fail to run
CELERY_RESULT_BACKEND='redis://localhost:6379/0'
CELERY_BROKER_URL='redis://localhost:6379/0'
# basic auth configuration, to securing the access, if necessary use private network
BASIC_AUTH_USERNAME=spamclassifier
BASIC_AUTH_PASSWORD=123456
BASIC_AUTH_FORCE=0
# training configuration
TRAIN_PERIOD_LIMIT=86400 # training time limit, unable to train before time limit passed or last training completed/dropped
ACTIVE_CLASSIFIER=MultinomialNB,BernoulliNB,GaussianNB,LinearSVC,SGDClassifier,
STOP_WORDS = all # stopword language, from nltk, use all if you want to remove all
KBEST_FUNCT = chi2 # feature selection function, available : chi2, f_classif, mutual_info_classif
KBEST_COMP = 2 # feature selection k value
USE_STEMMER=0 # Use stemmer or not
USE_MULTI_LANGUAGE_STEMMER=0 # Use multi language stemmer(autodetect, default to english if fail) or not
STEMMER_LANGUAGE=english # stemmer language if not using auto detect, available : All language in nltk.stem.SnowballStemmer and PySastrawi(indonesian)
MODEL_PERSISTENCE=joblib # available : pickle, joblib, use pickle for faster and uncompressed model
```

### Available API
Use application/x-www-form-urlencoded for parameter.
```
/ (GET) # show app name and version
/train (GET) # start train, else report if cannot start training
/classify (POST, param=['subject','body']) # classify 
/metric # get metric
/clear_model # clear existing classifier model

/training_data (GET, param=['limit','page']) 
/training_data/<id> (GET)
/training_data (POST, param=['subject','body','label'])
/training_data/<id> (PUT, param=['subject','body','label'])
/training_data/<id> (DELETE)

/test_data (GET, param=['limit','page']) 
/test_data/<id> (GET)
/test_data (POST, param=['subject','body','label'])
/test_data/<id> (PUT, param=['subject','body','label'])
/test_data/<id> (DELETE)
```

### TODO
- Add more test
- Trim docker image
- Separate each classifier into different model file
- Make more flexible labelling(multi-class)
- Add more classifier
- Add Lemmatizer

### Docker repo
[Repo](https://hub.docker.com/u/kirimemail/simple-spam-classifier)

For question: mail me at gamalan.at.kirim.dot.email or gamalanpro.at.gmail.dot.com.
Feel free to make pull request.
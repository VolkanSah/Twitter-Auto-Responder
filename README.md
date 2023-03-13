# Twitter Auto-Responder
This Python script uses the Twitter API and Tweepy to automatically respond to mentions on Twitter that include the hashtag "#helloworld". 

## Getting Started
To use this script, you will need to have a Twitter developer account and create an app to obtain your API keys and access tokens. You will also need to install Tweepy and Python 3 on your machine.


Once you have your API keys and access tokens, you can enter them into the `CONSUMER_KEY`, `CONSUMER_SECRET`, `ACCESS_KEY`, and `ACCESS_SECRET` variables at the top of the script.

The script will store the ID of the last mention it responded to in a text file called `last_seen_id.txt`. Make sure you have write access to this file.


## How it Works
The script uses Tweepy to authenticate with the Twitter API and retrieve any new mentions since the last time it was run. It then checks each mention for the hashtag "#helloworld" and responds with a pre-defined message if the hashtag is found.

The script runs in a continuous loop and checks for new mentions every 15 seconds. 

## Usage
To use the script, run it from the command line with the following command:
 
```python
import tweepy
import time

print("tweepy imported")

CONSUMER_KEY = ''
CONSUMER_SECRET = ''
ACCESS_KEY = ''
ACCESS_SECRET = ''

auth = tweepy.OAuthHandler(CONSUMER_KEY, CONSUMER_SECRET)
auth.set_access_token(ACCESS_KEY, ACCESS_SECRET)
api = tweepy.API(auth)

FILE_NAME = 'last_seen_id.txt'

def retrieve_last_seen_id(file_name):
    """
    Retrieve the last release id of a file.
    Args:
        file_name: (str): write your description
    """
    try:
        with open(file_name, 'r') as f_read:
            last_seen_id = int(f_read.read().strip())
    except FileNotFoundError:
        last_seen_id = 0
    return last_seen_id

def store_last_seen_id(last_seen_id, file_name):
    """
    Store the last modified_seen_id.
    Args:
        last_seen_id: (str): write your description
        file_name: (str): write your description
    """
    with open(file_name, 'w') as f_write:
        f_write.write(str(last_seen_id))

def reply_to_tweets():
    """
    Reply to the last reply.
    Args:
    """
    last_seen_id = retrieve_last_seen_id(FILE_NAME)
    mentions = []
    try:
        mentions = api.mentions_timeline(last_seen_id, tweet_mode='extended')
    except tweepy.TweepError as e:
        print("Error: ", e)

    for mention in reversed(mentions):
        print(str(mention.id)+'-'+mention.full_text)
        last_seen_id = mention.id
        store_last_seen_id(last_seen_id, FILE_NAME)
        if '#helloworld' in mention.full_text.lower():
            print("Found #helloworld!")
            print("Responding back...!")
            try:
                api.update_status('@'+ mention.user.screen_name + '#HelloWorld back to You....' , mention.id)
            except tweepy.TweepError as e:
                print("Error: ", e)

while True:
    reply_to_tweets()
    time.sleep(15)
```

## Contributions
Contributions to this project are welcome. If you find any bugs or have suggestions for new features, please create a new issue or submit a pull request.

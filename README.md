# twitter-api
 
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



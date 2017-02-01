This is just for fun.

I am going to introduce how we can easily build a basic Twitter bot with Python and the use of Twitter's API.

We are going to be inspired by the account [The Alot Shepherd](https://twitter.com/TheAlotShepherd/), a bot that searches for tweets including the typo 'alot' and reply to them with a [pre-defined image of the Alot Shepherd in the answer](https://twicopy.org/TheAlotShepherd/).

![Alot of Fun](http://i.imgur.com/COEpa.png)

# How to start?

First of all we need to get familiar with [Tweepy](http://tweepy.readthedocs.io/en/v3.5.0/), an easy to use Python library for accessing to the Twitter API. We need to install it in our machine:

```python
> pip install tweepy
```

After, we have to create a new application by visiting our [Twitter developer page](https://apps.twitter.com/) - in case our account profile has no phone number set, you must add your mobile phone to your Twitter profile before creating an application.

Click the button "Create a new app", and follow the instructions. After you fill the form and confirm it, under Application Settings click on "manage keys and access tokens". Finally click on "Create my access token".

Now it is time to add the following files in our project folder:

- *keys.py* where we are going to save the consumer and access key tokens.
- *bot.py* which is our application and where we are going to code our bot.

We can copy and paste in a dictionary the consumer and access keys from our app.

```python
#!/usr/bin/python
# keys.py

keys = {
    'consumer_key': 'CONSUMER_KEY',
    'consumer_secret': 'CONSUMER_SECRET',
    'access_token': 'ACCESS_TOKEN',
    'access_token_secret': 'ACCESS_TOKEN_SECRET',
}
```

Now we can create our object api with tweepy.
We first need to grant with our consumer and access keys to a new tweepy Open Authentication class and initialize our tweppy module- just a quick reminder, in Python classes are denoted by capitalize names while modules are denoted by all-lowercase names.

```python
#!/usr/bin/python
# bot.py

import tweepy

from keys import keys

auth = tweepy.OAuthHandler(keys['consumer_key'], keys['consumer_secret'])
auth.set_access_token(keys['access_token'], keys['access_token_secret'])

api = tweepy.API(api)
```

It is better use a fake account instead of our regular one as Twitter can blocked it if they detect we are using it as a bot.

For this example, I created a fake Twitter account with the id [@Cani92286899](https://twitter.com/Cani92286899/with_replies) and name 'Cani'. Some user in Quora defined a Spanish [cani](https://www.quora.com/Whats-the-meaning-of-Spanish-cani-choni-etc) as a *male youth who are notorious for wearing sports clothing, outlandish jewelry and accesories, proudly and overtly displaying their lack of manners and education, as well as being fans of Reggaeton music*.

Now we are going to make a literal search of tweets that will return a total of 20 tweets in the desired language (Spanish) containing exactly the sentence 'eres un cani' ('you are a cani').

```python
query = '"eres un cani"'

tweet_list = api.search(
    q=query,
    count=20,
    lang='es'
)
```

Now we can go through every tweet we found, and reply every single Tweet with a message we want, in this case 'yo tambien soy cani!' (I am also a cani!).

The update_status call is the one responsible to generate the response to the message. As the program can raise an exception if we constatly publish response tweets to users (because of Twitter restrictions), we need to use the try/except block to avoid program hang or freeze.

```python
for tweet in tweet_list:
    screen_name = tweet.user.screen_name

    message = '@{username} {message}'.format(
        username=screen_name,
        message='yo tambien soy cani!'
    )

    try:
        api.update_status(
            status=message,
            in_reply_to_status_id=tweet.id
        )
        print message

    except tweepy.TweepError as e:
        print e.message

```

Now it is time to run our script and print our messages.

```python
> python bot.py
@em186__ yo tambien soy cani!
@MusicaBanjo43 yo tambien soy cani!
@_vannsss yo tambien soy cani!
@itskeisy yo tambien soy cani!
@lionlolcod123 yo tambien soy cani!
@Adriiyoungg yo tambien soy cani!
@Sasuviq yo tambien soy cani!
@ivanivanciyo yo tambien soy cani!
@Cristinaort30 yo tambien soy cani!
@xavivill99 yo tambien soy cani!
@DoblasftWilson yo tambien soy cani!
@mellamanjoey yo tambien soy cani!
@UnaiGadi yo tambien soy cani!
@GemGalicia yo tambien soy cani!
@camilocuellarm yo tambien soy cani!
@ElAntiyo yo tambien soy cani!
```

![alt cani]({{ site.url }}/assets/images/twitter_bot_post.png)

Awesome how easy we can do it, right?

If you really want to use the bot for a wicked purpose, you can automate and daily run the bot by uploading the scripts we just code to some platform like Heroku or Openshift.





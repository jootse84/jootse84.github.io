This is just for fun.

I am going to introduce how we can easily build a basic Twitter bot with basic Python and the use of Twitter's API.

We are going to be inspired by the account [The Alot Shepherd](https://twitter.com/TheAlotShepherd/), a bot that searches for tweets including the typo 'alot' and reply to them with a [pre-defined image of the Alot Shepherd in the answer](https://twicopy.org/TheAlotShepherd/).

![Alot of Fun](http://i.imgur.com/COEpa.png)

# How to start?

First of all we need to get familiar with [Tweepy](http://tweepy.readthedocs.io/en/v3.5.0/), an easy to use Python library for accessing to the Twitter API. We need to install it in our machine:

```python
> pip install tweepy
```

Then, we have to create a new application by visiting our [Twitter developer page](https://apps.twitter.com/). Notice that you must add your mobile phone to your Twitter profile to be able to create the application.

The steps are pretty simple and straightforward: click the button "Create a new app", fill and confirm the form with the information about your app, click on "manage keys and access tokens" under *Application Settings* section, and finally click the button "Create my access token".

# Time to code

Our project structure will only contain the two following files:

- *keys.py* where we are going to save the consumer and access key tokens.
- *bot.py* where we are going to code the core of our application - bot.

To begin and finish with *keys.py*, we just need to copy from our application page all the key values and paste them in a dictionary.

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

For *bot.py*, we need first to provide a new Tweepy Open Authentication class with our consumer and access keys.

After, we need to initialize our Tweppy api module with the new *OAuthHandler* object - just a quick reminder, in Python classes are denoted by capitalized names while modules are denoted by all-lowercase names.

```python
#!/usr/bin/python
# bot.py

import tweepy

from keys import keys

auth = tweepy.OAuthHandler(keys['consumer_key'], keys['consumer_secret'])
auth.set_access_token(keys['access_token'], keys['access_token_secret'])

api = tweepy.API(api)
```

# Final steps

I suggest to create a fake Twitter account in order to avoid the block of our personal one.

For this example I named 'Cani' a fake Twitter account I created with the id [@Cani92286899](https://twitter.com/Cani92286899/with_replies).

Just for clarification, a Spanish [cani](https://www.quora.com/Whats-the-meaning-of-Spanish-cani-choni-etc) is a *young male who are notorious for wearing sports clothing, outlandish jewelry and accesories, proudly and overtly displaying their lack of manners and education, as well as being fans of Reggaeton music*.

I made this fake account, as I pretend to be the one that will be used to reply tweets that contains the exact sentence 'eres un cani' ('you are a cani').

The following code it is the responsible of retrieving a list of twenty tweets in Spanish containing that sentence.

```python
query = '"eres un cani"'

tweet_list = api.search(
    q=query,
    count=20,
    lang='es'
)
```

Now we can go through every tweet from the list and reply every single tweet with any message we want. For this example I decided to reply with the text '@username yo tambien soy cani!' ('@username I am also a cani!'). The update_status call is the one responsible to generate the response to the message.

As the program can raise an exception if we constatly publish response tweets to users (because of Twitter restrictions), we need to use the try/except block to avoid program hang or freeze.

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

Now it is time to run our script and print our messages:

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

Sweet, right?

Last but not least, if you really want to use the bot for a wicked purpose you can automate and daily run the bot by uploading the scripts we just code to some platform like Heroku or Openshift.





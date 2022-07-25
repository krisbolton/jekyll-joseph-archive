---
layout: post
title: Twitter Bot Automation Without Using the API
description: This post briefly explores the creation of tiwtter bots which don't use Twitter's API and appear to be normal users...
date:   2022-02-28 15:57:00 +0000
tags:   [twitter, bots, disinformation]
---

<style>p { text-align: justify; }</style>

This post briefly explores the creation of tiwtter bots which don't use Twitter's API and appear to be normal users. My motivation is to understand the practical side of disinformation and user emulation.

### Bots without using Twitter's API

Why can't we use Twitter's API? Nafarious individuals choose not to use Twitter's API due to the various restrictions (e.g., rate limiting) and the presumed ability to allow the detection of bots. Twitter can collect statistics on the use of API calls, generate behavioural profiles and presumably detect bots this way. More obvious bot detection methods, such as CAPCHA, the speed at which information is submitted into forms, and similar are other ways Twitter certainly does attempt to detect and disuade the use of bots.

### User emulation

How can we interact with a web page without using a websites official API? User emulation! Developers of web applications need a method to simulate the use of their application for testing and quality assurance, mature test libraries exist for this purpose, <a href="https://www.selenium.dev/">Selenium</a> is one well-known example. We can use Selenium to create an emulated Twitter user (a bot) which does not need to use Twitter's API. We'll be using <a href="https://pypi.org/project/selenium/">Selenium's python package</a> from PyPi.

### A simple python script

Below is a python script which uses Selenium to emulate a user selecting the username form field, entering a value, pressing enter (to laod the next part of the login page), selecting the password form field, entering a value and pressing enter to successfully login to Twitter. In this example, the method is flummoxed by the need for a 2FA code. I use 2FA on my personal Twitter so this may not be an issue on your account.

You can also find the script on <a href="https://github.com/krisbolton/twitter-user-emulation">GitHub</a>.

**Warning**: Do not run the script against your account more than 3 times -- create a new account. Twitter may lock or deactivate your account.

The script was tested in an IDE's (PyCharm) virtualenv, so it may need tweaks depending on your setup.

```python
import time
import chromedriver_autoinstaller

from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.by import By


def login(username, password):
    """
    A simple function which logs a user into Twitter using Selenium.
    Specifcally desigend as an experiment for use within an IDE.

    Parameters
    ----------
    username : str
        Twitter username of an existing account.
    password : str
        Twitter password of an existing account.
    """

    chromedriver_autoinstaller.install()
    # Automatically install chromedriver, avoiding need to tinker.
    driver = webdriver.Chrome()
    driver.get("https://twitter.com/i/flow/login")
    # Get login page using Selenium
    time.sleep(5)
    # Sleep to allow page to load

    username_field = driver.find_element(By.XPATH, "//*[@id=\"layers\"]/div/div/div/div/div/div/div[2]/div[2]/div/div/div[2]/div[2]/div[1]/div/div[5]/label")
    # Assign xpath of username form field to variable
    username_field.send_keys(username)
    # Enter the value passed as username into field
    time.sleep(1)
    # Sleep for 1sec so you can see it enter your username
    username_field.send_keys(Keys.RETURN)
    # Automated enter key press
    time.sleep(5)
    # Sleep again for 5 so password page can load

    password_field = driver.find_element(By.XPATH, "//*[@id=\"layers\"]/div/div/div/div/div/div/div[2]/div[2]/div/div/div[2]/div[2]/div[1]/div/div[3]/div/label")
    # Assign xpath of password form field to variable
    password_field.send_keys(password)
    # Enter the value passed as password into field
    time.sleep(1)
    # Sleep for 1sec so you can see it enter password
    password_field.send_keys(Keys.RETURN)
    # Automated enter key press
    time.sleep(10)
    # Sleep for 10 sec so it doesn't close the page on submission


if __name__ == "__main__":
    # Enter your username and password below
    login('USERNAME','PASSWORD')

```


## Conclusion

Although we didn't explore disinformation directly in this post, we did use one tool which can be used to enable the interaciton of bots with web apps like Twitter. We bypassed the need to use official APIs and their limitations, and while very early stages we could use these same techniques to automatically post content to Twitter.

I hope you found this interesting. You can follow me on Twitter at <a href="https://twitter.com/KrisBolton">@krisbolton</a>.

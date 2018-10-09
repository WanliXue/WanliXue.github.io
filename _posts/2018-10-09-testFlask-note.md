---
layout: post
title: "Flask note"
date: 2018-10-10
---

was inspired by a very interesting web-based [CHINESE dos game site](https://github.com/WanliXue/chinese-dos-games).

Had a look about [Flask](https://palletsprojects.com/p/flask/)

pip install Flask   done
but the simply test hello.py dosent work and showed cannot import the library
check the pip freeze it there.
I reckon it is about pythonpath, (use pycharm IDE a lot and everything can be done by simple click.)
fix:

get the directory of flask : pip show flask
Location: /usr/local/lib/python2.7/site-packages

 cd ~

nano .bash_profile

add export PYTHONPATH=/Library/Frameworks/Python.framework/Versions/2.7/bin/python2

save and exit, and  source .bash_profile 

worked.
$ python hello.py
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
127.0.0.1 - - [09/Oct/2018 15:04:12] "GET / HTTP/1.1" 200 -
127.0.0.1 - - [09/Oct/2018 15:04:13] "GET /favicon.ico HTTP/1.1" 404 -

It is lightwieght server? interesting

Fork Notes
--------
Forked to fix an issue with the shareable token links, as all URLs not pointing to the base domain lead to a "500 Internal Server Error" page when deployed via cPanel's Application Manager (which uses Passenger). 

This fork fixes this by inserting the token into the URL as a query string, which fixes the issue. 

Installation on WHM + cPanel + Apache
--------
1. On cPanel, open Files -> Git Version Control -> Create

2. Create a Repository with these settings: 
    Clone a Repository:     ON
    Clone URL:              https://github.com/mdelecate/snappass.git
    Repository Path:        public_html/snappass/repo
    Repository Name:        SnapPass
    Click Create
    
3. Open Software -> Application Manager -> Register Application 

4. Deploy the following Application: 
    Application Name:       SnapPass
    Deployment Domain:      Pick your preferred domain
    Base Application URL:   /
    Application Path:       public_html/snappass/repo/snappass
    Deployment Environment: Production
    
    Environment Variables -> Add Variable: 
    Variable Name:          SECRET_KEY
    Value:                  Input a random string characters (use a long string created by a random key/password generator)
    Click Deploy
    
5. In the Application Manager, for the new SnapPass app, click "Ensure dependencies" to install all required software. 

Done! Your app is now available on your preferred domain. 

If there are problems, toggle Deployment Environment to Development to increase your chance of seeing helpful error messages. 

It's strongly recommended to use a domain with an SSL certificate. WHM makes this easy with AutoSSL and Let's Encrypt. 
It's strongly recommended to force a secure connection by going to cPanel -> Domains -> Force HTTPS Redirect. 

/Fork Notes

-----------------


========
SnapPass
========

|pypi| |build|

.. |pypi| image:: https://img.shields.io/pypi/v/snappass.svg
    :target: https://pypi.python.org/pypi/snappass
    :alt: Latest version released on PyPI

.. |build| image:: https://travis-ci.org/pinterest/snappass.svg
    :target: https://travis-ci.org/pinterest/snappass
    :alt: Build status

It's like SnapChat... for passwords.

This is a web app that lets you share passwords securely.

Let's say you have a password.  You want to give it to your coworker, Jane.
You could email it to her, but then it's in her email, which might be backed up,
and probably is in some storage device controlled by the NSA.

You could send it to her over chat, but chances are Jane logs all her messages
because she uses Google Hangouts Chat, and Google Hangouts Chat might log everything.

You could write it down, but you can't find a pen, and there's way too many
characters because your security person, Paul, is paranoid.

So we built SnapPass.  It's not that complicated, it does one thing.  If
Jane gets a link to the password and never looks at it, the password goes away.
If the NSA gets a hold of the link, and they look at the password... well they
have the password.  Also, Jane can't get the password, but now Jane knows that
not only is someone looking in her email, they are clicking on links.

Anyway, this took us very little time to write, but we figure we'd save you the
trouble of writing it yourself, because maybe you are busy and have other things
to do.  Enjoy.

Security
--------

Passwords are encrypted using `Fernet`_ symmetric encryption, from the `cryptography`_ library.
A random unique key is generated for each password, and is never stored;
it is rather sent as part of the password link.
This means that even if someone has access to the Redis store, the passwords are still safe.

.. _Fernet: https://cryptography.io/en/latest/fernet/
.. _cryptography: https://cryptography.io/en/latest/

Requirements
------------

* Redis
* Python 2.7+ or 3.4+ (both included)

Installation
------------

::

    $ pip install snappass
    $ snappass
    * Running on http://0.0.0.0:5000/
    * Restarting with reloader

Configuration
-------------

You can configure the following via environment variables.

``SECRET_KEY``: unique key that's used to sign key. This should
be kept secret.  See the `Flask Documentation`__ for more information.

.. __: http://flask.pocoo.org/docs/quickstart/#sessions

``DEBUG``: to run Flask web server in debug mode.  See the `Flask Documentation`__ for more information.

.. __: http://flask.pocoo.org/docs/quickstart/#debug-mode

``STATIC_URL``: this should be the location of your static assets.  You might not
need to change this.

``NO_SSL``: if you are not using SSL.

``URL_PREFIX``: useful when running snappass behind a reverse proxy like `nginx`. Example: ``"/some/path/"``, Defaults to ``None``

``REDIS_HOST``: this should be set by Redis, but you can override it if you want. Defaults to ``"localhost"``

``REDIS_PORT``: is the port redis is serving on, defaults to 6379

``SNAPPASS_REDIS_DB``: is the database that you want to use on this redis server. Defaults to db 0

``REDIS_URL``: (optional) will be used instead of ``REDIS_HOST``, ``REDIS_PORT``, and ``SNAPPASS_REDIS_DB`` to configure the Redis client object. For example: redis://username:password@localhost:6379/0

``REDIS_PREFIX``: (optional, defaults to ``"snappass"``) prefix used on redis keys to prevent collisions with other potential clients

Docker
------

Alternatively, you can use `Docker`_ and `Docker Compose`_ to install and run SnapPass:

.. _Docker: https://www.docker.com/
.. _Docker Compose: https://docs.docker.com/compose/

::

    $ docker-compose up -d

This will pull all dependencies, i.e. Redis and appropriate Python version (3.7), then start up SnapPass and Redis server. SnapPass server is accessible at: http://localhost:5000

Similar Tools
-------------

- `Snappass.NET <https://github.com/generateui/Snappass.NET>`_ is a .NET
  (ASP.NET Core) port of SnapPass.


We're Hiring!
-------------

Are you really excited about open-source and great software engineering?
`Pinterest is hiring <https://careers.pinterest.com>`_!

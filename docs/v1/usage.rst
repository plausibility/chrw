APIv1 Wrapper Usage
===================

It's fairly simple to use the wrapper, so here's how you do it.

If you want a more thorough run down of any of the API calls,
visit the `chr docs <http://chr.rtfd.org>`_, and read the API section.

Initialising
------------

It's fairly easy to setup a wrapper instance, but you will need some things:

- The URL to the chr instance (in this case we'll use: ``chr.so``)
- Your API key (in this case, it's just: ``...``)

Here's how you set up your wrapper:

.. code-block:: python

    >>># Obviously v1 is for APIv1
    >>> import chrw.v1

    >>> # chr.so is our url, ... is our API key
    >>> w = chrw.v1.wrapper("chr.so", "...")

Shortening
----------

Shortening URLs can be done with a call to the :func:`chrw.v1.wrapper.shorten` method.

.. code-block:: python

    >>> reply = w.shorten("http://foo-bar.example.com/baz", custom="foo", give_delete=True)
    >>> pprint.pprint(reply)
    {u'delete': u'XXXXXXXXXX',
     u'enum': 0,
     u'error': u'false',
     u'given': u'http://foo-bar.example.com/baz',
     u'message': u'Successfully shrunk your URL!',
     u'short': u'+foo',
     u'url': u'http://chr.so/+foo'}

Deletion
--------
If you want to delete a URL, you'll need to call :func:`chrw.v1.wrapper.delete` with the
deletion key you got when you made the URL.

.. code-block:: python

    >>> reply = w.delete("+foo", "XXXXXXXXXX")
    >>> pprint.pprint(reply)
    {u'enum': 0,
     u'error': u'false',
     u'message': u'Successfully deleted http://chr.so/+foo'}

Statistics
----------
Pulling up information about a given URL could not be easier than calling :func:`chrw.v1.wrapper.stats`.

.. code-block:: python

    >>> reply = w.stats("+foo")
    >>> pprint.pprint(reply)
    {u'clicks': {u'browsers': {u'unknown': 0},
                 u'pd': {u'01/08': 0,
                         u'01/09': 0,
                         ...
                         u'02/05': 0,
                         u'02/06': 0},
                 u'platforms': {u'unknown': 0}},
     u'enum': 0,
     u'error': u'false',
     u'hits': {u'all': 0, u'ratio': u'0', u'return': 0, u'unique': 1},
     u'long': u'http://foo-bar.example.com/baz',
     u'long_clip': u'http://foo-bar.example.com/baz',
     u'message': u'Here are your stats, piping hot from the oven!',
     u'short': u'+foo',
     u'short_url': u'http://chr.so/+foo'}

This simply tells us that there hasn't been any clicks yet. Not a single damn one. :(

Expansion
---------
If you have a shortened URL you'd like info on, you can simply call :func:`chrw.v1.wrapper.expand`,
and all of your questions will be answered.

.. code-block:: python

    >>> reply = w.expand("+foo")
    >>> pprint.pprint(reply)
    {u'enum': 0,
     u'error': u'false',
     u'long': u'http://foo-bar.example.com/baz',
     u'message': u"Here's the expanded URL",
     u'short': u'+foo'}

Exceptions
----------
At some point in time, you're bound to come across some exceptions.

The API is turned off, your API key is nolonger valid, you gave a bad URL; it could be anything!

These are easy to handle with ``try: ... except`` clauses in Python,
and you can find the exception list at :mod:`chrw.exceptions`.

A quick example, which involves catching :py:exc:`chrw.exceptions.RequestRateTooHigh`.

.. code-block:: python

    >>> import chrw.v1
    >>> w = chrw.v1.wrapper("chr.so", "...")
    >>> try:
    ...     for x in xrange(0, 10):
    ...         reply = w.shorten("http://some-damn-website.com/{0}".format(x))
    ... except chrw.exceptions.RequestRateTooHigh:
    ...     print "Girl, you requestin' too darn fast!"
    ...     print "Slow yo' self down!"
    ...     raise
    ...
    Girl, you requestin' too darn fast!
    Slow yo' self down!
    Traceback (most recent call last):
      File "<stdin>", line 3, in <module>
      File "/home/chris/github/chrw/chrw/v1.py", line 93, in shorten
        "delete": "true" if give_delete else ""
      File "/home/chris/github/chrw/chrw/v1.py", line 33, in rate_limited_function
        raise chrw.exceptions.RequestRateTooHigh, msg
    chrw.exceptions.RequestRateTooHigh: Last request was 0:00:00.000016, should be at least 0:00:01
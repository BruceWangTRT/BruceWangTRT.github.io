---
categories: [python]
tags: [python, logging]
---

# Python logging 00 introduction

There is a logging module in Python standard library.
And the Python org has great documemtations about it
[here](https://docs.python.org/3/library/logging.html).
This series of posts are mostly my digest of those docs.

To give you an idea of how it looks like,
let's have a look at an example first:

```python
>>>import logging
>>>logger = logging.getLogger("some_name")
>>>logger.warning("some message")
some message
```

Here we import logging module from the standard library,
then we get an instance of logger and lastly we log some
message at warning level with that logger.

And that is how to log with standard library in its simplest form.

Eventually, you will get to know more about the whole module
and can easily understand this flow chart:
![](/images/python_stdlib_logging.png "logging flow chart")

We will learn more about that in the next post.